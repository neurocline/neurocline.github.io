---
layout: post
title:  "Command-line argument parsing"
date:   2015-11-04 11:06:00 AM
categories: dev
tags: command-line
---

One of the many things I want to see, because I want to use it, is the definitive
library for command-line argument parsing. There are a lot of these, and yet I
see programs doing ad-hoc command-line parsing, because most of the libraries
are verbose to use and require effort to understand. I've certainly done my share of
both ad-hoc and "real" command-line parsers, as well as used a dozen or so argument
parsing libraries.

The code you write to do command-line argument parsing needs to be as straightforward
as possible, showing the intent of the command-line. Otherwise you'll be tempted to
avoid it for simple command-lines, and you'll be spending significant effort
maintaining it for complex command-lines.

It also need to be flexible, as there are multiple styles of command lines. I don't
mind defaulting to the GNU getopt style, with single-dash and combining for single-character
commands and double-dash for word commands. However, we do have Microsoft with their
default disaster (command lines preceded by slash characters), and there are ecosystems
that are stubborn with their oddball styles. Suborn by making it easy, but don't put
up walls.

And this library needs to be something that can be copied easily; any license more
onerous than the 2-clause BSD license is yet another excuse to make a new system. It's
a command-line, and we don't need 50 different ways to parse it.

I also want this to be multi-language. Some languages are converging towards a single
stock library that are used for command-line parsing. The best of the breed to date I've
seen is Python's argparse. However, argparse is something that I have to read documentation
for in order to learn to use. Instead, what I want is the same library, tweaked for language
specifics, across all languages - where possible, of course; languages that diverge from
procedural make that harder.

Of course, if you can use a descriptive definition instead of a proscriptive one, then
you could have a common command-line argument parsing system. I just don't know if more
complex operations can be done in a descriptive fashion. There is one system that has
made a valiant try, and that's [docopt](http://docopt.org/). The test of this would be
to try to implement a large command line (like, say Git) in this system.

As with a lot of other things I want, this is interesting enough that I will eventually
work on it, but if someone else gets to it first, I'll happily use what they make.

## Reference

The contradiction here is: ignore what others are done so that you don't limit yourself;
and yet know what others have done so you make something better and different (e.g don't
painfully re-invent the wheel). No problem. Here's a sampling of what already exists. I don't
know how many hundreds of command-line argument parsing libraries exist, but I'm sure it
is many hundreds.

Also, I'm not endorsing docopt. Yet. But it's a good start.

Multiple languages

* [docopt](http://docopt.org/). Describe command lines to generate parser. Lots of languages supported, see [docopt GitHub repo](https://github.com/docopt).

Python

* [argparse](https://docs.python.org/3/library/argparse.html). Python's command-line processing library.
* [opster](https://github.com/piranha/opster).
* [Plac](http://plac.googlecode.com/hg/doc/plac.html). Has decent minimal complexity, uses code structure to create argument parsing.
* [Clap](https://pypi.python.org/pypi/Clap/0.7).
* [options.py](http://apenwarr.ca/log/?m=201111) from Bup. Another descriptive approach.

Ruby

* [optparse](http://ruby-doc.org/stdlib-2.2.3/libdoc/optparse/rdoc/index.html). Stock Ruby argument parser; see [decent example](https://gist.github.com/rtomayko/1190547).
* [clap](https://github.com/soveran/clap). 
* [clamp](https://github.com/mdub/clamp).
* [Thor](https://github.com/erikhuda/thor)
* [clip](http://clip.rubyforge.org/)

C

* [getopt](http://www.gnu.org/software/libc/manual/html_node/Getopt.html). Dates back to 1980, in POSIX.2 standard.
* [GNU getopt_long](http://www.gnu.org/software/libc/manual/html_node/Getopt.html). The GNU code is GPL, but there are BSD-license clones written by others.
* [genparse](http://manpages.ubuntu.com/manpages/hardy/man1/genparse.1.html). Parser generator, creates code for getopt_long.
* [Arg_parser](http://www.nongnu.org/arg-parser/arg_parser.html)
* [getoptpp](https://code.google.com/p/getoptpp/). A getopt variant.
* [Google Commandline flags](https://google-gflags.googlecode.com/svn/trunk/doc/gflags.html)
* [popt](http://directory.fsf.org/wiki/Popt) and [man](http://linux.die.net/man/3/popt). RedHat, X11 license.

C#

* [synoptic](https://github.com/bitdiff/synoptic). Argument parsing through code structure. Not sure I like this for large command lines, but it's an interesting approach for simple ones.

C++

* [cli](http://codesynthesis.com/projects/cli/). DSL approach, so slight negative for simple command-line programs.
* [ArgvParser](http://mih.voxindeserto.de/argvparser.html).
* [OptionParser](http://optionparser.sourceforge.net/)
* [Boost.Program_options](http://www.boost.org/doc/libs/1_59_0/doc/html/program_options.html)
* [TCLAP](http://tclap.sourceforge.net/). Templates.
* [GetPot](http://getpot.sourceforge.net/). Also has Java, Ruby and Python versions. Uses DSL and runtime parser to describe command-line argument parsing.
* [CmdLine](http://www.bradapp.com/ftp/src/libs/C++/CmdLine.html). Has descriptive/DSL mode.
* [arg](http://ccdw.org/~cjj/prog/arg/arg_manual.xhtml)
* [The Lean Mean C++ Option Parser](http://optionparser.sourceforge.net/). header-only library.

Java

* [Commons CLI](http://commons.apache.org/proper/commons-cli/). Default Java library (?)
* [args4j](http://args4j.kohsuke.org/). Anotations-based.
* [JCommander](http://jcommander.org/). Best tagline: "Because life is too short to parse command line parameters"
* [ArgParse4j](http://argparse4j.sourceforge.net/).

Javascript

* [Optimist](https://github.com/substack/node-optimist).

Go

* [Flag](https://golang.org/pkg/flag/).
