---
categories: dev
date: 2016-02-02T08:35:00Z
tags: make build
title: Can we get rid of make?
url: /2016/02/02/can-we-get-rid-of-make/
---

Roughly congruent with the development of C and Unix, the ```make``` build automation tool
was invented (originally created in 1976 by Stuart Feldman). Make replaced ad-hoc build
shell scripts that accompanied each program's source.

As wonderful as Make was originally, it has a number of flaws:

* the syntax is cumbersome for all but trivial programs, and virtually unworkable for large ones (most make files are copied from other make files).
* there is no standard; many different make programs, all with incompatible features and syntax
* make is very tied to Unix and sh
* recursive make is very slow, but non-recursive make on large programs is nearly infeasible.

The standardization part is not quite true - POSIX did finally standardize the basic features and
function of Make, but it was too little and too late. And there is GNU Make which is widely used,
and the standard on Linux and Mac OS X.

Go tried to get rid of the need for make by putting simple build feature support into the language
itself. However, I notice that any reasonable-sized Go program comes with a Makefile, at least on
Linux and Mac OS X. And this is not from inertial - the makefiles are absolutely necessary, because
large programs are never just all code in a single language.

I'm not even counting the true sin of Make, which was to cause Autotools to need to be invented. Autotools
is the true plague on the land. We'll talk about that another time.

How can we improve the state of the art in building software? More and more, the world is polyglot, which
implies the need for something like Make, but we're also turning back away from a platform monoculture.
The things we can count on that cross platforms at the moment are a handful of widely used programming
languages, chief among them being C and C++; there is no platform or architecture I know of that's in use
that doesn't have a C compiler, and usually at least a GCC target.

First, we can reduce the problem space, by chipping away at both ends.

I know I slightly disparaged Go, but it has succeeded in removing the need for Make and Makefiles for
the vast majority of Go programs. And, Makefiles for large Go programs are much simpler than they would
otherwise be. Go is close, and is gaining features that might get it to 100%. More languages should embed
features that make building single-language code bases automatic, regardless of their size.

On the high end, we do need something that cuts across languages. We should not expect C to add features
to facilitate mixing with D or Rust or Lua or SQL. So it does seem like we need something like Make. But
if we remove the need for Make on the low end, maybe we can design a new system that can be widely used
from the start. All previous Make incarnations were targeted at a single platform, whether "Unix" in
general or a specific flavor of Unix (or Windows, or Solaris, etc). But that was before the 2000s and
the true flowering of open source. Nowadays, a properly designed program that is widely compatible from
the start has a chance of taking off.

One huge issue to tackle is going to be interoperability with IDEs. We should never require the use of
an IDE, but on the other hand many programmers prefer to use one. And there will never be the one true
IDE to rule them all (the last attempt, Eclipse, failed miserably). We've seen a few attempts to do this;
SCons, CMake and Premake spring to mind. Each took a slightly different path; CMake attempts to just directly
replace Make but have an option for IDE project file generation, whereas Premake just flat-out defines
"make me a project file" as its mission. Partially, you can look at Premake as an attempt to turn Makefiles
into assembly language; you describe your project in a much higher-level fashion, and Premake creates a
Makefile for you (or a Visual Studio project, or an XCode project, or anything you can find a generator for).

Of course, this comes with a cost. Premake just can't do most of what Make can do, and CMake's main claim
against Make is that it's a lot more cross-platform (it can do a lot of what Make can do, but is very
cumbersome to use even on moderately sized projects). Likewise, SCons, written in Python, can run on almost
anything that can run Python, but for all of that isn't terribly cross-platform, and is a lot slower than
the alternatives to boot.

But SCons and Premake were based on a great idea. You write their scripts in a high-level programming
language; for SCons, you can write arbitrary Python code to do make tasks, and for Premake you write
in Lua. Both are far more productive for any but trivial build tasks compared to Make. Any Make replacement
should follow this trend. The challenge for SCons is that you need a Python install to use SCons. On
the other hand, someone has to build a compatible Premake or CMake binary for you. CMake has more
uptake in the Unix-like world, and Premake is easier to use in a Windows world.

Another advance in the world since Make was invented was the idea of packaging systems. Again, this is an
orthogonal problem that we're going to defer solving, but you want your build system to work very well
with package systems of all kinds. There are legitimate reasons for multiple packaging systems. For example,
computer languages are available on essentially all platforms, so those languages need a packaging system
that crosses all platforms. But platforms all have many languages that can be used on them, so a platform
needs a polyglot package system that crosses all languages. All this means is that our build system has
to be able to handle arbitrary package systems, depending on the use case.

And we need our build system to work well with package systems, because we've long since gone past the point
where any group writes all its own code. It's not just standard libraries, we all use each others' source
very heavily. A build system that cannot interoperate with a package system means that we'll need to develop
custom tools, or do things manually, and neither of these things scales. And again, while you can probably see
where I'm going with this, I'm going to come back to "just Make" for now.

So we're getting a little closer here. We want something that

- defers a lot of ordinary building to the language
- lets you write arbitrary complex build tasks in a good high-level language
- is available for every possible platform and architecture
- works with any package system
- treats IDEs as first-class citizens
- adds basically zero overhead to the build process

Here is my take on things

1. We need the language support ASAP in C/C++. Fortunately, both Clang and GCC are open source, and a
concerted effort could make this happen quickly (Microsoft is an outlier but maybe there's a way
to now leverage open source with them).
2. The "available anywhere" bit means I want this delivered itself as source and built as a binary (by
itself, bootstrapped with some even more widely available system), and thus available anywhere. At the
moment, this means it's written in C or C++.
3. The "is available anywhere" means that we want our Make program part of every operating system.
This is feasible for Linux and Mac OS X, would require some lobbying for Microsoft (self-interest
should help here), and then everyone else would be motivated to provide it.
4. The "write makefiles in a high level language" is a little more fluid. No C or C++ compiler is yet
interactive enough to write build instructions in C/C++. But we're closer than you might think, especially
if we can get some more language support. Something like Lua would be the next runner up, since the
Make program could easily contain a Lua interpreter (it's small). Python, Perl, Ruby are all large and
we would not want to embed them, and if not embedded, then there is a dependency in order to build.

I'm going to stop here for now. It's an ambitious goal, but not really that big. The next step is
a plan and a loose design.

This is possible. Time to move past the 1970s, folks.

# Reference

[Make](https://en.wikipedia.org/wiki/Make_(software))

[Makefile](https://en.wikipedia.org/wiki/Makefile)

[Replacing Make with Ninja](http://jpospisil.com/2014/03/16/replacing-make-with-ninja.html)

[A LIST OF MAKE SYSTEMS](http://blog.bfitz.us/?p=2099)
