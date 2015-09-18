---
layout: post
title:  "Recent Links"
date:   2015-09-14 11:32:00 AM
categories: dev
tags: service, microservice
---

[Enterprise Web Development: From Desktop to Mobile](http://enterprisewebbook.com/). This is an online version of the [print book](http://www.amazon.com/Enterprise-Web-Development-Building-Applications/dp/1449356818). I think the whole book is online even though the table of contents only points to prolog and epilog material. For example, I ran across [Chapter 8](http://enterprisewebbook.com/ch8_websockets.html) on WebSockets while doing a Google search, and once I looked at the [GitHub source](https://github.com/Farata/EnterpriseWebBook) for the book, I was able to create the other URLs. I don't think the text is hidden on purpose.

[AsciiDoc](http://www.methods.co.nz/asciidoc/) has been used to write a number of technical books. Maybe I should try this out. It appears to be a more human-writable version of DocBook. Still, this is more useful to writing structured docs than anything else, I bet.

[Service Discovery: Zookeeper vs etcd vs Consul](http://technologyconversations.com/2015/09/08/service-discovery-zookeeper-vs-etcd-vs-consul/). This is in the context of Docker.

[XRay the LLVM\Clang source code](http://www.codergears.com/Blog/?p=1069) is interesting, but mostly because I should be looking more closely at tools like [CppDepend](http://www.cppdepend.com/) and [CQLinq](http://cppdepend.com/cqlinq), which lets you make LINQ calls on the code database that CppDepend builds up.

[Libcox](http://libcox.net/)

[RAET: Reliable Asynchronous Event Transport Protocol](https://github.com/RaetProtocol/raet)

[Kubernetes from the ground up: the API server](http://kamalmarhubi.com/blog/2015/09/06/kubernetes-from-the-ground-up-the-api-server/). Second in a series about [Kubernetes](http://kubernetes.io/), an open-source cluster manager (from Google). It uses Etcd.

[Under the Hood of Amazon EC2 Container Service](http://www.allthingsdistributed.com/2015/07/under-the-hood-of-the-amazon-ec2-container-service.html)

[Service discovery with Docker](http://adetante.github.io/articles/service-discovery-with-docker-1/)

[Distributed systems for fun and profit](http://book.mixu.net/distsys/single-page.html). A book on distributed systems that's quite good as an introduction to the concepts and challenges.

[Mixu/markdown](https://github.com/mixu/markdown-styles) converts Markdown to HTML. This and [Ghost](https://github.com/mixu/ghost-render) look better than Jekyll.

[Visualizing Algorithms](http://bost.ocks.org/mike/algorithms/). Awesome.

[Linkers](https://lwn.net/Articles/276782/) 20 part series by Ian Lance Taylor for LWN.

[Managing Software Complexity in Models](https://insights.sei.cmu.edu/sei_blog/2015/09/managing-software-complexity-in-models.html)

[How to Elect a Leader Faster than a Tournament](http://arxiv.org/pdf/1411.1001v2.pdf). Significant advance in leader election.

[Dark corners of Unicode](http://eev.ee/blog/2015/09/12/dark-corners-of-unicode/)

[Stack, Heap, Pool](http://bulldozer00.com/2015/09/14/stack-heap-pool/). This "shows" that pools are 33x faster than stack allocation. However, it's cheating. Object initialization is being billed to
"pool" construction, which is all the time.