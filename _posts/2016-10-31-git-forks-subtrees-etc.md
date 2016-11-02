---
layout: post
title:  "Git repos - forks, subtrees and other shenanigans"
date:   2016-10-31 1:44:00 PM
categories: dev
tags: git
---

A Git repository can be modelled as a DAG (directed acyclic graph). More properly, as a set of DAGs,
and this is surprising to some people.

Each Git commit has zero or more parents. A commit with one parent is the normal work commit. A commit
with two or more parents is a merge commit - several lines of development have been joined together.
And while it is rare to have more than two parents, it's not unheard of nor particularly bizarre. When
joining five branches together, sometimes it's better to do it once rather than have 4 successive
merge commits.

A commit with zero parents is a root commit. The very first commit into a Git repo has to be a root
commit, because there's nothing yet to be a parent. This is most peoples' view of a Git repo - there
is a single root commit, and there's the occasional merge commit, the frequency of which depends on
the work style (the reverse of a merge, a branch, is when two commits have the same parent).

             c <-- d
           /         \
    a <-- b <-- e <-- f <-- g <-- h

But a repo can have more than one root commit; it could easily have many. They could merge together, or
they could stay independent forever.

One common way for this to happen is for two parallel but unrelated-in-details development
activities to happen. For example, GitHub has provided the ability to have a "Github Pages" web page
as part of your Github repository. If your repository has a branch named `gh-pages`, then with the
help of a setting you can have this automatically be published as a web page at
http://<username>.github.io/<repository>.

    a <-- b <-- c <-- d
    
    e <-- f <-- g <-- h <-- i <-- j

Another common way to have multiple root commits is to use git subtree to pull in part or all of
another repository into yours to make it easier to work with that repo. You might have a large program
that uses a socket library, but rather than try to consume it as a library or have a build system that
knows how to fetch multiple repos, you use subtree to add it to your repo. This has the side effect
of adding that repo's root commit(s) for the part you pull in.

              b <-- c <-- d
                           \
    e <-- f <-- g <-- h <-- i <-- j

Here are some surprising but obvious facts. If two repos have any shared commits, then they must also
have the root commit(s) at the base of any shared commits. So if you want to know whether two repos
share commits, all you need to do is look at their root commits. This is true whether or not you used
a shallow clone to fetch a copy of the repo; shallow clone doesn't alter the fact that commits exist,
it just neglects to give you copies of those commits. And since the current Git code handles side
effects of shallow clones poorly, few people do them (this could be fixed in a straightforward fashion,
see future post on this).

If repositories A and B have the same root commits, then it's very likely that one is a fork of the
other. It's very unlikely for two root commits to be identical unless one is the copy of another. That's
because a commit is the hash of the metadata for the commit (author, date etc), the hash of the tree for
the commit, and the hash of the parent commits. Note that it's not impossible, just unlikely. You could
set out to craft two root commits in exactly the same way, independently. In practice, this means specific
collusion; this is still a copy! Same for full commit chains. So if we have two equal commits, then one
is the copy of the other, and we'll presume fork.

It's not possible without outside data to determine which repository is the fork. For the most part,
we don't care, except when trying to assess provenance. Provenance needs more information than what is
in the repository. Specifically, numbers of commits don't tell us much, because an active fork of a
dormant upstream is a reasonable thing to see. But so is a fork with a handful of extra commits (say
a bugfix or a private feature) is of an active upstream.

If repository A has all the root commits from repository B, but has more root commits of its own, then
it's likely that A has put repository B in as a subtree. Also note that the dates on each root commit
don't tell you who is the subtree. Your relatively new project could put the Zlib repo in a subtree.
Still, here we can rely on statistics. If your repo has most or all of what's in the other repo, and
your repo has roots of its own, then that other repo is the parent and you have a subtree.

So, some heuristics.

## No shared root commits

There is no simple causal relationship between the two repos. Note that this does not say that the
content was not copied, just that the repository itself was not copied in any fashion.

By the way, it's possible for Github to be wrong when it says "A is the fork of B". It's rare, but B
could be re-created after it was forked; Github will not update it's "forked from" metadata. So
if you have two repos where Github says "fork" but they don't share root commits", then what you
probably have is "A is a fork of a repo that no longer exists".

## Same root commits in both

If two repositories have the same root commits, then presume that the fork is the one with more
commits. This will be wrong some of the time, so if you have outside metadata (e.g. like from Github)
then use that metadata.

## Common root commit

If a root commit is in two repos, and most of the commits that lead to the root commit are also
in both repos, then assume that the upstream repo is the one with fewer root commits. The typical
subtree case is when an existing repo adds another via subtree.
