---
layout: post
title:  "Source Control data models"
date:   2017-03-03 18:00:00 PM
categories: dev
tags: text
---

I'm going to discuss the designs of the currently popular source control
systems; this is especially important if you want to convert repos from
one system to another. This is notes for both a book on Source Control and
for notes on converting projects from one source control system to another.

If you want to quibble about the term, see the end of the text.

There are several distinct systems of design when it comes to making a source
control system, having to do with how you address lines of development. There's also
how you represent changes explicitly or not; some systems were based on patches as
an implementation detail that couldn't be changed, some expose patches as a
first-class citizen visible to the user, and some treat patches as an optional
optimization step.

There are a number of old systems that I used but have forgotten how they worked,
or never knew. They were proprietary, so little was said about their design. In
particular, early systems like PVCS (Polytron Version Control System) and MPW
Projector/SourceServer were used before CVS and SourceSafe existed or were
popular.

## Per-file revision

SCCS and its descendants store revisions of individual files; each revision of a
specific file has a version number unrelated to the version numbers in another file.

As well as per-revision numbers that are assigned automatically, you can assign
a symbolic name to a revision. If you assign the same name to a revision in each file,
you can then refer to that as a tag (a fixed point in revision space) or a branch
(a sliding point). CVS introduced the idea of reserved symbolic names: HEAD refers
to the most recent revision of each file, and BASE the most recent revision you
have fetched locally.

These treat the trunk separately from branches - the trunk is the initial line of
development, numbered 1.1, 1.2 etc, and then branches start a new numbering scheme
at the point of branching. Thus, by looking at numbering, you can determine the
branching hierarchy. This of course assumes a very centralized system; there is
one source of truth.

Because tag and branch names share the same namespace and are stored per-file, they
pretty much need to be unique across the entire set of files.

Because of the consequences of the per-file versioning, removing files is tricky,
and adding files interferes with old history. If you remove a file, its history is
gone. For this reason, CVS introduced an Attic folder on the server, where old files
are moved into. However, of course, if you add a file, remove it, then add it again,
then remove it again, you have a collision.

All use deltas to store multiple revisions; SCCS and direct descendants like TeamWare
or BitKeeper use interleaved deltas, RCS uses backward-deltas (main line) and forward-deltas
(branches). History has shown that the RCS approach is actually very sub-optimal, but
SCCS isn't much better. However, deltas are not key to the data model itself, and
are merely to reduce storage size.

There is locking, but this doesn't affect the data model, just the usage model.
It's possible to have or not have locking in any source control system.

The last thing specific to this kind of system is that they all allow arbitrary
precision when checking out revisions of files. You could have a single file
checked out on a different branch, or directories, or any arbitrary combination
you desire. This is not possible in some other systems (nor is it a good idea,
really).

The source control systems of this era have keyword expansion, which I hope is
dying out. They also poorly handle binary files, having been made for small text
files.

## The set of all revisions as a filesystem.

Subversion and Perforce follow a different model. There is a single logical
filesystem that you can address as subsets either for fetching/committing
or for branching. By convention, Subversion uses `trunk` as the name for a
mainline, `branches` as the root for your individual branches, and `tags`
as the root for all your tags, but you can have many projects in a single
Subversion repo, each with their own `trunk`, `branches` and `tags` directories.

In Subversion, this is implemented as a two-dimensional filesystem where one
axis is the revision number and the other is the pathname, with copy-on-write
to keep storage down to reasonable amounts, and use of diffs per-file to 
control revision storage, much like SCCS and RCS used deltas. However, Subversion
hides the actual storage behind a server, so it can change the implementation
over time as required.

In Perforce, instead of indexing by revision number, you index by changeset. But
in practice, this is the same thing under a different name, although inside the
implementation it's possible that changeset is a more rigorous construct, like
Darcs and its "theory of patches".

