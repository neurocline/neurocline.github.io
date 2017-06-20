---
categories: dev
date: 2015-05-02T17:00:00Z
tags:
- premake
- build
title: Generating Projects with Premake
url: /2015/05/02/generating-projects-with-premake/
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

## Building Premake from source

Premake source, along with the online manual, has moved to GitHub; see [Premake on GitHub][premake-github].
It's very easy to build - clone the repo, run an existing premake, and then build from the generated
project:

```
$ git clone git@github:premake/premake-core.git
$ cd premake-core.git
$ premake5.exe vs2013
Building configurations...
Running action 'vs2013'...
Generating Premake5.sln...
Generating Premake5.vcxproj...
Generating Premake5.vcxproj.filters...
Done.
```

At this point, there is a Visual Studio 2013 solution in the root folder that you can open and
build, or use msbuild from the command-line to build from it. And once you start using Premake,
you'll wonder why project files aren't tucked away in a folder so that multiple .gitignore
statements aren't required to handle all possibilities. We put generated projects in the build
folder, because that's where all generated artifacts go.

If you have never built Premake from the given repo before, or you've changed the Lua scripts,
there's one final step to take, which is to embed the scripts in the executable. This copies
all the Lua scripts into a script.c file that's then part of the binary.

```
$ premake5.exe embed
Running action 'embed'...
Writing scripts.c
```

Premake will load the loose scripts from disk if they exist, and since a lot of the Premake
functionality is actually in the Lua scripts, this makes it easy to iterate quickly when adding
or updating Premake functionality. But in normal practice, it's just the single binary that's
distributed, hence the need for the embedding step.

## A simple Premake project

Let's make a Hello World program.

No, that's too simple. Let's create a Visual Studio solution and project file for libgit2 (which uses
CMake).

Get the libgit2 source from GitHub:

```
$ git clone git@github.com:libgit2/libgit2.git
$ cd libgit2.git
```

Now create a file named premake5.lua in the root folder of the libgit2 project, with this as the
contents:

```lua
solution "Libgit2"
    configurations { "Debug" }
    location ("build")

project "libgit2"
    targetname "libgit2"
    language "C"
    kind "StaticLib"
    location ("build/projects")

    files
    {
        "**.c", "**.h"
    }
    
    configuration "Debug"
        targetdir "build/bin/debug"
        defines { "_DEBUG", "WIN32" }
```

Why `premake5.lua`? This is the default name for a premake config script in Premake5. You can
name it anything, but you'll then need to pass that name on the command-line, because Premake5
isn't going to try to guess.

This is not a production-quality premake file. For one, it only targets Windows and Visual Studio,
and one of the main points of using Premake is that it is cross-platform. However, I want to build
up to that, but also to show that you can very quickly generate projects for any codebase.

This won't build, of course. I'm writing it blind without referencing the CMake script. But that's
ok for now.

Run Premake5 to generate a solution:

```
$ premake5.exe vs2013
Building configurations...
Running action 'vs2013'...
Generating build/Libgit2.sln...
Generating build/projects/libgit2.vcxproj...
Generating build/projects/libgit2.vcxproj.filters...
Done.
```

When you run this, you'll see that a build folder has been created, and inside that build folder
is a Visual Studio solution and related project folder. All the source files are in that single
project.

```
$ dir build /s /b
build\Libgit2.sln
build\projects
build\projects\libgit2.vcxproj
build\projects\libgit2.vcxproj.filters
```

If you open the Libgit2.sln project, you'll see it's full of source and header files, arranged in
folders that mimic the source tree layout. This is the default behavior.

If you try building, it will "work" for some degree of work - that is, it will start compiling, but
generate lots of errors because header files can't be found.

Let's fix just the simplest error. The code expects a path to the `include/` folder, because many of
the includes are of the form `#include "git2/common.h"`. Add this section right after the
`files` section in the premake5.lua file:

```lua
includedirs
{
    "../../include"
}
```

Re-run the `premake5.exe vs2013` command. It will overwrite files in place, although if you're
like me, you'll just delete the whole build folder (always safe to do, since it's generated). Now
open the project again.

Important note: Premake generates paths relative to the lua file being executed. Even though
the project file ends up several levels deep in the build folder, your premake5.lua script
describes files relative to itself. So, since the ```include/``` folder is at the same level
as our ```premake5.lua``` file, we can just say ```"include"```. If you look in the project
file, you'll see the actual include paths looks something like this

```
..\..\include;%(AdditionalIncludeDirectories)
```

It's for reasons like this that using Premake even on a single platform can be a boost. If you're
careful, your premake scripts will stay small and readable, and that can't be said for large
programs with dozens of solutions and hundreds of project files. Simply making a small change to
a large hierarchy of projects (say, to add a define) can be time-consuming and error-prone. Yes,
you could attempt to use property sheets in modern versions of Visual Studio, or the equivalent
in other build systems.

Or you can just write your projects in a far more readable form and generate them.

I'll return to Premake in future posts, as I lay out some best practices for using Premake on
both small and large projects. I'll also be looking at the other contenders in this space, and
hypothesizing on what the "perfect" system would look like. For example, Premake5 is very
Windows-centric, and this makes for slightly confusing operation when trying to generate Xcode
projects, because Xcode uses different names for things, and even has slightly different behavior.

More to follow.

[premake-download](http://premake.github.io/download.html)

[premake-daily-windows](http://sourceforge.net/projects/premake/files/Premake/nightlies/premake-dev-windows.zip/download)

[premake-github](https://github.com/premake/premake-core)
