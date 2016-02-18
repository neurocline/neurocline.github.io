---
layout: post
title:  "Minimal Premake"
date:   2016-02-17 17:00:00
categories: dev
tags: premake, build
---

This is part of a planned series of articles, discussing both usage of Premake and analysis of it (what is good
about Premake, what is bad), as well as usage and analysis of equivalent tools like CMake, SCons and Gyp.

Premake is useful, and Premake 5 more so. With any tool, it's important to understand it. But it's also important
to know how to use it properly. The apocryphal legend about ```make``` is that there is only one original
```Makefile```, and all others are just edited copies of the ur-file. While not true in fact, it is true that
```make``` is complex and most people who use it don't really understand what they are doing.

If you don't have a ```premake``` binary on your system and in your path, grab a copy from here:
[https://premake.github.io/download.html](https://premake.github.io/download.html). I strongly suggest Premake 5.0, which while
listed as alpha is still stable (Blizzard has been using it for the past 6 months). The examples here start
with Visual Studio, and then move on to XCode and Gmake.

## Bare skeleton

Let's look at Premake through the filter of "what is the simplest Premake file we can make?". We're going to
use it to build a single-file project. Here's the layout for our working folder:

{% highlight text %}
test/
|-- premake-minimal.lua
|-- src/
    |-- main.cpp
{% endhighlight %}

Our ```main.cpp``` file looks like this:

{% highlight c++ %}
#include <iostream>

int main(int argc, char* argv[])
{
  std::cout << "Hello\n";
  return 0;
}
{% endhighlight %}

This is the smallest ```premake-minimal``` file; every line in here is necessary, as far as I can tell:

{% highlight lua %}
solution "mysolution"
    configurations { "Debug" }
    project "myproject"
        kind "ConsoleApp"
        files { "src/main.cpp" }
{% endhighlight %}

First off, indentation in Premake scripts is arbitrary; Premake scripts are just Lua scripts with predefined
behavior, and Lua scripts are agnostic to whitespace (think C, not Python). The indentation is chosen to
show meaning and ownership, but to Premake, a file with none of the above indentation would mean the same thing.
This is important to keep in mind for future features like ```filter```.

All Premake scripts declare a single solution.

{% highlight lua %}
solution "mysolution"
{% endhighlight %}

The word ```solution``` comes from its original Visual Studio
focus; supposedly, newer Premake versions are now preferring ```workspace```, with ```solution``` as a synonym.
The name you give your solution is, while arbitrary, also practical: this is the name given to the generated
solution/workspace/makefile, and (where relevant), is also the name that shows up when you open your solution.

Your Premake scripts must have one or more ```configurations```; Premake has no defaults.

{% highlight lua %}
configurations { "Debug" }
{% endhighlight %}

Configurations are sets of build options that
can cut across targets. The name configuration comes from Visual Studio; Xcode also calls them configurations,
although I think that might be recent. In makefiles, configurations are a Premake-made-up concept.

A solution contains one or more projects.

{% highlight lua %}
project "myproject"
{% endhighlight %}

Likewise, the word ```project``` shows Visual Studio bias, but is a reasonable concept (for Visual Studio builds,
each ```project``` section generates a corresponding .vcxproj file); other build systems might
call this a target. Like with solution, the name you give a project is both arbitrary and practical; any generated
project file (just for Visual Studio, as far as I know) will have this name, and when open in an editor, the project
will present this name.

A project need a ```kind```, which declares what type of thing to build:
choices are ```StaticLib``` for
static libraries, ```SharedLib``` for shared libraries (called DLLs on Windows), ```ConsoleApp``` for console applications,
and ```WindowedApp``` for apps that run in a window (mostly a distinction for Windows and Mac OS X). Premake is
used for building code projects, and this shows up in the set of items for kind.

Finally, a project has to reference files, or it does nothing. This is the one piece that is optional, we
could have made a project that has no source; but there's little point to this. Note that we reference files relative to
where the premake script is found. In this case, we just have the single file ```src/main.cpp```, but this could be
an entire comma-separated list of files.

Use premake to generate make system files from this script; the parameter ```--file=<PREMAKE-SCRIPT>``` is optional
if your premake script has the default name, which for Premake 5 is ```premake5.lua```.

{% highlight bash %}
C:\test> premake5.exe --file=premake-minimal.lua vs2013
{% endhighlight %}

This will create a Visual Studio 2013 project that you can open and compile. After building, you should see
output something like this:

{% highlight text %}
test/
|-- bin/
    |-- Debug/
        |-- myproject.exe
        |-- myproject.ilk
|-- myproject.vcxproj
|-- mysolution.sdf
|-- mysolution.sln
|-- mysolution.v12.suo
|-- obj/
    |-- myproject.tlog/
    |-- myproject.log
    |-- main.obj
    |-- vc120.pdb
|-- premake-minimal.lua
|-- src/
    |-- main.cpp
{% endhighlight %}

This will look slightly different if you use a different version of Visual Studio.

It's a little messy, but Premake has imposed some structure without us asking. In general, Premake tries to have
sensible defaults while letting you override them if you want or need to.

## Simple organization

Of course, you would never make even a simple project this way. So what should we add?

First, we can control where the generated project files go. The Premake directive is ```location (<PATH>)```; this
sets the base location for all generated output. Let's place our generated project files in a ```build``` folder
(foreshadowing: unless we specify otherwise, all compiler outputs will go here too).

So we update our premake script to put generated files in this location:

{% highlight lua %}
solution "mysolution"
    configurations { "Debug" }
    location ("build")

    project "myproject"
        kind "ConsoleApp"
        files { "src/main.cpp" }
{% endhighlight %}

and (after throwing away all previous artifacts so we don't confuse ourselves), re-generate:

{% highlight bash %}
C:\test> premake5.exe --file=premake-minimal.lua vs2013
{% endhighlight %}

After running this, you'll see a ```build/``` directory with files in it; I won't list those files again, our
previous list suffices.

{% highlight text %}
test/
|-- build/
|-- premake-minimal.lua
|-- src/
    |-- main.cpp
{% endhighlight %}

As well as Premake-generated files, all the files that Visual Studio creates are now tucked away in this build folder.
A big advantage to doing this is that all build artifacts
are in a single folder that can be ignored when committing to source control, and can be removed in order to
facilitate a clean build.

## Multiple configurations

If you try to debug with this project in Visual Studio, you'll see Visual Studio complaining that it can't find
the debug symbols for your program. Premake by default does not create projects that generate debug symbols. Let's fix
that. The premake directive for this is ```flags { "Symbols" }```. The ```flags``` keyword sets some Premake-specific
values. The ```"Symbols"``` flag means "set up project to generate debug symbols".

{% highlight lua %}
solution "mysolution"
    configurations { "Debug" }
    location ("build")

    project "myproject"
        kind "ConsoleApp"
        files { "src/main.cpp" }
        flags { "Symbols" }
{% endhighlight %}

Now, when we re-generate (throwing away our old ```build/``` directory is not strictly necessary) and rebuild,
we'll have debug symbols in our project.

We can add a Release configuration easily enough. Let's also set it up so that the Debug configuration generates debug
symbols, but the Release configuration does not. Also, we want the Release configuration to generated optimized code.

{% highlight lua %}
solution "mysolution"
    configurations { "Debug", "Release" }
    location ("build")

    project "myproject"
        kind "ConsoleApp"
        files { "src/main.cpp" }

        filter "configurations:Debug"
            defines { "_DEBUG" }    
            flags { "Symbols" }
        filter "configurations:Release"
            defines { "NDEBUG" }    
            optimize "On"
        filter {}
{% endhighlight %}

This introduces a new Premake feature called ```filter```; any settings that appear after ```filter``` will only be
applied to the subset of builds that match the keywords in the filter. Our first filter, ```configurations:Debug```,
means that subsequent lines only apply to configuration ```Debug```. The ```defines``` directive introduces a C
preprocessor define ```_DEBUG``` into our debug configuration, and then the ```flags { "Symbols" }``` directive turns
on generating of debug symbols just for our debug configuration.

Filters stay in effect until removed or replaced. The way to remove a filter is to just "apply" the empty filter. At the
end of our block, you'll see ```filter {}```. In our very short premake script, this is not needed, but it's a good
practice to follow, because it will be essential in large premake scripts, especially ones that include other scripts.
The best way to visualize this is to remove all the indentation from your premake script. Now it's clear that filters
are in effect until removed; the indentation is fooling your brain into thinking there is some sort of scope.

Filters can replace other filters. That's why we don't bother resetting the filter before applying our second filter,
```filter "configuration:Release"```. This just replaces the debug filter. And like before, our ```NDEBUG``` symbol
is only defined for the release configuration. The directive ```optimize "On"``` is how you tell Premake to do whatever
it takes to configure for optimized builds. This is the cross-platform and generic way; more complex projects may
wish to directly issue compiler flags, but that requires per-build-platform sections. We'll get there eventually, but
not just yet.

If you regenerate and inspect the generated project files, you'll see that we now have two solution configurations,
Debug and Release; debug has debug symbols and does not have optimized code, whereas release does not have debug
symbols but has optimized code.

## Multiple source files

The final thing we're going to cover in our minimal Premake project is on specifying source files. While it is
possible to list every file in our ```files``` directive, we can use wildcards, including recursing through
multiple layers of directories:

{% highlight lua %}
files { "**.cpp", "**.h", "**.c" }
{% endhighlight %}

The new files line will add any .cpp, .h, or .c files found anywhere in the ```src/``` directory. By default, Premake
will try to be nice and will create groupings in the generated projects to match the directory structure. As long as
you are disciplined and don't keep stray files in your workspace, you may find you never need explicit listing of files
in the premake script. Note that the first level isn't replicated in the project file - ```main.cpp``` shows up at the top
level, and not inside a ```src``` group. If you add more files and directories to your ```src``` folder, you'll see
those files put into groups named to match the directories.

So, that's it. There are the basics of Premake that will serve you for small to medium projects. You'll find that it's
far easier to write a quick Premake script than to manually construct a Visual Studio project. More importantly, this
also removes one small but important barrier to being cross-platform. Let's copy our source files to a Mac, and grab
a Mac version of the premake binary.

{% highlight bash %}
test$ premake5 --file=premake-minimal.lua xcode4
{% endhighlight %}

Success! We have a build/mine.xcodeproj that we can build and debug and run. And this is why people like using
Premake (or CMake, or Scons, or Gyp), because there are many projects that are cross-platform.

And, for fun, we can generate a makefile project that is consumed by GNU Make (gmake). We'll do this on the Mac,
although with a little tweaking, you could turn it into a makefile that Windows NMake could consume.

{% highlight bash %}
test$ premake5 --file=premake-minimal.lua gmake
{% endhighlight %}

And, if we look in our ```build/``` directory, we'll see a makefile named ```Makefile```.

## Identifying boilerplate

Boilerplate is a term that means "stuff I copy over and over again just to make the system happy". In
general, we want a system that is as concise as possible, where everything I add is necessary and
unique to my project. This has to be balanced against the needs of syntax and semantics. Keywords like
"solution" aren't boilerplate if they are necessary to declare some information that is needed.

Looking at our minimal project, there is some boilerplate related to how we define debug and release
builds. For example:

{% highlight lua %}
defines { "NDEBUG" }    
{% endhighlight %}

Every C/C++ project I make that uses the standard C library needs to have ```NDEBUG``` defined for
non-debug builds. And Microsoft long ago introduced a parallel construction, every debug build should
have ```_DEBUG``` defined (I suppose they didn't like ```#ifndef NDEBUG``` as a guard).

This is right on the edge of boilerplate, because Premake doesn't have any idea of predefined meaning
for configurations, so how does Premake now that my config is debug or release? However, and also
looking at symbols and optimization, there are common idioms. Maybe we should have been able to
introduce all of them at once. This makes sense given that ```optimize "On"``` itself is a collection
of predefined options for what "optimized" means.

One way would have been to attach information to a configuration. Doing it in a filter is one way, but
verbose. What if we just told it when we declared the configurations.

{% highlight lua %}
configurations { ["Debug"] = "debug", ["Release"] = "release" }
{% endhighlight %}

And if the configuration name matches one of our sets (built-in or not), then we wouldn't need to
specify this. In the above, I'm saying that the "debug" set is "#define _DEBUG, build debug symbols",
and the "release" set is "#define NDEBUG, turn full optimization on".

This makes my small script even smaller, but more importantly it gets rid of duplicated code. The lines
above are likely to be seen, in one form or another, in every Premake script ever made. This is
a large part of what leads to copy/paste makefiles; a ton of common operations that need to be done,
so why not just copy it instead of re-write it. It's just that copying is the poor man's code re-use,
because errors and misunderstandings are just as easily propagated as best practices and required
structure.

## Canonical minimal Premake script

Here's a commented version of the minimal premake script, usable as a standalone reference. Because this
is Lua, we can comment it, and we could even compose it as functions (which we would do for a very large
project).

{% highlight lua %}
-- A solution is the top-level entity in Premake. In Visual Studio, this
-- maps to a Visual Studio solution file (.sln). In XCode, this maps
-- to an XCode workspace (.xcworkspace). In make, this is a makefile.
solution "mysolution"

    -- A solution has one or more configurations. The names of the configurations
    -- are arbitrary and are strings that can be matched by filters to customize
    -- behavior. Each project is generated for each possible configuration.
    configurations { "Debug", "Release" }

    -- The location specifies where Premake will put generated files. If not
    -- specified, the default is the directory where the premake script lives.
    -- Non-absolute paths are relative to the premake script (and good practice
    -- would be to avoid absolute paths in project scripts).
    location ("build")

    -- A project is the next-level entity in Premake; solutions contain one
    -- or more projects. While this conceptually belongs to the solution, it
    -- belongs to the solution only because solution is a singleton.
    project "myproject"

        -- A project has a kind, which is the form of the generated output for
        -- this project. There are four kinds: StaticLib, SharedLib, ConsoleApp,
        -- and WindowedApp (the last is unique to Windows and Mac OS X).
        kind "ConsoleApp"

        -- A project has one or more source files in it. These can be enumerated
        -- individually, or specified with wildcards. The '**' wildcard is used
        -- to recurse through directories, not just to match in a single directory.
        files { "**.cpp", "**.h", "**.c" }

        -- filters can be used to apply settings to a subset of the configurations
        -- and architectures. In this case, we are applying some settings to just
        -- the debug configuration.
        filter "configurations:Debug"
            defines { "_DEBUG" }    
            flags { "Symbols" }

        -- In this case, we are applying some settings to just the release
        -- configuration
        filter "configurations:Release"
            defines { "NDEBUG" }    
            optimize "On"

        -- A filter is in effect until the end of the file, or until it is replaced
        -- with another filter. As a good engineering practice, we want to end a
        -- filter as soon as we no longer need it; the way to do this is to say
        -- "filter nothing".
        filter {}
{% endhighlight %}

## Reference

Unfortunately, Premake documentation is now disjoint. There was a major version change, but lots of useful
documentation was orphaned. Concepts were explained better in older documentation, you just need to be careful
because syntax and features have changed.

[Original User Guide](http://industriousone.com/premake/userguide)

[Premake 4.x User Guide](https://github.com/premake/premake-4.x/wiki)

[Premake 5.x User Guide](https://github.com/premake/premake-core/wiki)

Likewise, the original home for Premake development was on the industriousone web site, but is now in GitHub.

[premake on GitHub](https://github.com/premake)

[New Premake home page](http://premake.github.io/)

[industriousone](http://industriousone.com/). However, there are no links to Premake content, but Google searches
will show links to the old forums and help articles.

[Old forums for Premake](http://industriousone.com/forums/premake)

[Google Groups Premake forum](https://groups.google.com/forum/#!forum/premake-development)

[Premake Cookbook](http://industriousone.com/forums/premake/premake-cookbook)
