---
layout: post
title:  "Minimal Premake"
date:   2016-02-17 17:00:00
categories: dev
tags: premake, build
---

Premake is useful, and Premake 5 more so. With any tool, it's important to understand it. But it's also important
to know how to use it properly. The apocryphal legend about ```make``` is that there is only one original
```Makefile```, and all others are just edited copies of the ur-file. While not true in fact, it is true that
```make``` is complex and most people who use it don't really understand what they are doing.

If you don't have a ```premake``` binary on your system and in your path, grab a copy from here:
[https://premake.github.io/download.html](https://premake.github.io/download.html). I strongly suggest Premake 5.0, which while
listed as alpha is still stable (Blizzard has been using it for the past 6 months). The examples here start
with Visual Studio, and then move on to XCode and Gmake.

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
solution "mine"
    configurations { "Debug" }

    project "mine"
        kind "ConsoleApp"
        files { "src/main.cpp" }
{% endhighlight %}

The word ```solution``` comes from its original Visual Studio focus; supposedly, newer Premake versions are now
preferring ```workspace```, with ```solution``` as a synonym.

You must have one or more configurations; Premake has no defaults. Configurations are sets of build options that
can cut across targets.

Likewise, the word ```project``` shows Visual Studio bias, but is a reasonable concept (for Visual Studio builds,
each ```project``` section generates a corresponding .vcxproj file); ther build systems might
call this a target. A project need a ```kind```, which declares what kind of thing is being made:
choices are ```StaticLib``` for
static libraries, ```SharedLib``` for shared libraries (called DLLs on Windows), ```ConsoleApp``` for console applications,
and ```WindowedApp``` for apps that run in a window (mostly a distinction for Windows and Mac OS X).

Finally, a project has to reference files, or it does nothing. This is the one piece that is optional, we
could have made a project that has no source; but there's little point to this. Note that we reference files relative to
where the premake script is found. In this case, we just have the single file ```src/main.cpp```, but this could be
an entire comma-separated list of files.

Use premake to generate make system files from this script; the parameter ```--file=<PREMAKE-SCRIPT>``` is optional
if your premake script has the default name, which for Premake 5 is ```premake5.lua```.

{% highlight bash %}
C:\test> premake5.exe --file=premake-minimal.lua vs2013
{% endhighlight %}

This will create a Visual Studio 2013 project that you can open and compile. Of course, you would never make even
a simple project this way. So what can we add?

First, we can control where the generated project files go. The Premake directive is ```location (<PATH>)```; this
sets the base location for all generated output. Let's create a folder named ```project``` to place
our project files:

{% highlight text %}
test/
|-- premake-minimal.lua
|-- project/
|-- src/
    |-- main.cpp
{% endhighlight %}

and update our premake script to put generated files in this location

{% highlight lua %}
solution "mine"
    configurations { "Debug" }
    location ("project")

    project "mine"
        kind "ConsoleApp"
        files { "src/main.cpp" }
{% endhighlight %}

and (after throwing away all previous artifacts so we don't confuse ourselves), re-generate:

{% highlight bash %}
C:\test> premake5.exe --file=premake-minimal.lua vs2013
{% endhighlight %}

After running this, you'll see some project files in the ```project/``` directory:

{% highlight text %}
test/
|-- premake-minimal.lua
|-- project/
    |-- mine.sln
    |-- mine.vcxproj
|-- src/
    |-- main.cpp
{% endhighlight %}

And, all the generated files that Visual Studio creates are now tucked away in this project folder. In fact, after you
build this project, you'll see that Premake also decided to put all the compiler artifacts in sub-folders in this
folder:

{% highlight text %}
test/
|-- premake-minimal.lua
|-- project/
    |-- bin/
        |-- Debug/
            |-- mine.exe
            |-- mine.ilk
    |-- obj/
        |-- main.obj
        |-- mine.log
        |-- mine.tlog/
        |-- vc120.pdb
    |-- mine.opensdf
    |-- mine.sln
    |-- mine.vcxproj
|-- src/
    |-- main.cpp
{% endhighlight %}

Still, if you try to debug with this project in Visual Studio, you'll see Visual Studio complaining that it can't find
the debug symbols for your program. Premake by default does not create projects that generate debug symbols. Let's fix
that. The premake directive for this is ```flags { "Symbols" }```. The ```flags``` keyword sets some Premake-specific
values. The ```"Symbols"``` flag means "set up project to generate debug symbols".

{% highlight lua %}
solution "mine"
    configurations { "Debug" }
    location ("project")

    project "mine"
        kind "ConsoleApp"
        files { "src/main.cpp" }
        flags { "Symbols" }
{% endhighlight %}

Now, when we re-generate (throwing away our old ```project/``` directory is not strictly necessary) and rebuild,
we'll have debug symbols in our project.

We can add a Release configuration easily enough. Let's also set it up so that the Debug configuration generates debug
symbols, but the Release configuration does not. Also, we want the Release configuration to generated optimized code.

{% highlight lua %}
solution "mine"
    configurations { "Debug", "Release" }
    location ("project")

    project "mine"
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

Filters can replace other filters. That's why we don't bother resetting the filter before applying our second filter,
```filter "configuration:Release"```. This just replaces the debug filter. And like before, our ```NDEBUG``` symbol
is only defined for the release configuration. The directive ```optimize "On"``` is how you tell Premake to do whatever
it takes to configure for optimized builds. This is the cross-platform and generic way; more complex projects may
wish to directly issue compiler flags, but that requires per-build-platform sections. We'll get there eventually, but
not just yet.

If you regenerate and inspect the generated project files, you'll see that we now have two solution configurations,
Debug and Release; debug has debug symbols and does not have optimized code, whereas release does not have debug
symbols but has optimized code.

The final thing we're going to cover in our minimal Premake project is on specifying source files. While it is
possible to list every file in our ```files``` directive, we can use wildcards, including recursing through
multiple layers of directories:

{% highlight lua %}
solution "mine"
    configurations { "Debug", "Release" }
    location ("project")

    project "mine"
        kind "ConsoleApp"
        files { "**.cpp", "**.h", "**.c" }

        filter "configurations:Debug"
            defines { "_DEBUG" }    
            flags { "Symbols" }
        filter "configurations:Release"
            defines { "NDEBUG" }    
            optimize "On"
        filter {}
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

Success! We have a project/mine.xcodeproj that we can build and debug and run. And this is why people like using
Premake (or CMake, or Scons, or Gyp), because there are many projects that are cross-platform.

And, for fun, we can generate a makefile project that is consumed by GNU Make (gmake). We'll do this on the Mac,
although with a little tweaking, you could turn it into a makefile that Windows NMake could consume.

{% highlight bash %}
test$ premake5 --file=premake-minimal.lua gmake
{% endhighlight %}

And, if we look in our ```project/``` directory, we'll see a makefile named ```Makefile```.

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