This conceptual model is simple, but has flaws. There is only tagging by convention;
nothing stops you from adding a commit to a "tag". In that case, strictly speaking,
it's now a branch, but it remains in the path you gave it, say
`/tags/blizzcon-1-build`. Even worse, it's quite easy by accident to commit
to multiple branches at the same time (and some people see this as a desirable
feature, in multi-project repositories). E.g. you can create a single branch
for multiple projects at the same time.

Subversion uses a skip-delta scheme that tries to walk a fine line between
CVS (reverse and forward deltas) and SCCS (interleaved deltas).

Perforce stores tags as metadata, not as entries in the filesystem. It added
streams at some point, which is a more rigorously defined branching model
(changes flow to the mainline, each stream has a parent stream)

Presumably, Subversion copied Perforce, since Perforce started in 1995 and the
Subversion project didn't start until 2000.

## Snapshot DAG

In some source control systems, notably Git but also Monotone (its inspiration),
Mercurial and others, the model is actually a set of
snapshots of trees, connected together in a DAG (directed acyclic graph).
A branch is just a copy of a tree, and trees can be merged
together as well; in fact, in Git, it is just as practical to merge together
10 branches as 2 - once the merge is done, further operations on the history
don't care how many parents a merge has.

In Git, every other artifact turns into an implementation detail, because the
tree and the following and encoding of trees is the primitive. As such, Git
repositories tend towards single-project use, since it's easy to branch the
entire repository and harder to branch a part of it. It's not impossible,
and over time Git has added the ability to do things with subtrees, such as
moving them up or down in the tree without losing the ability to merge, but
its origin is in whole-tree operations.

Git has shown that many of what were thought to be ideal primitives are in fact
a bad idea. Deltas aren't a first-class citizen, they should be employed
strictly as a time/space tradeoff. Even renames are inferred in Git, there is
no mechanism to track changes to specific files. What has worked well is that
many operations can be done directly on the DAG, without needing to look at
individual files.

What you lose is the more free-form ability given by "set of individual files"
or "arbitrary tree". This means that, even more than between SCCS and Subversion,
going between Subversion and Git means that extra data needs to be kept out of
band (or encoded in band) in order to save all the structure. It's not hard to
do a one-way transfer, but there are operations in Git that cannot be represented
directly in Subversion (n-way merges) and there are operations in Subversion that
cannot be represented directly in Git (mixed-revision checkouts, single-file
or subtree branches, and especially committing to multiple branches at the same
time). And perhaps the craziest thing in Subversion that is just flat-out illegal
in a DAG is that you can "branch" a subtree back to the root. This is an impossible
cycle in a DAG-based system but a legal (if bizzare) and non-breaking operation
in Subversion.

Monotone and Mercurial used a manifest as opposed to a tree of directory nodes.
In Monotone, the manifest is a changeset, whereas in Mercurial there are both
manifests and changesets. Monotone attempted a globally unique branch namespace
by proposing that branch names incorporated DNS names and warning when branch
names seemed to not follow this rule. Git avoids this by having branch names
be local per repo, and a user decides how to map branch names in remote repos
to branch names in the local repo.

TBD: this may not be entirely true for Monotone, Mercurial and others - verify.

## Theory of patches

Some source control systems believe that composing patches is the the single most
important thing, and so are designed around that as the conceptual model. The
most arrogant in this field is Darcs, which at times claims to be the only source
control system based on a strong theoretical backing.

## History

- 1970 - Clear/Caster
- 1972 - SCCS
- 1980 - DSEE
- 1982 - RCS
- 1985 - PVCS
- 1985 - MPW Projector/SourceServer (eol in 2001)
- 1985 - RCS paper released
- 1986 - CVS as a set of shell scripts
- 1990 - CVS written in C
- 1991 - QVCS (eol in 2010)
- 1991 - Aegis
- 1991 - TeamWare (eol in 2005)
- 1992 - ClearCase (descendent of DSEE)
- 1993 - Vesta
- 1994 - Visual SourceSafe
- 1995 - Perforce
- 1996 - CVS pserver (network access)
- 1998 - CVSNT (fork of CVS for better Windows NT support)
- 1998 - BitKeeper
- 2000 - Subversion development started
- 2001 - Arch/tla ("Tom Lord's Arch") (end-of-life in 2009)
- 2002 - AccuRev
- 2003 - ArX (Arch fork)
- 2003 - SVK
- 2004 - Subversion 1.0
- 2004 - ArX 2.0 incompatible with Arch
- 2004 - FastCST (never reached a final state, abandoned)
- 2005 - Team Foundation Server
- 2005 - Baz (Arch fork) came and went in one year
- 2005 - Bazaar development started (dying out in 2016)
- 2005 - Git development started, 1.0 released
- 2006 - Plastic SCM
- 2008 - Subversion 1.5 with decent merge support
- 2013 - Team Foundation Server distributed version

