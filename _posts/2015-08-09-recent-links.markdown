---
layout: post
title:  "Recent Links"
date:   2015-08-09 11:49:00 AM
categories: dev
tags: distributed, consensus
---

[Deconcentration of Attention: Addressing the Complexity of Software Engineering](http://deconcentration-of-attention.com/) - interesting article on the complexity of software systems and the thinking that is required, and promoting the idea of deconcentration.

[post-mortems](https://github.com/danluu/post-mortems) - a hopefully growing collection of software project post-mortems, most of them modern.

[Performance without the event loop](http://dave.2heney.net/2015/08/08/performance-without-the-event-loop) - good if basic talk on scalable software architecture and how Go fits in, mostly about network dispatch.

[gb, a project based build tool for the Go programming language](http://dave.cheney.net/2015/06/09/gb-a-project-based-build-tool-for-the-go-programming-language) - describes a project-centric build tool, meaning all your source (including vendor code) is present but clearly separated.

[Ask HN: Books with a high signal to noise ratio?](https://news.ycombinator.com/item?id=10027102) - a surprisingly good curated list of books.

[How We're Predicting AI&mdash;or Failing To](https://intelligence.org/files/PredictingAI.pdf) - article by Stuart Armstrong and Kay Sotala analyzing all the (wrong so far) predictions about AI.

[MIRI blog](https://intelligence.org/blog/) - blog from Machine Intelligence Research Institute. Also see papers.

A kerfluffle between Steven Pinker and Nicolas Nassim Taleb over Pinker's book The Better Angels of
Our Nature (Pinker comes off the better):

- [The "Long Peace" is a Statistical Illusion](http://www.fooledbyrandomness.com/longpeace.pdf) - Taleb's attack on the book and Pinker.
- [Fooled by Belligerence](http://stevenpinker.com/files/pinker/files/comments_on_taleb_by_s_pinker.pdf) - Pinker responds to Taleb broadside.

[HydraBase&emdash;The evolution of HBase@Facebook](https://code.facebook.com/posts/321111638043166/hydrabase-the-evolution-of-hbase-facebook/).

[ZooKeeper vs. Doozer vs. Etcd](http://devo.ps/blog/zookeeper-vs-doozer-vs-etcd/) - evaluating systems used to share configurtion across a cluster of servers. With [commentary on Hacker News](https://news.ycombinator.com/item?id=6366665). TLDR: ZooKeeper complex and written in Java implementing Paxos, Doozer is dead, etcd is a very young project written in Go and implementing Raft.

[Apache ZooKeeper: the making of](https://developer.yahoo.com/blogs/hadoop/apache-zookeeper-making-417.html).

Youtube uses Zookeeper: see [package zkocc](http://godoc.org/code.google.com/p/vitess/go/zk/zkocc).

[Raft lecture](https://www.youtube.com/watch?feature=player_detailpage&v=YbZ3zDzDnrw#t=1481) on Youtube from John Osterhout. Probably more videos, look for them.

[Arakoon](http://arakoon.org/) is another distributed key-value store.

[Open-Source Service Discovery](http://jasonwilder.com/blog/2014/02/04/service-discovery-in-the-cloud/). Covers Zookeeper, Doozer and Etcd, but also looks at other solutions.

[Good tech talks on Youtube](https://news.ycombinator.com/item?id=9791739)

[A Hacker's Guide to Risk](https://media.defcon.org/DEF%20CON%2023/DEF%20CON%2023%20presentations/Speaker%20&%20Workshop%20Materials/Bruce%20Potter/DEFCON-23-Bruce-Potter-Hackers-Guide-to-Risk.pdf) Talk at DefCon.

[Seaweed-FS](https://github.com/chrislusf/seaweedfs). Simple and highly scalable distributed file system. Based off of Haystack.

[Finding a needle in a Haystack: Facebook's photo storage](https://www.usenix.org/legacy/event/osdi10/tech/full_papers/Beaver.pdf).

[C++ Internals :: STL vector, Part III](http://www.gahcep.com/cpp-internals-stl-vector-part-3/).

[MQTT](http://mqtt.org/) is a machine-to machine connectivity protocol, intended to be very lightweight.

[utf8rewind](https://bitbucket.org/knight666/utf8rewind/) - the size of it seems insane.

[Working Hard to Keep It Simple](https://www.youtube.com/watch?v=3jg1AheF4n0) 2011 talk by Martin Odersky.

[Userland Slab Allocator](https://github.com/bbu/userland-slab-allocator). Uses bitvectors, looks interesting.

[Verdi proves Raft correct](https://github.com/uwplse/verdi/pull/16). Verdi is a framework for formally verifying distributed systems using Coq. This is the pull request that contains the Raft verification.

[Call me maybe: Zookeeper](https://aphyr.com/posts/291-call-me-maybe-zookeeper)

[The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)

[Distributed systems for fun and profit](http://book.mixu.net/distsys/single-page.html)

[Practicalities of Productionizing Distributed Systems](https://www.youtube.com/watch?v=BKqgGpAOv1w) Talk by Jeff Hodges at RICON West 2013.

[Semaphores in Plan 9](https://swtch.com/semaphore.pdf).

[How to make a book with Pollen](http://pollenpub.com/).

[Let's Build a Simple Interpreter. Part 2.](http://ruslanspivak.com/lsbasi-part2/).
