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

# Kubernetes

Kubernetes, [https://github.com/kubernetes/kubernetes](https://github.com/kubernetes/kubernetes), is the largest Go
program I know of outside of the Go suite itself, at 460,000 lines as of this writing.

This uses ```godep```, as most big projects are currently doing - it vendors external source into a Godeps directory and rewrites
your source to use the vendored paths.

# OpenShift Origin

Origin, [https://github.com/openshift/origin](https://github.com/openshift/origin), is fairly large - as of this writing,
it is 257,000 lines of Go code (ignoring whitespace and comment lines). It's interesting to look at, because
it is a suite containing a lot of independent executables. There are 6 executables in the origin/cmd package, some example programs
in origin/examples, a bunch of tool executables in origin/tools, and then a handful of other programs.

This uses ```godep```.
