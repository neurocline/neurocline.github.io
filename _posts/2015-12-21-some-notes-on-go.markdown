---
layout: post
title:  "Some notes on Go"
date:   2015-12-21 16:02:00 PM
categories: dev
tags: programming go
---

# GOPATH and source on your hard disk

This is the thing that's most annoying me about Go. Many of its proscriptive choices
are neutral to me, but dictating how your source code is laid on on the hard disk
is harder to work with.

The ```go``` tool finds files in relation to the ```$GOPATH``` environment variable,
and really wants to see a strucure like this at the root of ```$GOPATH``` (using one
of my recent projects as an example)

{% highlight bash %}
bin/
pkg/
src/
  github.com/
    catalog/
      ...
{% endhighlight %}

See [How to Write Go Code](https://golang.org/doc/code.html) for the official explanation.

On the other hand, I have been organizing projects on my hard disk thematically, and
partially by origin. And large projects (say, 1000 files and up), tend to develop
their own internal organization.

I like some of the thoughts behind the Go structure. I've always preferred systems where
generated files lived outside of your source files; source files come from source control,
and generated files are mostly temporaries. And many programming environments and languages
make this difficult. In the Go world, your source is all inside ```src/```, your intermediate
files are in ```pkg/```, and your output binaries are in ```bin/```. Of course, that's a
little limiting, but I can work in it.

One choice is to keep changing ```$GOPATH```. It "points to your workspace", but there's no
reason you need just the one workspace. I'm at the point where I'm seriously considering
this approach. Since I don't use an IDE at the moment for Go code, however, this means that
I need to remember to set ```$GOPATH``` each time I create a terminal window. This would be
necessary if I worked on large Go programs, or on dozens at the same time. A single ```bin/```
location would be a problem. The Go docs seem to recommend against this, see
[GOPATH](https://github.com/golang/go/wiki/GOPATH) from the [Go Wiki](https://github.com/golang/go/wiki).
I suspect this is advice bourne from working on lots of small programs, though.

There is one big reason to use multiple ```$GOPATH``` hierarchies, and that's when using ```go get```.
While Go will search among multiple paths for files, ```go get``` will download new files into
exactly one location.

But there are other problems I'm wrestling with. I have some multi-language repositories. So
I either relocate everything to suit Go, or I checkout the repo multiple times on the disk, or
I use a symlink. The symlink approach is awkward, the multiple checkout is confusing (editing
files in the wrong place is easy), and relocating everything to suit Go is very Go-centric.

# Relative and absolute Go paths

The ```go``` tool really likes absolute paths, and by absolute I mean paths that are relative
to the ```$GOPATH``` root.

If you use absolute paths to build, then your built output goes in the ```bin/``` folder. If
you use relative paths to build, then your built output goes next to the source. While that
doesn't sound bad, it also has implications for any ```import``` statements in your source.
Relative imports are forbidden if you use an absolute path to start. And if you have relative
source imports (which are nice and readable and convenient), then you must use a relative
path to trigger the build.

Given this plus the above, it sounds like the only sane choice is to build via relative path.
I note that some projects do this: [Netflix/rend](https://github.com/Netflix/rend) must be
built via relative paths.

# Versioning and Go source

```go get``` is useful, to a point. It can automatically fetch source packages for you.

What you don't get is control over a version. And this is going to be important to let build
servers automatically get and build projects, at least without a ton of pain.

[GO GET CONSIDERED HARMFUL](http://0x74696d.com/posts/go-get-considered-harmful/)

# Reference

[Golang.org](https://golang.org/)

* [About the go command](https://golang.org/doc/articles/go_command.html)
* [Command go](https://golang.org/cmd/go/)
