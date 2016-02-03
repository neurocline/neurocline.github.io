---
layout: post
title:  "Organizing Go source code part 2"
date:   2016-02-01 11:26:00 AM
categories: dev
tags: go programming
---

There are a handful of rules in Go that directly affect how the source code in your project is laid out.

When using the ```go``` tool:

* all the files in a directory must be in the same package
* all the files in a package must be in the same directory

The ```go``` tool, when iterating, assumes that all files in a directory are in the same package. You'll get a compile
error otherwise. When told to target a specific file, this is not the case, but this is not the normal usage of
the ```go``` tool. And from this consequence, all the files in a package need to be in the same directory.

One consequence of this is for executables - things you run. In Go, this is package main with func main().
This means that the top-level source for each executable should be in its own directory even if a single file, and must
be in its own directory if multiple files. In other words,
if you have a suite of programs built from one source base, then you'll have something like this

{% highlight go %}
binaries/
  binary1/
    main.go
  binary2/
    main.go
libs/
  package1/
    subpkg1/
    subpkg2/
  package2/
{% endhighlight %}

There is no language-level mandate that all the files in a package are in the same directory - this is a tooling
issue. On the other hand, it is the standard tooling at the moment.

Of course, this is not precisely true. Build constraints can prevent some files from being seen as part
of a package. And test code is not in the same package; files ending in _test.go often are in a package
*_test, and you'll see this code pulling in the directory it's in as a package.

# Kubernetes

Kubernetes, [https://github.com/kubernetes/kubernetes](https://github.com/kubernetes/kubernetes), is the largest Go
program I know of outside of the Go suite itself, at 460,000 lines as of this writing.

The basic source layout is this:

- API/protocol (swagger, in json) in ```api```
- binaries (package main) in ```cmd```
- build scripts in ```hack```
- packages in ```pkg```
- external source in ```Godeps``` (uses ```godep```)
- manually imported external source in ```thirdparty```

The source uses a short URL http://k8s.io that redirects to http://kubernetes.io. Does this further redirect
to https://github.com/kubernetes/? I'm not sure how that would work, though, unless you clone from k8s.io and not Github. In any event, there is a set of scripts in hack that are used to build and run Kubernetes from source.
See http://www.sebastien-han.fr/blog/2015/07/01/build-kubernetes-from-source/ for quick note on building.

This uses ```godep```, as most big projects are currently doing - it vendors external source into a
Godeps directory and rewrites your source to use the vendored paths.

There is a contrib folder that is the remnants of Kubernetes components that aren't part of the core system. These moved to their own repo.

# OpenShift Origin

Origin, [https://github.com/openshift/origin](https://github.com/openshift/origin), is fairly large - as of
this writing, it is 257,000 lines of Go code (ignoring whitespace and comment lines).

The basic source layout is this:

- API/protocol (swagger, in json) in ```api```
- main binaries (package main) in ```cmd```
- external source in ```Godeps``` (uses ```godep```)
- build scripts in ```hack```
- tool binaries in ```tools```

The build scripts in Kubernetes and Origin look similar. Not surprising, since OpenShift is built on top
of Docker and Kubernetes.

# CockroachDb

CockroachDb, [https://github.com/cockroachdb/cockroach](https://github.com/cockroachdb/cockroach), at 130,000 lines of code, is the largest Go program I've seen (outside of the Go tools themselves) that has no
substantial dependencies on other software.

The main binary is at the root of the repo - ```main.go```. There are a handful of other binaries in
a simulation directory, a handful of tools in the cmd directory, and a stray simulation file in the gossip
directory.

- build system in ```build```
- command-line suite in ```cli```

The repo is fairly flat, all the major components are at the top level; there is no src or pkg directory.

# Docker

Docker, [https://github.com/docker/docker](https://github.com/docker/docker), is also fairly large,
at 100,000 lines of Go code. It follows the Go 1.5 vendor method of doing external code.

- API expressed in Go code in ```api```
- add-ons (not part of the core) in ```contrib```
- main binary in ```docker```
- build scripts in ```hack```
- packages in ```pkg```
- external source in ```vendor``` (uses Go 1.5+)

# etcd

Medium sized at 60K lines of code.

Main binary at the root of the repo. Build scripts in the root of the repo. Big packages in directories at the root of the repo.

- Go client library for etcd in ```client``` (standalone package used by third party clients)
- main binary ```etcdmain``` (main.go in root just calls into etcdmain)
- external source in ```Godeps```
- packages in ```pkg```
