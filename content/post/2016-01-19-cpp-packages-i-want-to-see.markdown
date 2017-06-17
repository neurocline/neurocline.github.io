---
categories: dev
date: 2016-01-19T00:37:00Z
tags: c++ programming
title: C++ packages I want to see
url: /2016/01/19/cpp-packages-i-want-to-see/
---

Here are some packages I want to see.

* Boost (broken into individual pieces, not a mega-package)
* crypto
* libtommath
* gmp (although non GPL projects have a hard time using it)
* pcre
* icu
* curl
* zlib
* openssl
* protobuf
* sqlite
* breakpad

More generically, things like this

* command-line processing
* unit test frameworks
* every crypto algorithm
* database interfaces
* low-level network stacks besides just Boost.ASIO
* web frameworks
* graphics libraries
* embedded languages: lua, python, etc

Also, a decent fraction of the Standard C++ library might be better off as packages, at least in the proposed
drastically expanded version that includes game and UI libraries. Specifically, Boost should be available
as packages, not as a machine-level installable thing.

I also expect there to be platform-specific packages. But that would be fairly rare, instead we'd
have packages that had inside them platform-specific bits, but the user doesn't care as long as it
works. E.g. networking frameworks need to be built on top of platform-specific bits.

# Reference

* gmp assorted links: [here](https://www.reddit.com/r/haskell/comments/1twtvm/the_problem_with_integer/) and [here](https://news.ycombinator.com/item?id=6990233) and [here](https://en.wikipedia.org/wiki/GNU_Multiple_Precision_Arithmetic_Library) and [here](https://gmplib.org/) and [here](https://github.com/gx/gmp) and [here](https://github.com/ncw/gmp)).
