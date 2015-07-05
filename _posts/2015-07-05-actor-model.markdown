---
layout: post
title:  "The Actor model"
date:   2015-07-05 14:00:04
categories: programming
---

The actor model has held out a lot of promise over the past 40 years, but has
never been fully embraced by the software development community (unlike, say,
structured programming or object-oriented programming). There are a few modern
languages that facilitate Actor model programming; Erlang and Scala+Akka are the
two most popular.

In the Actor model, the only means to share state between concurrent and/or
parallel actors is message passing. In most other systems, access to state is
shared and synchronized by primitives like semaphores and mutexes. This does
not mean bliss and the lack of needing synchronization, because state can be
coupled even if it is not directly shared.

Some random links that I will sort through:

* [Actor model](https://en.wikipedia.org/wiki/Actor_model)
* [A gentle introduction to actor-based concurrency](https://practicingruby.com/articles/gentle-intro-to-actor-based-concurrency)
* [ActorsModel](http://c2.com/cgi/wiki?ActorsModel)
* [Actors are not a good concurrency model](http://pchiusano.blogspot.com/2010/01/actors-are-not-good-concurrency-model.html)
* [The Actor Model: Towards Better Concurrency](http://www.slideshare.net/drorbr/the-actor-model-towards-better-concurrency)
* [The Neophyte's Guide to Scala Part 14: The Actor Approach to Concurrency](http://danielwestheide.com/blog/2013/02/27/the-neophytes-guide-to-scala-part-14-the-actor-approach-to-concurrency.html)
* [An Introduction to Actor Model, with Examples in Akka](http://www.infoq.com/news/2014/10/intro-actor-model)
* [Erlang Style Actors Are All About Shared State](http://james-iry.blogspot.com/2009/04/erlang-style-actors-are-all-about.html)

Perhaps the reason the Actor model has never taken off is because state doesn't
compose well. This is why we have transactions, as evil as those are. Is there
another answer?
