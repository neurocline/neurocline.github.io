---
layout: post
title:  "Converting Subversion to Git"
date:   2017-02-18 11:36:00 AM
categories: dev
tags: text
---

Several groups have converted their large Subversion repository to Git. The largest I
know about is KDE, and they ended up writing a new program `svntogit` to do that.

- [svn2git](https://github.com/svn-all-fast-export/svn2git)
- [UsingSvn2Git](https://techbase.kde.org/Projects/MoveToGit/UsingSvn2Git)

The KDE Subversion repository is still accessible at

- [svn://anonsvn.kde.org/home/kde](svn://anonsvn.kde.org/home/kde)
- [https://websvn.kde.org](https://websvn.kde.org)

Other people have used their tool

[Switching an SVN repository to Git with KDE's Svn2Git](https://www.jeffgeerling.com/blogs/jeff-geerling/switching-svn-repository-svn2git)

Some people persevere with `git-svn`, bundled with Git.

[Converting a Subversion repository to Git](http://john.albin.net/git/convert-subversion-to-git)

I was writing my own partly because of pathologies in in-house repos that choked everything
I found, and partly because I have bigger plans.

## Large Subversion repos

The Apache Software Foundation puts all projects into a single common repo. As of this writing,
it has 1,783,493 revisions:

[https://svn.apache.org/repos/asf](https://svn.apache.org/repos/asf)

KDE used to be in Subversion, and small pieces stil live there (just localization at
this point, I think). As of this writing, it has 1,482,977 revisions:

[svn://anonsvn.kde.org/home/kde](svn://anonsvn.kde.org/home/kde)

GCC is in Subversion, although I think they are trying to switch to Git. As of this writing,
it has 245,564 revisions:

[svn://gcc.gnu.org/svn/gcc](svn://gcc.gnu.org/svn/gcc)

SourceForge still uses Subversion for project repos.

## Random notes

[Facebook's git repo is 54GB](https://news.ycombinator.com/item?id=7648237)

[geerlingguy/ansible-vagrant-examples](https://github.com/geerlingguy/ansible-vagrant-examples)

[GCC: Anonymous read-only SVN access](https://gcc.gnu.org/svn.html)

[Adventures in Converting Subversion to Git](http://scholarslab.org/research-and-development/adventures-in-converting-subversion-to-git/)

[My Experiences With Subversion](http://www.chiark.greenend.org.uk/~sgtatham/svn.html) - this is
really about a CVS -> Subversion conversion.
