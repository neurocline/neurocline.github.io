---
layout: post
title:  "Consensus algorithms"
date:   2015-08-09 03:18:00 AM
categories: programming
---

I'm going to start talking about data replication, and the first and probably most
important topic is that of consensus algorithms. I don't want to regurgitate what
others have written, so at first this is going to be a reading list. But, at some
point, it will contain original bits, and then all of this will turn into a bibliography.

## What went before

Here are a handful of the papers I think are important.

* [Time, Clocks and the Ordering of Events in a Distributed System](http://research.microsoft.com/en-us/um/people/lamport/pubs/time-clocks.pdf). 1978 - brilliant or obvious, or both, this provided a more formal description of partial orders (which is all we get in our universe where relativity applies).

## Consensus algorithms

This sub-field was launched with a paper from Lamport titled The Byzantine Generals Problem.

* [The Byzantine Generals Problem](http://research.microsoft.com/en-us/um/people/lamport/pubs/byz.pdf). 1982 - making progress in the presence of conflicting information.

Note that state machine replication and consensus algorithms go together, and we'll be diving
into that as well, since that's the actual fun/hard work.

* [State machine replication](https://en.wikipedia.org/wiki/State_machine_replication) on Wikipedia.

## 2PC - two-phase commit

Yep.

## 3PC - three-phase commit

Fixes the fragility of 2PC.

* [Consensus Protocols: Three-phase Commit](http://the-paper-trail.org/blog/consensus-protocols-three-phase-commit/). 2008.

## Paxos

This was developed by Leslie Lamport in the late 1980s, circulated informally in the
community in the early 1990s, and finally formally published in Transactions on
Computer Systems in 1998. It is an improvement over 2PC and 3PC (two-phase commit and
three-phase commit).

Paxos has the reputation of being complex, and developers are often warned against
implementing it themselves. This seems a little draconian or elitist to me.

These are in publication order, but are starred with a reading order for those wanting
to understand Paxos.

* [Paxos - Wikipedia](https://en.wikipedia.org/wiki/Paxos_(computer_science)). 2015 - recent version.

Papers

* [The Part-Time Parliament](http://research.microsoft.com/en-us/um/people/lamport/pubs/lamport-paxos.pdf). 1998 - the first Leslie Lamport paper on the Paxos algorithm.
* [Paxos Made Simple](http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-Simple.PDF). 2001 - another paper from Lamport explaining Paxos, presumably in a fashion easier for some people to understand.
* [Cheap Paxos](http://research.microsoft.com/pubs/64634/web-dsn-submission.pdf). 2004 - a version of Paxos that is cheaper (less work) when processors aren't constantly failing.
* [Fast Paxos](http://msr-waypoint.com/pubs/64624/tr-2005-112.pdf). 2005 - paper by Lamport proposing a faster version of Paxos that learns in two message delays instead of three (but error-intolerant).
* [Generalized Consensus and Paxos](http://research.microsoft.com/pubs/64631/tr-2005-33.pdf). 2005 - allow parallel execution of non-interfering commands.
* [Stoppable Paxos](http://research.microsoft.com/en-us/um/people/lamport/pubs/stoppable.pdf). 2008.
* [Vertical Paxos and Primary-Backup Replication](http://research.microsoft.com/en-us/um/people/lamport/pubs/vertical-paxos.pdf). 2009 - an exploration of the idea that valid master/slave replica systems are a variant of Paxos.
* [Leaderless Byzantine Paxos](http://research.microsoft.com/en-us/um/people/lamport/pubs/disc-leaderless-web.pdf). 2011.

Blog articles

* [Consensus Protocols: Paxos](http://the-paper-trail.org/blog/consensus-protocols-paxos/). 2009 - A fairly clear article from the Paper Trail blog on Paxos.
* Understanding Paxos [Part 1](https://distributedthoughts.wordpress.com/2013/09/22/understanding-paxos-part-1/), [Part 2](https://distributedthoughts.wordpress.com/2013/09/30/understanding-paxos-part-2/). 2013 - pair of articles on Paxos.
* [Neat Algorithms - Paxos](http://harry.me/blog/2014/12/27/neat-algorithms-paxos/). 2014 - Javascript implementation from Harry Brundage.
* [Paxos Made Moderately Complex](http://www.cs.cornell.edu/courses/cs7412/2011sp/paxos.pdf). 2015 - paper from Robbert van Renesse and Deniz Altinbuken talking about full reconfiguratble multidecree Paxos.

Uses of Paxos

* Google: the [Chubby](http://static.googleusercontent.com/media/research.google.com/en//archive/chubby-osdi06.pdf) distributed lock service ([BigTable](http://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf) uses Chubby).
* Yahoo and ZooKeeper.
* Heroku and Doozerd.
* Amazon Web Services.
* Cassandra and Nutanix.
* [libpaxos-cpp](http://www.leonmergen.com/libpaxos-cpp/)

## Raft

Raft was developed as an easier-to-understand consensus algorithm, easier that Paxos, that is.

* [Raft](https://en.wikipedia.org/wiki/Raft_(computer_science)) on Wikipedia.
* [The Secret Lives of Data](http://thesecretlivesofdata.com/raft/)
* [Raft refloated - do we have consensus](http://www.cl.cam.ac.uk/~ms705/pub/papers/2015-osr-raft.pdf). 2015.

Facebook is using Raft in HydraBase, to replace HBase.

## Names in consensus algorithms

Major names

* [Leslie Lamport](http://www.lamport.org/). Also wrote TLA+, and LaTeX.
* [Jim Gray](http://research.microsoft.com/en-us/um/people/gray/). Two-tier transaction commit semantics, described ACID semantics.
* [Butler Lampson](http://research.microsoft.com/en-us/um/people/blampson/). One of the key names for Xerox PARC and the Alto.
* [Barbara Liskov](http://www.pmg.csail.mit.edu/~liskov/).

Implementors

* [Harry Brundage](http://harry.me/)
* Henry Robinson [LinkedIn](https://www.linkedin.com/in/henrynrobinson), [Paper Trail](http://the-paper-trail.org/), [henryr on Github](https://github.com/henryr) - Cloudera, ZooKeeper.
