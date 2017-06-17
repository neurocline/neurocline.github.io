---
categories: dev
date: 2017-04-12T19:30:00Z
tags: premake, build
title: Premake notes
url: /2017/04/12/premake-notes/
---

# Basic Premake

So, making more use of Premake. Let's build up from the very minimal to something reasonable,
all the while striving for simplicity and lack of duplication.

## Minimal premake script

This is the minimal Premake script, even if it does nothing:

`00_min.lua`

```lua
workspace "00_min"
configurations { "Debug" }
```

For each solution, and for each configuration, Premake does stuff. Without a solution, there's nothing
to attach it to, and without any configurations, nothing gets done.

Admittedly, if you run this against Premake, you'll get some fairly useless project files.

```
$ premake5 --file=00_min.lua gmake
$ premake5 --file=00_min.lua vs2015
$ premake5 --file=00_min.lua xcode
```

Alas, `premake clean` doesn't work (output says "** The clean action has not yet been ported")

I won't show the generated projects here, but you can run this for yourself and look.

## Almost useful Premake script

Even though projects are really a consequence of IDEs like Visual Studio and XCode, they are required even
if you just build makefile targets. So our real minimal script is this:

`01_min.lua`

```lua
workspace "01_min"
configurations { "Debug" }
project "01_min"
kind "ConsoleApp"
```

This will generate a solution with a single project in it, with a single target, and no files. You must
specify kind. Sometimes I feel like defaults would be useful, but, that said, no real user of Premake
would trust the defaults.

Run this and you can build against these generated projects.

## Scopes (Indentation is an illusion)

Premake scripts are a special kind of Lua script. For the most part, there are no blocks, so there's
no control flow, and indentation is actually a little misleading.

A more human way to write the above is:

```lua
workspace "01_min"
    configurations { "Debug" }

    project "01_min"
        kind "ConsoleApp"
```

And you should do this, but fight the temptation to think that arbitrary scope rules are in force. Instead,
there are three scopes

- global scope
- workspace scope
- project scope

