---
categories: dev
date: 2017-03-05T15:00:00Z
tags: text
title: Local package managers
url: /2017/03/05/local-package-managers/
---

Recently I read this presentation:

[Zen and the Art of Package Management](http://slides.com/yehudakatz/deck-4#/)

I like the terminology of global package manager versus local package manager, so I think
I'll switch to that instead of "programming language package manager" (too unwieldy).

Specifically, we want more local package managers and fewer (no) global package managers.
Some existing package managers claimed to be local package managers:

- [Bundler](http://bundler.io/) - for Ruby
- [npm](https://www.npmjs.com/) - for Javascript/Node.js
- [Cargo](https://crates.io/) - for Rust
- [Yarn](https://yarnpkg.com/en/)
- [Composer](https://getcomposer.org/) - for PHP
- [Pub](https://pub.dartlang.org/) - for Dart
- [Cocoapods](https://cocoapods.org/) - for Cocoa (Objective-C and Swift)
- [Swift Package Manager](https://swift.org/package-manager/)

Note, first off, that "local" can go along with "unwieldy" - cocoapods is getting quite
unwieldly, so is npm.

I also like the clear-call out that containers are solving a problem that shouldn't
exist - global dependencies. The fewer global dependencies, the fewer container strategies
you need on the development side.
