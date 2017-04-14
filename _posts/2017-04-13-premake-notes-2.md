---
layout: post
title:  "Premake notes (2)"
date:   2017-04-13 12:30:00
categories: dev
tags: premake, build
---

Continuing Premake notes.

## Lua and Premake

The biggest challenge with using Premake in non-trivial ways is that you do need to learn Lua
to some degree. But the advantage of Premake is that at least this is not a new langauge invented
just for the make system. Lua has some quirks of its own, but can be learned quickly by someone
familiar with one or more existing languages.

The most important thing to realize about Premake is that most Premake "configuration" lines
are actually function calls. This:

```
workspace "HelloWorld"
    configurations { "Debug", "Release" }

project "HelloWorld"
    kind "ConsoleApp"
    files { "**.h", "**.c" }
```

is identical to this:

```
workspace("HelloWorld")
    configurations({ "Debug", "Release" })

project("HelloWorld")
    kind("ConsoleApp")
    files({ "**.h", "**.c" })
```

The Premake idiom is to use the sytax for single-parameter function calls to turn Lua into
an IDL, which works for the most part. But you need to be aware of this, or non-trivial scripts
will feel arbitrary to you.

The second most important thing to realize about Premake is that the functions `workspace(), project()` etc
don't do work, they add to a data structure. Once Premake has read all the Lua, it then iterates through
the data structure to actually call functions that generate project files. The only way to do procedural
work is to inject your own Lua code through overrides and call arrays. You can put these inline in your
Premake scripts or as modules, and with modules, you can share these with others as third-party modules
(which unfortunately require the module located somewhere in Premake's search paths)

You can also create actions and toolsets.

## Configurations

To Premake, configurations and platforms are just strings. What you do with them is up to you, and in fact
you have to do some work that's caused by this flexibility.

Let's make a Premake script with two configurations, a debug config and a release config. It will look
something like this:

`06_min.lua`

```lua
workspace "06_min"
    configurations { "Debug", "Release" }
    location "build"

   filter "configurations:Debug"
        defines { "_DEBUG" }
        optimize "Off"
        flags { "Symbols" }

   filter "configurations:Release"
        defines { "NDEBUG" }
        optimize "On"

    filter {}

project "06_min"
    kind "ConsoleApp"
    files { "**.cpp" }
```

Although you have an idea for what a "Debug build" means, Premake has no idea. Nor should it, because in truth,
there is no one true Debug and Release targets.

The down side is that you need to list all the things that aren't default, and it's hard to keep all the defaults
in your head. Listed above are common settings for Debug and Release targets. This is done using `filter`, which
is Premake's way of having settings apply to a subset of the combinations of workspace, project, config, platform,
and so on.

Filters stay in action until replaced with a new filter. Keep in mind that, like other Premake lines, this
is a function call, and all it's doing is attaching this to the active scope (global, workspace, project).
An attached filter accepts or rejects subsequent lines. Filters are removed when the scope changes; you can
also remove the active filter with `filter {}`. Just like with overzealous use of parens or braces, you could
put `filter {}` as an "end", but as you get used to filters, you'll find yourself putting the bare minimum in.

As an aside, the current syntax for symbols is a little goofy and is expecting to change to this:

```
    symbols "Off"
    symbols "On"
```

## Platforms

Configuration is squisy - let's talk about platform. In this case, there sometimes are dependencies between
the architecture and the generated code's "platform" - just don't confuse those with the platform name in
Premake, which is your arbitrary label.

We can do this:

```
workspace "07_min"
    configurations { "Debug", "Release" }
    platforms { "x32", "x64" }
    location "build"

   filter "configurations:Debug"
        defines { "_DEBUG" }
        optimize "Off"
        flags { "Symbols" }
   filter "configurations:Release"
        defines { "NDEBUG" }
        optimize "On"

   filter "platforms:x32"
        architecture "x86"
   filter "platforms:x64"
        architecture "x86_64"

project "07_min"
    kind "ConsoleApp"
    files { "**.cpp" }
```

OK, an editorial comment. Platform has magic behavior in current versions of Premake. If the platform in Premake
is `x32`, then when a Visual Studio project is generated, it has platform `Win32` which means architecture `x86`.
And if the platform in premake is `x64`, then when a Visual Studio project is generated, it has platform `x64`,
which means architecture `x86-64`. If you use other names as platform, they will be handled properly, for the
most part, but it will look odd. So probably don't do it.

## Language and other settings

By default, Premake generates C/C++ projects.

Premake has the `language` keyword, which affects the toolset used by a project. Let's create some
C# and D source files

`08_main.cs`

```C#
public class Hello1
{
   public static void Main()
   {
      System.Console.WriteLine("Hello, World!");
   }
}
```

`08_main.d`

```D
import std.stdio;

void main()
{
    writeln("Hello, World!");
}
```

```lua
workspace "08_min"
    configurations { "Debug", "Release" }
    platforms { "x32", "x64" }
    location "build"

   filter "configurations:Debug"
        defines { "_DEBUG" }
        optimize "Off"
        flags { "Symbols" }
   filter "configurations:Release"
        defines { "NDEBUG" }
        optimize "On"

   filter "platforms:x32"
        architecture "x86"
   filter "platforms:x64"
        architecture "x86_64"

project "08_cpp"
    kind "ConsoleApp"
    language "C++"
    files { "**.cpp" }

project "08_csharp"
    kind "ConsoleApp"
    language "C#"
    files { "**.cs" }

project "08_d"
    kind "ConsoleApp"
    language "D"
    files { "**.d" }
```

And when we build, we will now see three projects: one C++, one C#, and one D. Well, theoretically - I didn't have
the VisualD compiler installed, so I couldn't actually verify that it worked.

## Exploring Premake options

Until the documentation gets better, or someone writes a real book, one way to understand
what will happen is to try it - generate twice, with and without an option, and see what
happens in the generated project.

So let's go back to a barebones config, and see what `flags { "C++14" }` etc do. We'll add
output location handling in our scripts so we can write output to different locations.

`09_min.lua`

```lua
newoption { trigger = "to", value = "path", description = "Set the output location for the generated files" }

workspace "09_min"
    configurations { "Debug" }
    location(_OPTIONS["to"])

project "09_min"
    kind "ConsoleApp"
    files { "**.cpp" }
```

`10_min.lua`

```lua
newoption { trigger = "to", value = "path", description = "Set the output location for the generated files" }

workspace "09_min"
    configurations { "Debug" }
    location(_OPTIONS["to"])
    flags { "C++14" }

project "09_min"
    kind "ConsoleApp"
    files { "**.cpp" }
```

(naming the output the same so we can compare with a diff tool), and then generate:

```
$ premake5 --file=09_min.lua --to=build1 vs2015
$ premake5 --file=10_min.lua --to=build2 vs2015
merge build1 build2
```

Nothing is different on Visual Studio, because at the moment, Premake doesn't do anything with the `flags { "C++14" }`
setting. This is Visual Studio's fault - until Visual C++ 2015 Update 3, there weren't actually any flags to set.

If we generate and compare XCode or Makefile, we will see a difference. For gmake, we see `-std=c++14` added
to the `ALL_CXFLAGS` list. For Xcode, we see - nothing. Evidently, this is not supported in the public Xcode
builder either. But if we run this against a prerelease version, then we see `c++14` in `CLANG_CXX_LANGUAGE_STANDARD`
and `libc++` in `CLANG_CXX_LIBRARY`.
