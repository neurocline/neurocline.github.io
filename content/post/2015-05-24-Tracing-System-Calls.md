---
categories: dev
date: 2015-05-24T23:24:00Z
tags: build, linux, mac, bsd
title: Tracing System calls
url: /2015/05/24/Tracing-System-Calls/
---

## Linux

Tracing system calls on Linux is straightforward. The `ptrace` API lets
one process trace all system calls made by another process, and the
command-line program `strace` uses `ptrace` to allow a user to do the
same.

* [strace(1)](http://linux.die.net/man/1/strace)
* [ptrace(2)](http://linux.die.net/man/2/ptrace)
* [Write Yourself an Strace in 70 Lines of Code](https://blog.nelhage.com/2010/08/write-yourself-an-strace-in-70-lines-of-code/)

## Mac OS X

Tracing system calls on Mac OS X is a little harder, but more powerful.
The `dtrace` system ships with Macs starting with Mac OS X 10.5. Unlike
strace, however, support for dtrace has to be built in to programs. Mac
OS X ships with several thousand probes (the attachment points), and most
normal monitoring is covered. Brendan Gregg's blog has a Mac OS X-specific
dtrace page, as an example, and there are other useful tutorials

* [Top 10 DTrace scripts for Mac OS X](http://dtrace.org/blogs/brendan/2011/10/10/top-10-dtrace-scripts-for-mac-os-x/)
* [Hooked on DTrace, part 1](https://www.bignerdranch.com/blog/hooked-on-dtrace-part-1/)

Apple added ptrace to Mac OS X, maybe. There's a man page for it, but no
strace program for user-level use. I'll have to try writing some code.

* [ptrace(2)](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man2/ptrace.2.html)

There are ways to hook/override program and system functions at runtime.
These work but aren't truly supported, so are of most use for debugging
situations.

* [mach_override](https://github.com/rentzsch/mach_override)
* [mach_inject](https://github.com/rentzsch/mach_override)

Amit Singh went the kernel extension approach to get access to system calls

* [Re-routing System Calls](http://osxbook.com/book/bonus/ancient/syscall/)

While there are definitely nefarious uses for this stuff, it can be used for
developers too. For example, this paper talks about rootkits, but the techniques
also make for great developer tools.

* [Advance Mac OS X Rootkits](https://www.trailofbits.com/resources/advanced_macosx_rootkits_paper.pdf)

## BSD

The equivalent to ptrace on BSD is ktrace. Unfortunately, ktrace is not
available on Mac OS X. That's annoying, because it is complementary to
DTrace, which is an awesome sysop-level tool, but does not give complete
access to all system calls. Mac OS X had ktrace in 10.4 and earlier.

* [ktrace(1)](https://www.freebsd.org/cgi/man.cgi?query=ktrace(1)&sektion=)
* [ktrace(2)](https://www.freebsd.org/cgi/man.cgi?query=ktrace&sektion=2)

## Windows

There is the amazing Process Monitor, which traces all file and registry
actions. However, this is strictly a user-level program, you work with it through
its GUI. It's very useful, but does not give you system-call level access from
your own source code.

* [STRACE EQUIVALENT FOR WINDOWS](http://kohei.us/2010/06/25/strace-equivalent-for-windows/)

There is a fairly new project called StraceNT. It comes with source code.

* [StraceNT - A System Call Tracer for Windows](http://intellectualheaven.com/default.asp?BH=STraceNT)
* [github/intellectualheaven/stracent](https://github.com/intellectualheaven/stracent)

Dr. Memory comes with "strace for Windows" called drstrace.

* [github/DynamoRIO/drmemory](https://github.com/DynamoRIO/drmemory)
* [System Call Tracer ("strace") for Windows](http://www.drmemory.org/strace_for_windows.html)

Event Tracing for Windows (ETW) is the Microsoft official technique, and has
several programs layered on top.

* [Core OS Events in Windows 7, Part 1](https://msdn.microsoft.com/en-us/magazine/ee412263.aspx)

There is Logger and LogView, also Microsoft tools

* [Logger and LogViewer](https://msdn.microsoft.com/en-us/library/windows/hardware/ff552060(v=vs.85).aspx)

There is NtTrace

* [NtTrace - Native API tracing for Windows](http://www.howzatt.demon.co.uk/NtTrace/)

An article

* [Intercepting System Calls on x86_64 Windows](http://jbremer.org/intercepting-system-calls-on-x86_64-windows/)

## fabricate

This is a python build tool that watches for files that have changed as a result
of running a command. It uses strace on Linux but does filetime watching on
Windows, so maybe look for how to get Python on Windows to use one of the other
file monitoring solutions?

* [fabricate](https://code.google.com/p/fabricate/)
* [fabricate issue 3](https://code.google.com/p/fabricate/issues/detail?id=3)

## tup

The tup build tool uses DLL injection on Windows to detect file I/O.

* [tup](http://gittup.org/tup/)

## ninja

The ninja tool also does filesystem watching, I think.

[Ninja](http://martine.github.io/ninja/)

## Google "building in the cloud"

This is a FUSE-based approach, so not exactly tracing system calls.

[Build in the Cloud: Accessing Source Code](http://google-engtools.blogspot.com/2011/06/build-in-cloud-accessing-source-code.html)
