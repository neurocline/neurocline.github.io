---
categories: dev
date: 2016-09-14T01:55:00Z
tags: git
title: Git committer info
url: /2016/09/14/git-committer-info/
---

Simple queries can be done with the Git command-line. To see aggregate commit
history for the past year in all branches (and assuming that merges don't count as commits):

{{< highlight bash >}}
$ git log --no-merges --since="1 year ago" --all > log.txt
$ git shortlog -sne < log.txt
{{< / highlight >}}

This could be done with pipes as well to avoid intermediate files.

Note that --all looks at remotes, and for the most part this is what you want - your clone of
a repo rarely has tracking branches for all branches in the the remote(s), but you almost
certainly want information about that data.

By default, ```git log``` uses the medium pretty format, which looks like this

{{< highlight bash >}}
commit e22c092b573c93e8d9c358330ad65c82c14bff0a
Author: Brian Fitzgerald <bfitz@blizzard.com>
Date:   Thu Sep 29 17:07:11 2016 -0700

    Mass query of Github

    Do queries for repos, orgs, users for GitHub. Also handle GitHub
    Enterprise (the Ghosthub instance).

    Add --dir option to working.py to allow working dir to be set
    from the command line (slight improvement to workflow).
{{< / highlight >}}

You might find you want to go
beyond that, but ```--pretty=format:<fmt>``` looks daunting. Here is a format string that
reproduces the medium pretty format:

{{< highlight bash >}}
git log --pretty=format:"%C(auto)commit %H%nAuthor: %an <%ae>%nDate: %ad%n%n%w(0,4,4)%s%n%n%b"
{{< / highlight >}}

The ```git log help``` page has a section that covers all the format operators, although
some are hard to puzzle out just from the docs. In particular, indented formatting is
not explained well. The string ```%w(0,4,4)``` means to not wrap lines, indent the first line
by 4 spaces, and indent remaining lines by 4 spaces. This also stays in effect until
changed.

More importantly, in logs you want to process with code, you'll probably not indent them, and
you won't colorize them either.

If you use one-line formats, you'll probably want to use `---pretty=tformat:<fmt>``` instead.
The ```format``` option uses separator semantics, whereas the ```tformat``` option uses terminator semantics.
This is a lot of words to say that the file entry in ```format``` won't have a newline appended.

# Same commit hash in two or more repos

If you have the same commit in two repos, then you have some kind of fork or clone relationship.
Since a commit hash depends on the parent commit hash(es), and because the likelihood of random
hashes colliding is close to zero, then the only way that the same commit can appear in two repos
is if one is a fork or clone of the other. It's not possible to determine which is the clone of
the other, algorithmically, but this can usually be done by inspection.

This also doesn't have to go back to the root commit(s): one party could have pruned old history.

There is one other way that's technically not a fork, and that is to have pulled in commits from
another repo into your repo as a new independent tree. Git is a collection of commits, and you can
have more than one root commit. I think you could get this with a subtree merge, as well as by
just directly doing it.

But the starting principle is that each commit hash refers to unique work, so identical commit
hashes are referring to the same exact work.