See [Scopes and Inheritance](https://github.com/premake/premake-core/wiki/Scopes-and-Inheritance) in the Premake5
docs for more details.

A side note - you may see `solution` used in Premake scripts - this is an older alias for `workspace`, still supported
in Premake4 but not mentioned in the documentation.

All workspace scopes are in the global scope, and project scopes are in workspace scopes. Each line of the form
`workspace "name"` or `project "name"` selects that scope, creating it if necessary.

You can select the parent of the current project scope with `project "*"` - this selects whatever workspace is
the parent of the current project. There is a hack to get back to global scope, and that is `workspace "*"`.

So, that said, what you really need to pay attention to is that lines in a Premake script are declarations,
and they are added to the active scope. They are not statements in an imperative program. The reason for this
is that, once you have declared your workspaces and projects, Premake iterates through the workspaces,
configurations and projects to actually generate output.

Most declarations are settings; settings in a workspace are inherited by all projects in the workspace. So,
in general, prefer to put workspace-wide settings in the relevant workspace. And if there are truly global
settings, put them in the global scope.

We'll come back to that

## Adding files

Let's have a file to add

`02_main.cpp`

```c++
#include <stdio.h>

int main(int argc, char* argv[])
{
    printf("Hello, world\n");
    return 0;
}
```

and then add it to our project:

`02-min.lua`

```lua
workspace "02_min"
    configurations { "Debug" }

    project "02_min"
        kind "ConsoleApp"
        files { "**.cpp" }
```

and after we generate a project and build it, we can run the output

```
$ bin\Debug\02_min.exe
Hello, world
```

It's common to use wildcards to add files to projects; the `**.cpp` filter means "add files in the current
directory and any sub-directories". In this case, it only matched one file, but if we write it this way,
we don't need to update our Premake script if we add more source files. In fact, for C++, we typically want
this:


```lua
workspace "02_min"
    configurations { "Debug" }

    project "02_min"
        kind "ConsoleApp"
        files { "**.cpp" }
```

We can get a lot done with just this minimal amount of Premake - we could have hundreds of source files in
many directories.

## Cleaning up

We didn't specify where to put output. Premake has defaults for that:

- generated projects go in `.`
- binaries go in `./bin/<target>`
- intermediates go in `./obj/<target>`

where `.` is the location of the Premake script, and not the current working directory. E.g. if we were
in some other directory and ran Premake like this:

```lua
$ premake5 --file=premake-scripts/tests/02_min.lua vs2015
```

then we would see generated projects in `premake-scripts/tests`, and those projects would target locations
in this directory as well.

First, let's control where compiler-generated files go. Premake has a `location` function which sets the
location of generated output, so let's use that to make `bin` and `obj` go in a `build` directory.

```lua
workspace "03_min"
    configurations { "Debug" }
    location "build"

    project "03_min"
        kind "ConsoleApp"
        files { "**.cpp" }
```

This is, alas, where inheritance breaks down. The `location` function is one of the few that aren't inherited
to contained scopes. So in this case, `location` applies to the workspace only and this has an unexpected
behavior - our generated projects go in our `build` directory. The reason that the project ends up in the
build folder is that a contained project defaults to `location "."` which is relative to the enclosing
workspace. Of course, this behaves like it's inherited, so maybe the technical detail should be ignored.

But this still has a useful behavior: we can control where generated projects go. In a Premake world, these
are artifacts just like objects and executables. And with everything in a `build` directory, we just have
one directory to clean up (or not commit to source control).

There are specific functions to set the path for object files and binaries:

```lua
workspace "03_min"
    configurations { "Debug" }
    location "build"

    project "03_min"
        kind "ConsoleApp"
        files { "**.cpp" }
        objdir "debug/obj"
        targetdir "debug"
```

and if we build and run, we'll see workspace and project in `build`, object files in `build/debug/obj`, and binaries
in `build/debug`.

This is where simplicity and maintainability part company. We have hard-coded the configuration name twice; worse,
if we add a new configuration, we'll be directing files to the wrong location. That's because the default
paths for `objdir` and `targetdir` actually look like this:

```lua
objdir "%{wks.location}/obj"
targetdir "%{wks.location}/bin"
```

where the syntax `%{...}` is a [Value Token](https://github.com/premake/premake-core/wiki/Tokens) expression
that is interpolated into the string. In this case, the `wks` object is the workspace.

If we put these into our lua script

`04_min.lua`

```lua
workspace "04_min"
    configurations { "Debug" }
    location "build"

    project "04_min"
        kind "ConsoleApp"
        files { "**.cpp" }
        objdir "%{wks.location}/obj/%{cfg.buildcfg}"
        targetdir "%{wks.location}/bin/%{cfg.buildcfg}"
```

we will see exactly the behavior that we saw before. Of course, you don't want to be more verbose about default
settings; this is just to illustrate how you can access these. When you can access them, you can change them.

Returning back to `location` for a minute, let's set location for both the workspace and the project

```lua
workspace "04_min"
    configurations { "Debug" }
    location "build"

    project "04_min"
        kind "ConsoleApp"
        location "%{wks.location}/prjbuild"
        files { "**.cpp" }
```

If we generate and build, we'll see a workspace/solution file in `build`, a project file in `build/prjbuild`, an
object directory at `build/prjbuild/obj`, and a binaries directory at `build/prjbuild/bin`.

Note something important - all paths in the Premake script are relative to the Premake script's location.

## Debugging

Short of building Premake yourself and using a system that has a Lua debugger, it's hard to introspect the
process of generating project files. There are some tricks that can be employed.

Token expansion happens in Premake script commands. There are a few that can result in inspectable output.

Let's add a text file so we can filter on it

`05_help.txt`

```
Nothing important
```

and we add it to the project along with a filter and a buildmessage

`05_min.lua`

```lua
workspace "05_min"
    configurations { "Debug" }
    location "build"

    project "05_min"
        kind "ConsoleApp"
        location "%{wks.location}/prjbuild"
        files { "**.cpp" }

        filter 'files:**.txt'
            buildmessage 'wks.location = %{wks.location}'
            buildcommands { 'dir' }
            buildoutputs { 'output.i' }
```

This build rule won't actually work, of course. But it will put text into the generated project that we can look at.
Of course, if you do this with a Visual Studio target, many of the Premake variables will be translated into
MSBuild variables. From the Premake source

```lua
    vstudio.pathVars = {
        ["cfg.objdir"]                  = { absolute = true,  token = "$(IntDir)" },
        ["prj.location"]                = { absolute = true,  token = "$(ProjectDir)" },
        ["prj.name"]                    = { absolute = false, token = "$(ProjectName)" },
        ["sln.location"]                = { absolute = true,  token = "$(SolutionDir)" },
        ["sln.name"]                    = { absolute = false, token = "$(SolutionName)" },
        ["wks.location"]                = { absolute = true,  token = "$(SolutionDir)" },
        ["wks.name"]                    = { absolute = false, token = "$(SolutionName)" },
        ["cfg.buildtarget.directory"]   = { absolute = false, token = "$(TargetDir)" },
        ["cfg.buildtarget.name"]        = { absolute = false, token = "$(TargetFileName)" },
        ["cfg.buildtarget.basename"]    = { absolute = false, token = "$(TargetName)" },
        ["file.basename"]               = { absolute = false, token = "%(Filename)" },
        ["file.abspath"]                = { absolute = true,  token = "%(FullPath)" },
        ["file.relpath"]                = { absolute = false, token = "%(Identity)" },
        ["file.path"]                   = { absolute = false, token = "%(Identity)" },
        ["file.directory"]              = { absolute = true,  token = "%(RootDir)%(Directory)" },
        ["file.reldirectory"]           = { absolute = false, token = "%(RelativeDir)" },
        ["file.extension"]              = { absolute = false, token = "%(Extension)" },
    }
```

but makefile targets won't do such translation - they will be relative to the Makefile, of course.
Still, in a pinch, you can see what variables are expanding to, since the documentation isn't all that
clear.
