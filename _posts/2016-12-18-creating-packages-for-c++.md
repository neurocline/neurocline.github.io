---
layout: post
title:  "Creating packages for C/C++"
date:   2016-12-18 1:11:00 PM
categories: dev
tags: C++
---

If only we had a package manager for C++. By this I mean something that C/C++ developers on
any operating system could use; packages that can only be used from Red Hat don't do Mac
or Windows or even Debian developers any good.

But that's not actually the problem - if only all the libraries we collectively need
were available as packages.

There are a dozen C++ package managers now. Each of them has 10-50 packages. And then that's
usually about it. Since any single package manager lacks all the packages one needs, no
package manager gets widespread adoption.

This is not a chicken-and-egg situation. And it's not that none of the package managers are
all that terrible. None are great, but many are usable.

The problem needs to be attacked from the other end. Instead of designing a package manager
and then starting to assemble packages for it, just make all the packages. All the packages.
Thousands and tens of thousands of them. Actually, millions, because source is constantly
updated to keep it working and add features.

This either means an army of people spending a collective hundred thousand hours,
or a (semi) automated tool.

I started on this; just barely. Here are some notes towards the system I think is needed.

## The basic idea

Something like this:

{% highlight bash %}
$ make-package curl
{% endhighlight %}

and out pops a package for curl. This implies several things.

First, that we can take the string ``curl`` and find curl source with it. This could be

* looking on Github or another source repository.
* looking in a ``make-package`` forwarding list
* searching in Google

Then, we need to analyze what we downloaded, whether it is a Git repo, tar/zip file, or
some other collection.

Since purely automated analysis is unlikely to result in a usable package for all but the
most trivial of projects, we will have to add a little human input from time to time.

Once we know how to assemble our package, we do so, in whatever format of the day is desired.

Then, repeat, for all versions of all projects that we wish as packages.

There is a common engine fronted by one or more input systems and backed by one or more output
systems.

## Input systems

Git - Github, Gitorius, Gitlab, arbitrary Git upstreams.

Launchpad - https://launchpad.net/

Sourceforge, although no one cares any more, do they?

Maybe every Linux distribution can serve as an upstream?

Private company source control systems.

FTP sites.

Google can help us find things, but it will likely require human sifting. I assume eventually
that there will be a master repository - not of source, but of pointers to source.

## Analysis

Jumping to the end, package source will have metadata, a manifest, that instructs how to
assemble the source into a package. That is best kept with the source, but that's clearly
not going to happen right away. So we keep it and associate it with the source on need. Someday,
maybe, once we have packages for everything, package authors will take over the maintenance
of the package metadata. But we will not count on that.

In fact, this is the key. We have to do all of it and keep it running without expecting
anyone else to participate. Oddly enough, I think that's the best way to guarantee
that everyone else does join in.

## Output systems

We are very definitely not going to presume the one true package format. That is just as mad
as trying to have everyone adopt a package metadata format.

For this, we embrace a polyglot nature. Whatever package format is desired, we write a generator
for it. This is actually not very hard. Every existing package format to date is pretty simple,
it's some kind of simple container with one or more manifest files embedded. This is very close
to how compilers work - the compiler itself can be generic, with several backends to generate
code in the appropriate format, and several front-ends to parse text into syntax trees.
