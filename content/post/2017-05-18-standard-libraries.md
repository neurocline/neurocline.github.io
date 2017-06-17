---
categories: dev
date: 2017-05-18T02:50:00Z
tags: text
title: Standard libraries
url: /2017/05/18/standard-libraries/
---

Before the modern era, C evolved an ecosystem that turned into the POSIX standards
and the C Standard Library. However, this was an incomplete solution, in that there
was a specification, but every vendor had to implement their own versions. The
infinite varieity of what was actually available to any one person
directly led to the abomination that is autoconf. Still, it was all we had, until
the 1990s.

There are many reasons for the success of new languages, but in the past 10 years,
it seems clear that some of the success is due to the libraries that are easily
accessible to programmers in that language.

This started with TeX, actually, which is not something you think of as a programming
language, even though it is technically (Turing-complete, that is). TeX was
successful for two reasons. First, it was designed and written by Donald Knuth;
he really revolutionized the writing of scientific papers. The story I've often heard
told is that TeX was written to make writing the next volumes of The Art and Science
of Computer Programming easier.

But TeX also had CTAN, the Comprehensive TeX Network. This meant that TeX users could
share libraries of TeX code. This really helped TeX's growth in popularity. But it
also had another side-effect; it helped Perl.

Perl has CPAN, and without CPAN, even with being the first "language of Internet plumbing",
Perl probably would have stayed a niche language. And CPAN was directly inspired by CTAN.
With CPAN, Perl entered a long virtuous cycle; new users wrote in Perl because there was
a large library that could be easily accessed, and that library continued to grow and grow.
Perl has since faded due to the lack of the language itself.

However, there's a lesson here that has been mis-learned. It is not package archives
that make languages successful. It's not having an ecosystem; it's what's in the ecosystem.

It's existing code that you can use to solve your problem. And that means it has to be usable
code.

What Python got right more than most others is that their core code is pretty comprehensive,
and is constantly argued over and refined by a group of people who are good at APIs.
By contrast, the libraries added to the C++ standard come to the committee largely baked,
and thus retain lots of rough edges; and are few in number compared to what Python has.

## C Standard Library

- [C standard library](https://en.wikipedia.org/wiki/C_standard_library)
- [The C Library Reference Guide](https://www-s.acm.illinois.edu/webmonkeys/book/c_guide/)

## POSIX

At first, POSIX just meant this: IEEE Std 1003.1-1988. I didn't know this, but Richard
Stallman named it (it was going to be IEEE-IX). POSIX started as a standardization for
Unix.

POSIX and C are joined at the hip, so this could be thought of as the C libraries.

Mostly the lessons from POSIX are "don't do it that way", except that this is the
single most widely used set of interfaces in the programming world; many later system
provide POSIX compatibility of one form or another. For example, many low-level networking
libraries in newer languages just transliterate POSIX networking calls into their own
language.

- [Austin Group](http://www.opengroup.org/austin/) as the home of the POSIX standards.
- [IEEE Std 1003.1™-2008, 2016 Edition](http://pubs.opengroup.org/onlinepubs/9699919799/)

## Java

I guess I have to.

## Perl

Evidently Perl 6 is a thing.

## Python

The gold standard?

## Ruby

The Ruby Standard Library lacks coherent documentation. There's a lot there, but
it's somewhat scattered - almost like having docstrings.

- [Help and documentation for the Ruby programming language](https://ruby-doc.org/)

That said, there are interesting patterns in their libraries, and the Core library
(that built in, doesn't need a `require` to access) is surprisingly large.

The Standard Library has very oddball modules in it; some don't seem like they
should be standard (e.g. Abbrev, LUSolv, Etc, Win32OLE). There's also what looks like crap: see
extmk, un.

The balance between what's in the Standard Library and what's a standalone gem (their
name for a package) is not as clear as in other languages. It could just be a
representation of the relative effort spent in Ruby vs other languages.

## Go

New package manager?

## Rust

Need to learn. May be best of breed?

## Reference

[Module Counts](http://www.modulecounts.com/) tracks libraries in popular package
repositories. This is a proxy for languages, since many languages have a single
popular repository.

Package repositories by language:

- C#
  - [nuget](http://www.nuget.org/) has 80K packages
- Clojure
  - [Clojars](https://clojars.org/) has 19K packages
- D
  - [DUB](https://code.dlang.org/) has 1K packages
- Erlang
  - [Hex](https://hex.pm/) has 4K packages
- Go
  - [Gopm Registry](https://gopm.io/) has 19K packages
- Haskell
  - [Hackage](http://hackage.haskell.org/) has 11K packages
- Java
  - [Maven Central](http://search.maven.org/) has 188K packages
- Javascript
  - [npm](https://www.npmjs.com/) has 455K packages
- Perl
  - [CPAN](http://www.cpan.org/) has 35K packages
- PHP
  - [Packagist](https://packagist.org/) has 140K packages
- Python
  - [PyPI](https://pypi.python.org/pypi) has 108K packages
- R
  - [CRAN](https://cran.r-project.org/) has 10K packages
- Ruby
  - [RubyGems](https://rubygems.org/) has 132K packages
- Rust
  - [Cargo](https://crates.io/) has 9K packages
