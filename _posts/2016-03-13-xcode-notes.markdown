---
layout: post
title:  "Xcode notes"
date:   2016-03-13 22:14:00 PM
categories: dev
tags: programming Xcode
---

Xcode is in parts good to great, and in parts terrible. One thing that's terrible is all the documentation
that I can lay my hands on. It seems to be aimed at people who work on small projects.

This is not a replacement, it's just some things I was searching while I was trying to figure things
out on my own. This is stuff that people have had to figure out, e.g. the Premake and CMake authors doing
Xcode project generation.

[Managing Xcode](https://pewpewthespells.com/blog/managing_xcode.html)

[Xcode Groups vs. Folder References](http://vocaro.com/trevor/blog/2012/10/21/xcode-groups-vs-folder-references/)

By the way, I think CocoaPods is approaching it from the wrong direction, it's maintaining a foreign
build system inside Xcode. I think. I should really actually use CocoaPods before I condemn it :)

[venmo/synx](https://github.com/venmo/synx) is an interesting project that reorganizes the files on disk
to match the order declared in the Xcode project. This is presumably because people find it easier to
organize the Xcode project first?

[XCode 4.2 and Using a Static Library Causing Problems](http://stackoverflow.com/questions/9726000/xcode-4-2-and-using-a-static-library-causing-problems/9726445). I read things like this and I think someone must have gone
horribly wrong somewhere.
