---
categories: dev
date: 2017-02-28T13:53:00Z
tags: text
title: Automatically finding branches in Subversion repos
url: /2017/02/28/automatically-finding-branches-in-subversion-repos/
---

Subversion lacks formal mechanism for branches and tags. These are distinct entities
in many other source control systems, but in Subversion these are a combination
of convention and the copy primitive that Subversion has.

This is awkward for new users of Subversion, who have to learn how to follow the
convention, and even puts a little burden on creators of new repos, who have to
decide what pattern to follow. But it's an ambiguously flawed feature, because it
does have some benefit - it allowed for mega-repos that contain many independent
projects, each of which could be independently branched, and could even be
cross-branched.

Of course, all this flexibility comes out as a pain when you want to convert a
Subversion repo to, say, Git. Converting files themselves is trivial; the pain
comes when you want to faithfully represent the old repo's lines of development.

There are two typical patterns when using Subversion. The first is to have this
top-level structure for a single-project repository:

    /branches
    /tags
    /trunk

Your main line of development is in `/trunk`, you put branches as directories in
`/branches`, and you put tags in `/tags`. Oh, right, forgot to mention - Subversion
doesn't have tagging either. There's nothing to prevent you from committing to a copy
made in the `/tags` hierarchy, thus turning it into a defacto branch, because it's 
all convention. Hold that thought.

The other typical pattern is when Subversion is used to host a multi-project
repository. In that case, you might have this:

    /abdera/
        /branches
        /tags
        /trunk
    /accumulo
        /branches
        /tags
        /trunk
    ...
    /httpd
        /branches
        /tags
        /trunk
    ...

As you might guess, this is from the Apache Software Foundation repo at
https://svn.apache.org/repos/asf. Except, no, the reality is a lot messier.

Some projects, like ace or avro, have a handful of files and directories alongside the
`trunk` and `branches` dirs.

    KEYS
    branches/
    doap.rdf
    releases/
    sandbox/
    site/
    trunk/

The `htttp` (Apache) project has a host of nested projects, each of which has its
own `branches`, `tags` and `trunk`. This is a subset, in the interests of space:

    apreq/
    docs-build/
    flood/
    httpd/
        branches/
        tags/
        trunk/
        vendor/
        win32-msi/
    mod_fcgid/
    mod_ftp/
        branches/
        tags/
        trunk/
    mod_mbox/
    mod_spdy/
    mod_wombat/
    sandbox/
    site/
    test/

And maven - well, maven is its own special child. This is a subset of the top level:

    app-engine/
    archetype/
    maven-1/
    maven-2/
    maven-3/
    project/
    release/
    resources/
    retired/
    scm/
    shared/
    site/
    trunks/

Look, a `trunks` directory. Which is - empty? And the `maven-1` dir has an empty
`trunks`. We finally find source in `/maven/maven-2/branches`. And the mainline
of development is really `/maven/maven-3/trunk`.

So, while the ASF repo is an example of the multi-project repo approach, it's
also an example of the heterogeniety of large Subversion repos. The approach up
until now when converting a repo is that you have to describe the structure, which
can be time-consuming, and you can get it wrong. But maybe that's enough?

What about the fact that Subversion copy operations can be of any subset and can
do topologically-absurd actions? For example, copying root to a point in the
tree, or copying a point in the tree to a parent of that point?

By the way, it is possible to compose a Git repo as a Subversion repo, it's just
bizarre and unwieldly. Take master and all branches, and add them as subtrees
of a new root. This takes up almost no space in the Git repository, just like with
Subversion. The difference is that unless you do selective checkout, you're going
to have a very large working folder. Selective checkout is the default behavior
in Subversion.
