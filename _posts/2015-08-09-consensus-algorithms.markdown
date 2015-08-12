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

Wikipedia has a decent article

* [Consensus](https://en.wikipedia.org/wiki/Consensus_(computer_science))

Note that state machine replication and consensus algorithms go together, and we'll be diving
into that as well, since that's the actual fun/hard work.

* [State machine replication](https://en.wikipedia.org/wiki/State_machine_replication) on Wikipedia.

More reading on consensus:

* [Chapter 14: Consensus and Agreement](http://www.cs.uic.edu/~ajayk/Chapter14.pdf), from the book Distributed Computing: Principles, Algorithms and Systems.
* [Distributed Systems, Failures, and Consensus](https://www.cs.duke.edu/courses/fall07/cps212/consensus.pdf), from a class by Jeff Chase at Duke University on distributed systems.
* [The Building Blocks of Consensus](http://www.cs.cornell.edu/home/rvr/papers/icdcn08.pdf). 2008.
* [Bounded Cost Algorithms for Multivalued Consensus Using Binary Consensus Instances](http://research.microsoft.com/en-us/people/weic/ipl_bincons.pdf).
* [A Simple Proof of a Simple Consensus Algorithm](http://www.cs.utexas.edu/users/psp/consensus.pdf). 1990?
* [Randomized Protocols for Asynchronous Consensus](http://www.cs.yale.edu/homes/aspnes/papers/randomized-consensus-survey.pdf). 2002.
* [The correctness proof of Ben-Or’s randomized consensus algorithm](http://www.cs.toronto.edu/~samvas/teaching/2221/handouts/benor-paper.pdf). 2011.

And Papers We Love has a whole section on distributed systems

[papers-we-love/distributed_systems](https://github.com/papers-we-love/papers-we-love/tree/master/distributed_systems)

## 2PC - two-phase commit

Conceptually, two-phase commit is straightforward; the commit-request phase (or call it the voting
phase), and the commit phase, where the coordinator decides whether to commit or not, based on the
information gathered in the voting phase.

This is a widely-used technique, although it has problems in the presence of failures. E.g. it assumes
there is storage that can be trusted at each node, that no node crashes, and that nodes can communicate
with each other. And it's a blocking protocol. Other than that, it's great :)

* [Two-phase commit protocol](https://en.wikipedia.org/wiki/Two-phase_commit_protocol) from Wikipedia.
* [Consensus Protocols: Two-Phase Commit](http://the-paper-trail.org/blog/consensus-protocols-two-phase-commit/). 2008.

## 3PC - three-phase commit

Fixes much of fragility of 2PC, although it's still vulnerable to partitions. E3PC tries to address
the problem of partitions.

* [Three-phase commit protocol](https://en.wikipedia.org/wiki/Three-phase_commit_protocol) from Wikipedia.
* [A Formal Model of Crash Recovery in a Distributed System](https://www.cs.purdue.edu/homes/bb/cs542-11Spr/TSE83_SS.pdf). 1983, Skeen and Stonebraker, introduced the three-phase commit protocol.
* [Consensus Protocols: Three-phase Commit](http://the-paper-trail.org/blog/consensus-protocols-three-phase-commit/). 2008.
* [Crash Recovery in a Distributed Data Storage System](http://research.microsoft.com/en-us/um/people/blampson/21-crashrecovery/acrobat.pdf). 1979, Butler Lampson and Howard Sturgis.
* [Increasing the Resilience of Distributed and Replicated Data Systems](http://webee.technion.ac.il/~idish/Abstracts/jcss.html). 1998, Idit Keidar and Danny Dolev, enhanced three-phase commit.

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

* [The Raft Consensus Algorithm](https://raftconsensus.github.io/). Home page on Github.
* [Raft](https://en.wikipedia.org/wiki/Raft_(computer_science)) on Wikipedia.
* [In Search of an Understandable Consensus Algorithm](/assets/raft.pdf). 2014, Osterhout paper introducing Raft.
* [The Secret Lives of Data](http://thesecretlivesofdata.com/raft/)
* [Raft refloated - do we have consensus](http://www.cl.cam.ac.uk/~ms705/pub/papers/2015-osr-raft.pdf). 2015.
* [Consensus Protocol](https://www.consul.io/docs/internals/consensus.html). Consul uses Raft for
its consensus protocol.
* [In Search of an Understandable Consensus Algorithm](https://www.usenix.org/conference/atc14/technical-sessions/presentation/ongaro) video of presentation at Usenix 2014.
* [ARC: Analysis of Raft Consensus](http://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-857.pdf). 2014, Heidi Howard.
* [logcabin](https://github.com/logcabin/logcabin/). LogCabin is a distributed storage system built on Raft; written in C++.

Facebook is using Raft in HydraBase, to replace HBase. CoreOS is using Raft in etcd.

* [https://github.com/coreos/etcd](https://github.com/coreos/etcd)

## Ark

Introduced for TokuMX and MongoDB.

* [Ark: A Real-World Consensus Implementation](http://arxiv.org/pdf/1407.4765v1.pdf). 2014.
* [Introducing Ark: A Consensus Algorithm for TokuMX and MongoDB](https://www.percona.com/blog/2014/07/18/introducing-ark-a-consensus-algorithm-for-tokumx-and-mongodb/).
* [Explaining Ark Part 2: How Elections and Failover Currently Work](https://www.percona.com/blog/2014/07/25/explaining-ark-part-2-how-elections-and-failover-currently-work/)
* [Explaining Ark Part 3: Why Data May Be Lost on a Failover](https://www.percona.com/blog/2014/07/30/explaining-ark-part-3-why-data-may-be-lost-on-a-failover/)
* [Explaining Ark Part 4: Fixing Majority Write Concern](https://www.percona.com/blog/2014/08/07/explaining-ark-part-4-fixing-majority-write-concern/)

## Names in consensus algorithms

Major names

* [Leslie Lamport](http://www.lamport.org/). Also wrote TLA+, and LaTeX.
* [Jim Gray](http://research.microsoft.com/en-us/um/people/gray/). Two-tier transaction commit semantics, described ACID semantics.
* [Butler Lampson](http://research.microsoft.com/en-us/um/people/blampson/). One of the key names for Xerox PARC and the Alto.
* [Barbara Liskov](http://www.pmg.csail.mit.edu/~liskov/).

Implementors

* [Harry Brundage](http://harry.me/)
* Henry Robinson [LinkedIn](https://www.linkedin.com/in/henrynrobinson), [Paper Trail](http://the-paper-trail.org/), [henryr on Github](https://github.com/henryr) - Cloudera, ZooKeeper.
