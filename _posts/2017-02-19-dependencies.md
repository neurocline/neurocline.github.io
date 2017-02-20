---
layout: post
title:  "Dependencies"
date:   2017-02-18 11:36:00 AM
categories: dev
tags: text
---

Dependencies are one of the main pain points when working on a large code base.

You can make a monolithic codebase where you write everything from scratch. This is
a daunting task and I don't think anyone does this any more, even when they have
their own hardware. The amount of code and the depth of some of the code is so
big that even Google or Microsoft can't really afford to do this.

So we use code we didn't write to extend our own code.

INSERT STUFF HERE

It's our program, so we should make all policy decisions at the top level. But we also
don't want to modify package code in order to use it, and we don't want heavy
configuration required to use code.

The crudest form of policy is "use this package/don't use this package". Since it
is our program, nothing forces us to use a specific package. But we really want to
use packages, because we want to make and ship our program in a reasonable amount
of time.