---
layout: post
title:  "Session state"
date:   2015-08-12 17:27:00 PM
categories: programming
---

Some kinds of network interations are best thought of in the context of a session that is
initiated, used, and then destroyed. The states in the session are represented by data
we store, usually at the "server" end, but sometimes both.

Once we have session state, however, we have to make sure that our session gets exclusive
access to this session state data, and that we can get access to it even if the underlying
connection changes, or even if the machines we are using as part of the session change
underneath us. For example, a server might crash or be restarted for maintenance reasons
in the middle of our session.

So we'd like to persist session state beyond any single machine, and yet not have this
be so expensive that sessions are slow. This means we need to store session state data on more
than just the server endpoint, and ideally this data is stored redundantly so that we are
fault-tolerant to normal failure cases (hardware fails, at inopportune times).

And we'd even like to entertain scalability far beyond what is possible now. At the moment, sessions
are typically tied to servers located in a single datacenter, but for both elasticity and for
reliability, we'd like to be able to move sessions not just between servers in a data center, but
between data centers.

[Session State: Beyond Soft State](http://research.microsoft.com/pubs/74713/ssm-nsdi.pdf). 2003
paper by Benjamin C. Ling, Emre Kiciman and Armando Fox, talking about a system they designed
called SSM, which is highly fault-tolerant, and designed towards the needs of session state.
This also includes some auto-tuning methods for detecting and handling load.

[The Case For RAMClouds: Scalable High-Performance Storage Entirely in DRAM ](http://delivery.acm.org/10.1145/1970000/1965751/p121-ousterhout.pdf). 2011 update of 2009 paper by John Ousterhout and his group at Stanford, making a case for RAM-only data storage that is still fault-tolerant.

[Fast Crash Recovery in RAMCloud](http://web.stanford.edu/~ouster/cgi-bin/papers/ramcloud-recovery.pdf).
2011. Describes RAMCloud's recovery technique. RAMCloud doesn't replicate data in RAM, instead it replicates
logs across many servers, allowing both redundant and fast recovery.

[Log-structured Memory for DRAM-based Storage](https://www.usenix.org/system/files/conference/fast14/fast14-paper_rumble.pdf).
2014. Describes RAMCloud's main innovation, log-structured memory.

[PNUTS: Yahoo!’s Hosted Data Serving Platform](http://perso.telecom-paristech.fr/~kuznetso/INF346/papers/pnuts.pdf).
2008 paper by Brian F. Cooper and company describing Yahoo's distributed database system. They decided
that eventual consistency was sufficient.

[Redis Sentinel](http://redis.io/topics/sentinel). From Redis documentation, describes how to use Redis in
a high-availability mode.

[Redis Cluster](http://redis.io/topics/cluster-spec). From Redis documentation, describes the new (as of 3.0)
clustering system allowing for a distributed implementation of Redis.

[roshi](https://github.com/soundcloud/roshi). Github repo. Distributed storage of timestamped events.

## Reference

[Session](https://en.wikipedia.org/wiki/Session_(computer_science)) definition from Wikipedia.