## Features

These are features that are desired by some or all users of source control.

- Atomic operations
- File locking
- Branching
- Merging
- Tags
- Secure
- Distributed operation
- Central/canonical repositories

## Notes on source control systems

### CLEAR/CASTER, SDC, CMS

Clear/Caster has a paper for it in 1970 that I haven't read; I assume as a system it dates back to
just before then. This appears to be unrelated to ClearCase, which was developed by Atria in
1992.

SDC, CMS.

### SCCS

- local version control
- single file history (projects are a fiction)
- branches, tags
- interleaved deltas to store multiple revisions

SCCS was developed in 1972 by Mark Rochkind, at Bell Labs, and is credited as the first source
control system (so maybe Clear/Caster wasn't?). It stores version history per file with interleaved
deltas, which means the entire revision file must be read to extract a specific version. In
later versions of SCCS, a recursive mode was added so that an entire tree of SCCS files could
be operated on at once. Interestingly, SCCS wasn't open-source until 2006.

I like the phrase I saw in a paper that called SCCS and RCS 'local version control': "Local version
control stores the history of the code base on the same disk storage used for the code under
development."

### RCS

Main architecture is pretty much identical to SCCS, with one big difference:

- deltas as the mechanisms of storing multiple revisions

RCS is a single-file source control systems, written as a reaction and proposed improvement over
SCCS, with first release in 1982. As in SCCS, each file has individual history, but delta patches
are used to store each version. Each version on the mainline is stored as a reverse delta from the
next most recent version, and branches are stored as forward deltas.

It's not strictly true that RCS is a per-file system; if you have a directory named `RCS`,
then you could operate on every file inside that RCS directory with a single command. However,
it's not really practical to use RCS in a multi-directory project.

The definition of how patches are created and interpreted is baked into the system of each. SCCS/RCS
are only of historical interest, but they had a lot of influence on systems that are alive today.

Specifically, while SCCS did invent and RCS popularize the base concepts of source control that we
use today, they are a dead-end because of some assumptions that sounded good at the time but turn
out to be wrong - specifically, that deltas are the optimal way to handle changes in a file or set
of files. This received heroic attention over the next 20 years but has been abandoned as not
efficient in both space and access time.

Also, to be clear, network access wasn't an issue because there really weren't networks
at the time; mainframes with terminal access were the rule.

RCS was closed-source until 1990, when it became open source. This is probably why it supplanted
SCCS and was the base for CVS, and not on its technical merits. This is a lesson for tools
developers - directly make money, or become the standard. In fact, very few closed-source tools
become dominant.

### CVS

Main architecture notes:

- server-based access
- whole-project-based (even though back-end storage uses RCS)
- vendor branches

CVS in one respect is a massive hack - it gave the user the ability to work
on sets of files with a single command, but under the hood was just a massive set of RCS revision
files. Thus, the features and design of CVS look remarkably like RCS, except that you can branch
across a set of files in one operation, or check out or commit a set of files, or get history
on a set of files.

It started out in 1986 as a set of shell scripts running RCS, then by 1990 was reimplemented
in C, and added network access in 1996.

I don't want to minimize the addition of a server - this is what made CVS be a credible source
control system and able to be used across a network or even across the Internet. There are
CVS repositories to this day, although no one sane would put a new project in CVS, and if
it were zero effort, people would migrate away from it.

More importantly, it added a cleaner user abstraction for branches.

For a brief moment in time (2002-2006), DCVS was an attempt to turn CVS into a distributed
system rather than a centralized system.

### Sourcesafe

Imagine a system halfway between RCS and CVS, and you have Sourcesafe. The client handles
checkin of multiple files at the same time, but there is no server. Instead, you put your
database in your file system, whether it's on your computer or a file server. That decision
led to easy implementation, but you can corrupt the repository quite easily, merely by
having a problem with file system access in the middle of a checkout.

Only mentioned because it had widespread use, but nothing new over CVS. And yes, VS 2005
added a client/server mode, but SourceSafe had already died its ignominious death by then.

### ClearCase

Main architecture notes:

- revisions as a view into the source control system (looking like the raw filesystem)
- revisions as a set of overlays

This was developed by Atria in 1992, and is based on an older system from Apollo called
Domain Software Engineering system. It used the clever-in-theory but clumsy-in-practice
metaphor of views into the revision tree - your working folder was a view mapped to the
specific revision of files that you wanted to work on. Persistent sync and performance
issues marred the illusion of seamlessly editing files on your system, however. The
other barrier to widespread use was high cost, not just for the software, but in operating
a ClearCase system; the statement was "one full-time sysop per 40 developers".

### TeamWare

Advanced for its time, but largely only used inside Sun.

- hierarchy of repositories
- atomic update of multiple files
- distributed operation (by copying and merging repositories)

This is a layer on top of SCCS.

### BitKeeper

Interestingly, BitKeeper is a layer on top of SCCS, or on top of a reimplementation of
SCCS. This is because Larry McVoy, the developer, came from working on TeamWare.

BitKeeper became open-source in 2016 - [bitkeeper.org](http://www.bitkeeper.org/)

## "Source Control" versus "Version Control" versus "Revision Control"

All three of these terms are equivalent:

- source control
- version control
- revision control

Source control as a phrase is geting more popular, and has now edged out
version control - revision control is a distant third as a phrase. See the
Google Ngram viewer:

[phrase: source control, version control, revision control](https://books.google.com/ngrams/graph?content=revision+control%2Cversion+control%2C+source+control&year_start=1980&year_end=2010&corpus=15&smoothing=3&share=&direct_url=t1%3B%2Crevision%20control%3B%2Cc0%3B.t1%3B%2Cversion%20control%3B%2Cc0%3B.t1%3B%2Csource%20control%3B%2Cc0)

So, if you like version control better as a phrase, use it or lose it. If you like
revision control as a phrase, that battle was lost from the start - give up and
pick one of the others.

## Reference

[SCCS: The POSIX standard Source Code Control System](http://sccs.sourceforge.net/)

[The Source Code Control System](http://www.basepath.com/aup/talks/SCCS-Slideshow.pdf)

[RCS—A System for Version Control](https://www.gnu.org/software/rcs/tichy-paper.pdf)

[Version Control Systems](http://people.ds.cam.ac.uk/fanf2/hermes/doc/talks/2012-04-staffsem/notes.pdf)

[ArX Distributed Version Control](http://www.nongnu.org/arx/codecon/img0.html)

[Comments on Open Source Software / Free Software (OSS/FS) Software Configuration Management (SCM) / Revision-Control Systems](https://www.dwheeler.com/essays/scm.html)

[Towards a Better SCM: Revlog and Mercurial](https://www.mercurial-scm.org/wiki/Presentations?action=AttachFile&do=get&target=ols-mercurial-paper.pdf)

["not rocket science" (the story of monotone and bors)](http://graydon.livejournal.com/186550.html)

[Monotone: A distributed version control system](http://www.monotone.ca/monotone.pdf)

[Version Control by Example](http://ericsink.com/vcbe/)

[Re: clarification on git, central repositories and commit access lists](https://lwn.net/Articles/246381/)

[The Great Migration: from TeamWare to Mercurial](https://www.slideshare.net/JamesCMcPherson/the-great-migration-from-teamware-to-mercurial-presentation)

[Perforce for Subversion Users](https://www.perforce.com/sites/default/files/pdf/perforce-for-subversion-users-guide.pdf)

[Comparison: Perforce and Subversion](https://www.perforce.com/sites/default/files/perforce_subversion.pdf)

[Perforce for Git users](http://stackoverflow.com/questions/17267218/perforce-for-git-users)

Wikipedia (note to self: fix all the source control pages)

- [RCS](https://en.wikipedia.org/wiki/Revision_Control_System). 1982-present
- [PVCS](https://en.wikipedia.org/wiki/PVCS). 1985-present
- [MPW Projector](https://en.wikipedia.org/wiki/Macintosh_Programmer's_Workshop). 1985-2001
- [QVCS](https://en.wikipedia.org/wiki/QVCS). 1991-2013
- [Vesta](https://en.wikipedia.org/wiki/Vesta_(Software_configuration_management)). 1993-present
- [Visual SourceSafe](https://en.wikipedia.org/wiki/Microsoft_Visual_SourceSafe). 1994-2005
- [Perforce](https://en.wikipedia.org/wiki/Perforce_Helix). 1995-present
- [CVSNT](https://en.wikipedia.org/wiki/CVSNT). 1998-present
- [BitKeeper](https://en.wikipedia.org/wiki/BitKeeper). 1998-present
- [Subversion](https://en.wikipedia.org/wiki/Apache_Subversion). 2000-present
- [Arch](https://en.wikipedia.org/wiki/GNU_arch). 2001-2009
- [DCVS](https://en.wikipedia.org/wiki/Darcs). 2002-2006
- [Surround SCM](https://en.wikipedia.org/wiki/Surround_SCM). 2002-present
- [Monotone](https://en.wikipedia.org/wiki/Monotone_(software)). 2003-present
- [Arx](https://en.wikipedia.org/wiki/ArX). 2003-2005
- [Darcs](https://en.wikipedia.org/wiki/Darcs). 2003-present
- [Vault](https://en.wikipedia.org/wiki/Vault_(version_control_system)). 2003-present
- [SVK](https://en.wikipedia.org/wiki/SVK). 2003-2010
- [Codeville](https://en.wikipedia.org/wiki/Codeville). 2004-2008
- [Git](https://en.wikipedia.org/wiki/Git). 2005-present
- [Team Foundation](https://en.wikipedia.org/wiki/Team_Foundation_Server). 2005-present
- [Bazaar](https://en.wikipedia.org/wiki/GNU_Bazaar). 2005-present
- [Mercurial](https://en.wikipedia.org/wiki/Mercurial). 2005-present
- [Fossil](https://en.wikipedia.org/wiki/Fossil_(software)). 2006-present
- [Veracity](https://en.wikipedia.org/wiki/Veracity_(software)). 2010-present

Needs a Wikipedia page

- [Aegis](http://aegis.sourceforge.net/)
- [FastCST](https://github.com/zedshaw/fcst)

Dead and buried

- OpenCM: no reference material online any more, and only indirect mentions of it.

## More notes

From [Ten-year timeline part 6: almost to the present](https://lwn.net/Articles/271120/)

> I didn't mean to imply that I invented the interesting ideas in monotone. Merely that it, as a
> social and researchy development project, both discovered a few fresh ideas and consolidated /
> refined many others, and has subsequently been a ripe source of ideas for its successors. I
> did some of the work, but also made a ton of mistakes; the key theoretical work we stumbled
> through during the course of monotone development was mostly the doing of others. Jerome
> Fisher, Nathaniel Smith, Derek Scherger, Bram and Ross Cohen, Timothy Brownawell, Christof
> Petig, Richard Levitte, Zack Weinberg, Peter Simons, Daniel Phillips, Emile Snyder, Markus
> Schiltknecht, Paul Crowley ... and a long list of others who I am probably implicitly
> insulting by not mentioning here (sorry, limited comment space).

Names to follow up on when tracking down the origin of both content-addressable and DAG
as pertains to source control.
