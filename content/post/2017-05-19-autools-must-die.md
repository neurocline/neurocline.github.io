---
categories: dev
date: 2017-05-19T10:30:00Z
tags: text
title: Autotools must die
url: /2017/05/19/autools-must-die/
---

I want to kill Autotools in the best way - automatically replace every usage
of it with something much better. So I probably need to understand it first
(although the final approach is to use machine learning for cases like this).

Let's be clear up front - things like configure are an archaic leftover of
an older time where systems actively fought with each other to be different, or
were poorly assembled and had no active help for building software. The world
has changed, and we should continue to move towards a world where we don't need
a system that finds where header files are, or if certain tools are installed.
That's absurd. Use packages to declare dependencies.

[automake](https://www.gnu.org/software/automake/manual/html_node/index.html)

- [Basic Installation](https://www.gnu.org/software/automake/manual/html_node/Basic-Installation.html)
- [amehllo](https://www.gnu.org/software/automake/manual/html_node/Creating-amhello.html#Creating-amhello)
- [General Operation](https://www.gnu.org/software/automake/manual/html_node/General-Operation.html)

[Building a GNU Autotools Project](http://inti.sourceforge.net/tutorial/libinti/autotoolsproject.html)

GNU mandates that `AUTHORS`, `NEWS`, `README`, `ChangeLog` files exist in the top level, and Automake will report an error if they are missing. This is pretty heavy-handed. Oh, but
there is an option to disable it; I think it should off by default and enabled for those
strict folks. But this definitely says "Autotools is for making GNU projects; we guess you can
use this, if you must".

It looks like this is the development flow:

```
$ aclocal
$ automake --add-missing
$ autoconf
$ ./autogen.sh
$ ./configure
$ make
$ make install
```

`aclocal` creates `aclocal.m4`.`automake` creates `Makefile.in` and `configure`.

Files created by hand: `configure.ac`, `Makefile.am`.

Questions

- some docs reference `aclocal`, is this still a thing?

[VPATH builds](https://www.gnu.org/software/automake/manual/html_node/VPATH-Builds.html). I call this "building software grows up".

Ugg, Autotools gets it backwards - it calls the machine on which you build the "build platform",
and it calls the machine on which the compiled program runs the "host platform". This is
backwards from "host platform" being the machine on which you build and the "target platform"
being the machine that you run the compiled program on. I guess it's all relative, and so
we should all agree. Maybe the answer is to just drop the phrase "host platform", since it
does sound a little vague.

GNU does specify three options

```
--build
--target
--host
```

where `--build` is the system on which the package is built (and why can't this be
auto-determined?), `--target` is the system that built tools will run on, and then
`--host` is the system on which final output runs. This is useful in the edge case
where the build process creates a cross-compiler, e.g. we are on system A, the
intermediate tools (like GCC) run on system B, and the final output is intended for
system C. I'm sure there are real-world cases for this.
