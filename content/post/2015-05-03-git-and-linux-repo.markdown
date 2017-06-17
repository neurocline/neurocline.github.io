---
categories: dev
date: 2015-05-03T01:28:00Z
tags:
- git
- linux
title: Git limits and the Linux repo
url: /2015/05/03/git-and-linux-repo/
---

![My helpful screenshot](/assets/LinuxGit.jpg)

According to Linus Torvald's announcement about the Linux 4.0 release,
the Linux git repo now has over 4 million git objects.

[Ima Sheep](https://lkml.org/lkml/2015/4/12/178) from lkml.org

> Looking at just the statistics in git, this release is not
just when we cross half a million commits total, but also cross the 4
million git object limit.

The sentence should be parsed as "we crossed the 4 million git objects line" instead of
reading that there is a maximum of 4 million objects. But even otherwise, since there
was no panic, we can see that 4 million objects in a repo is not cause for alarm.

It's important to note that there's no real difference between small blobs and big
blobs in terms of most operations - getting history, creating branches and so on.
It's also true that blobs as patches versus blobs as whole files is mostly irrelevant
too, except at the point where you are checking out a revision.

So, Git can handle 4 million git objects in a repo. Let's compare that to a large
game's code base. Our big game will have about 250K files in it. Let's say that each
file is modified an average of 10 times; that's 2.5 million files. I also have access
to some large projects, so I'll be looking into them to see how big they are, but
I think the last time I looked at a large repo, it was several million
file objects.

### Addendum

The picture above is of the Linux kernel, from visualizations done by the
[Gource](https://code.google.com/p/gource/wiki/Screenshots) project.
