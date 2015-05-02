---
layout: post
title:  "Generating Projects with Premake"
date:   2015-05-02 02:00:00
categories: premake build
---
![My helpful screenshot](/assets/Premake.jpg)

There is a goal - a cross-platform build system. It still doesn't exist, and someday I'll be
forced to make it. Until then, I keep trying things. One of those things is Premake.

Premake is a cross-platform project generation system. So, while it's not a build system, it
handles one of the hard parts, code. It uses a Lua scripting system; your build definition
files are Lua scripts with lots of predefined behavior handled by the Premake engine. So, like
SCons, you start off on familiar ground with a powerful language that has widespread usage.

Unlike SCons, Premake is a C application. There are some positives - C code can run on almost
any system. But, you need to bootstrap Premake, because (of course) Premake is built with
Premake. This is only annoying when you start using Premake, then, like any self-hosting system,
you forget there was a time before self-compiling.

Premake had a big re-architecture pass happen in 2014, and (as of this writing in May 2015) is
just now starting to shape up. The original developer, Kitware, had a strong Visual Studio bias,
which shows not just in the features in Premake, but that Xcode support is only just now
re-emerging. That said, Premake 5 is what you should be using.

So, how do you get started with Premake?

## Bootstrapping

Prebuilt Premake binaries can be found on the [Premake Download][premake-download] site.

Grab binaries as needed; even if you want to be building from source (e.g. adding features to
Premake), you'll need at least one existing Premake version. Fortunately, Premake 5 can be
bootstrapped with Premake 4. When I went to grab a build in late April, the built Windows binary
was broken, but a nightly build worked.

I'm going grab a latest [Windows build][premake-daily-windows], and put the Premake binary in
```C:\Dev\premake5\premake5.exe```.

## Premake source

Premake source, along with the online manual, has moved to GitHub; see [Premake on GitHub][premake-github].
It's very easy to build - clone the repo, run an existing premake, and then build from the generated
project:

{% highlight text %}
> git clone git@github:premake/premake-core.git
> cd premake-core.git
> C:\Dev\premake5\premake5.exe vs2013
Building configurations...
Running action 'vs2013'...
Generating Premake5.sln...
Generating Premake5.vcxproj...
Generating Premake5.vcxproj.filters...
Done.
{% endhighlight %}

At this point, there is a Visual Studio 2013 solution in the root folder that you can open and
build, or use msbuild from the command-line to build from it. And once you start using Premake,
you'll wonder why project files aren't tucked away in a folder so that multiple .gitignore
statements aren't required to handle all possibilities. We put generated projects in the build
folder, because that's where all generated artifacts go.

If you have never built Premake from the given repo before, or you've changed the Lua scripts,
there's one final step to take, which is to embed the scripts in the executable. This copies
all the Lua scripts into a script.c file that's then part of the binary.

{% highlight text %}
>c:\dev\premake5\premake5.exe embed
Running action 'embed'...
Writing scripts.c
{% endhighlight %}

Premake will load the loose scripts from disk if they exist, and since a lot of the Premake
functionality is actually in the Lua scripts, this makes it easy to iterate quickly when adding
or updating Premake functionality. But in normal practice, it's just the single binary that's
distributed, hence the need for the embedding step.

[premake-download]: http://premake.github.io/download.html
[premake-daily-windows]: http://sourceforge.net/projects/premake/files/Premake/nightlies/premake-dev-windows.zip/download
[premake-github]: https://github.com/premake/premake-core
