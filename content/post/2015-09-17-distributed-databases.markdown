---
categories: dev
date: 2015-09-17T13:06:00Z
tags: database, distributed
title: Distributed Databases
url: /2015/09/17/distributed-databases/
---

What are our options if we want a distributed, fault-tolerant database?

## MySQL Cluster (NDB)

It's very widely used at scale.

## Cassandra

Cassandra was written initially at Facebook, strongly influenced by Dynamo,
Amazon's key/value store. It was then moved to be an Apache projec in 2010. Cassandra has been
used at very large scale. The main challenge is that it has an eventual consistency model, which
can be challenging to work with (Facebook moved from Cassandra to HBase due to this).

* [Cassandra](http://cassandra.apache.org/) home page.
* [Dynamo: Amazon’s Highly Available Key-value Store](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
* [Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra) page on Wikipedia.
* [Hulu Chooses Cassandra Over HBase and Riak](http://www.datacenterknowledge.com/archives/2014/07/31/hulu-cassandra-vs-hbase-vs-riak/)

## Riak

Riak also follows the thread from Dynamo, and is a key-value store written in Erlang. Riak can do in-memory
or disk storage, or both. Riak is very fault-tolerant but not as fast as something like Redis, leading to the
idea that perhaps Riak is the back-end for a Redis cache?

* [Riak](http://basho.com/products/) home page and [docs](http://docs.basho.com/riak/latest/).
* [Riak](https://en.wikipedia.org/wiki/Riak) page on Wikipedia.
* [Not So Versus, Riak Versus Redis](http://compositecode.com/2013/02/10/riak-redis/)
* [My Year of Riak](http://inaka.net/blog/2011/08/25/when-to-use-riak/) from 2011.
* [Riak Compared to Cassandra](http://docs.basho.com/riak/1.3.1/references/appendices/comparisons/Riak-Compared-to-Cassandra/) potential bias since this is from Riak.
* [Riak versus Cassandra](http://basho.com/posts/technical/riak-vs-cassandra/)

## HyperDex

HyperDex is a research project from Cornell turned commercial. I think this project started in 2010, but
it seemed to be at a usable state by 2012.

* [HyperDex](http://hyperdex.org/) home page and [papers](http://hyperdex.org/papers/)
  * [HyperDex: A Distributed, Searchable Key-Value Store](http://hyperdex.org/papers/hyperdex.pdf)
* [Emin Gün Sirer](https://www.cs.cornell.edu/people/egs/)
* [Robert Escriva](http://rescrv.net/)
* [HyperDex](https://en.wikipedia.org/wiki/HyperDex) page on Wikipedia

## MongoDB

We should not use MongoDB.

## HBase

HBase is implementation of Google's BigTable started by Powerset. It moved to the Apache Foundation in 
2009, and is part of the Hadoop project; in fact, it runs on top of the Hadoop File System.

Facebook started using HBase in 2010 for their new messaging platform, and have now forked it into
HydraBase. One key thing was the switch to RAFT for the consensus algorithm.

* [HydraBase – The evolution of HBase@Facebook](https://code.facebook.com/posts/321111638043166/hydrabase-the-evolution-of-hbase-facebook/)

## Redis Cluster

Not sure.

## Cockroach DB

This will be awesome someday

* [Cockroach Labs](http://www.cockroachlabs.com/)
* [cockroachdb](https://github.com/cockroachdb) GitHub organization.
* [Out in the Open: Ex-Googlers Building Cloud Software That’s Almost Impossible to Take Down](http://www.wired.com/2014/07/cockroachdb/) 2014 Wired Article
* 

## General notes

[Call Me Maybe - Jepsen](https://aphyr.com/tags/Jepsen)

[Challenges to Adopting Stronger Consistency at Scale](https://www.usenix.org/system/files/conference/hotos15/hotos15-paper-ajoux.pdf)

[Visual Guide to NoSQL Systems](http://blog.nahurst.com/visual-guide-to-nosql-systems)

[Cassandra vs MongoDB...](http://kkovacs.eu/cassandra-vs-mongodb-vs-couchdb-vs-redis)

[Distributed systems for fun and profit](http://book.mixu.net/distsys/single-page.html)
