---
categories: dev
date: 2016-01-15T22:27:00Z
tags: go programming
title: Organizing Go source code
url: /2016/01/15/organizing-go-source-code/
---

How should your Go project source be organized?

# What are others doing?

Here are some big Go projects on GitHub (found via the search string "language:go size:>=100000"). The line
counts are reported with [cloc](https://github.com/AlDanial/cloc), I've made an effort to exclude
vendored code embedded in the project repo, and I'm not looking at non-Go files.

- Go: [https://github.com/golang/go](https://github.com/golang/go)
  - 4021 files, 704959 code lines, 114329 comment lines, 94409 blank lines
- Kubernetes: [https://github.com/kubernetes/kubernetes](https://github.com/kubernetes/kubernetes)
  - 2027 files, 464331 code lines, 64270 comment lines, 54067 blank lines
- OpenShift Origin: [https://github.com/openshift/origin](https://github.com/openshift/origin)
  - 1439 files, 257173 code lines, 44010 comment lines, 30589 blank lines
- CockroachDb: [https://github.com/cockroachdb/cockroach](https://github.com/cockroachdb/cockroach)
  - 518 files, 134545 code lines, 27682 comment lines, 14479 blank lines
- vitess: [https://github.com/youtube/vitess](https://github.com/youtube/vitess)
  - 868 files, 132356 code lines, 19420 comment lines, 18665 blank lines
- golang/tools: [https://github.com/golang/tools](https://github.com/golang/tools)
  - 599 files, 112023 code lines, 27344 comment lines, 17631 blank lines
- Docker: [https://github.com/docker/docker](https://github.com/docker/docker)
  - 910 files, 109523 code lines, 15632 comment lines, 20870 blank lines

Here are some medium-sized projects:

- golang/net: [https://github.com/golang/net](https://github.com/golang/net)
  - 315 files, 80168 code lines, 7270 comment lines, 6137 blank lines
- influxdb: [https://github.com/influxdata/influxdb](https://github.com/influxdata/influxdb)
  - 238 files, 71848 code lines, 8578 comment lines, 13009 blank lines
- Flynn: [https://github.com/flynn/flynn](https://github.com/flynn/flynn)
  - 419 files, 69501 code lines, 3980 comment lines, 10233 blank lines
- etcd: [https://github.com/coreos/etcd](https://github.com/coreos/etcd)
  - 394 files, 61454 code lines, 9712 comment lines, 8159 blank lines
- Consul: [https://github.com/hashicorp/consul](https://github.com/hashicorp/consul)
  - 214 files, 56563 code lines, 6802 comment lines, 9344 blank lines
- Packer: [https://github.com/mitchellh/packer](https://github.com/mitchellh/packer)
  - 691 files, 49973 code lines, 4359 comment lines, 10614 blank lines
- syncthing: [https://github.com/syncthing/syncthing](https://github.com/syncthing/syncthing)
  - 255 files, 31833 code lines, 4756 comment lines, 6771 blank lines
- gogs: [https://github.com/gogits/gogs](https://github.com/gogits/gogs)
  - 125 files, 29071 code lines, 2539 comment lines, 5524 blank lines
- godropbox: [https://github.com/dropbox/godropbox](https://github.com/dropbox/godropbox)
  - 173 files, 25494 code lines, 3116 comment lines, 5400 blank lines
- Terraform: [https://github.com/hashicorp/terraform](https://github.com/hashicorp/terraform)
  - 145 files, 22831 code lines, 2320 comment lines, 3976 blank lines

Smaller projects, at least Go-code wise:

- lime-backend: [https://github.com/limetext/lime-backend](https://github.com/limetext/lime-backend)
  - 139 files, 19970 code lines, 1515 comment lines, 2349 blank lines
- Lantern: [https://github.com/getlantern/lantern](https://github.com/getlantern/lantern)
  - 411 files, 16328 code lines, 19169 comment lines, 15722 blank lines
- Serf: [https://github.com/hashicorp/serf](https://github.com/hashicorp/serf)
  - 110 files, 15804 code lines, 1982 comment lines, 3249 blank lines
- go-github: [https://github.com/google/go-github](https://github.com/google/go-github)
  - 112 files, 13698 code lines, 2210 comment lines, 3090 blank lines
- kati: [https://github.com/google/kati](https://github.com/google/kati)
  - 39 files, 11090 code lines, 844 comment lines, 1005 blank lines
- Drone: [https://github.com/drone/drone](https://github.com/drone/drone)
  - 126 files, 9996 code lines, 1154 comment lines, 1823 blank lines
- hashicorp/raft: [https://github.com/hashicorp/raft](https://github.com/hashicorp/raft)
  - 33 files, 6091 code lines, 1364 comment lines, 1315 blank lines

# Lots of code in package main

There are no modules or classes or other namespace systems in Go, outside of packages, so there's a
temptation to make packages just to create namespaces. But it feels like packages that aren't reusable
subsystems aren't really packages.

One answer is to just have lots of code in package main. There's always a ton of code that is unique
to your program; for example, the specific command-line processing that you have.

# Other notes

[Excellent Open Source Go Projects](http://herman.asia/open-source-go-projects-to-learn-from)

[Go Report Card](https://github.com/gophergala/go_report)

[Go Projects index](https://github.com/golang/go/wiki/Projects)

[Architecture for a Golang Web App](https://larry-price.com/blog/2015/06/25/architecture-for-a-golang-web-app)

[Generic programming in Go using "go generate"](http://www.onebigfluke.com/2014/12/generic-programming-go-generate.html)

I assume you've read at least these

* http://blog.golang.org/organizing-go-code
* http://blog.golang.org/godoc-documenting-go-code
