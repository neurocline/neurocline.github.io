---
layout: post
title:  "A Survey of Programming Language Package Systems"
date:   2016-02-09 12:20:00 PM
categories: dev
tags: package language
short-description: A survey of the major packaging systems used in programming languages, from CTAN in 1992 to Rust in 2013; in order to support the next generations of packaging systems, we want to know what made previous ones successful.
---

version 1, 2016/01/25

C++ lacks a standardized or even widely used packaging system. Other languages and platforms have prospered in large part due to their packaging system. In point of fact, CTAN, the first package archive, gets the credit for a lot of TeX's success. In that case, it wasn't the package system per se, it was the fact that there was a global package archive that was relatively easy to use; so call it the forerunner of more integrated things like pip or cargo.

I'm not going to cover Linux distribution packaging systems; those have a slightly different nature to them, although there are some valuable lessons to be learned there. I'm also only going to cover heavily used systems. These are in rough order of introduction to the world.

- (1992) TeX - CTAN
- (1995) Perl - CPAN
- (2003) Python - PyPI, easy_install, pip
- (2003) Ruby - RubyGems, gem
- (2003) Java - Maven, Ivy, jpm, OSGi
- (2005) Haskell - Cabal
- (2009) Node.js - npm
- (2009) Go - goget/goinstall
- (2010) Windows - NuGet
- (2011) Objective-C - CocoaPods
- (2013) Rust - Crate
- (2013) C++ - biicode -> Conan, cpm, ghp

As a side-note the Debian package manager dpkg came into existence sometime between January 1994 and March 1995, so it probably didn't have any influence over CPAN. Interview Ian Jackson.

# CTAN (TeX) - 1992

The TeX language has had CTAN, the Comprehensive TeX Archive Network, going back to 1992. It was the direct inspiration for CPAN, the Perl package archive.

CTAN has two main features - it can be searched with full-text search, and there's a web interface to view directories and files, and to download files and whole directories.

New or updated packages are submitted to the archive curators for publishing. There is no direct publishing of packages, but the curation process is fairly light. In 2015, uploads averaged about 100/month.

Architecture-wise, there is a core site that does all the management/curation (this is where uploads are accepted). Then there is a network of mirrors that automatically sync their site with the core site. Currently, there are about 94 mirror sites worldwide, and the main sites average about 6 TB per month in traffic.

## New features introduced by CTAN

- central and global archive that anyone can use
- full-text search
- browsing

## Closely related

- CRAN - R (1997; has integrated but manually-driven tooling)
- CEAN - Erlang

# CPAN (Perl) - 1995

The Perl community came up with CPAN over the span of 1993-1995, and can be said to be active as of late 1995 (it went public in October 1995). Many people credit at least half of the success of Perl to CPAN.

CPAN followed CTAN in spirit, but has an automatic upload mechanism, important since there are over 150,000 modules in CPAN as of mid-2015. It also introduced explicit tooling, rather than relying on a web browser, for both package upload and download. Most users interact with CPAN through a command-line or GUI package manager, including some limited searching. There is a fairly standard format for how distributions are made (the name for a package), but it changes over time and there are no strict rules as far as I can tell.

While package uploading is not gated, it does take a week or more to get a PAUSE account; this is manually reviewed, I guess to have some level of accountability in terms of what can be added to CPAN.

CPAN, due to size, also has some structure; there are 24 top categories, distributions can be browsed by author, and distributions can be browsed by their namespace hierarchy. And also due to size, there are multiple search engines, rating systems, forums, and a testing process that attempts to smoke test and validate all packages on CPAN. Like CTAN, there are a lot of mirrors

Another big factor for CPAN was that documentation in modules can be browsed online. There is a site called AnnoCPAN that has all the documentation for modules, and facilitates user-contributed annotations to the documentation.

The CPAN code can be run by anyone. There are sometimes private, company, or individual sites that just host an organization's packages, perhaps due to political reasons or that the packages are seen to be too specialized to submit to CPAN itself. The tooling allows you to pull from one or more CPAN sites, just like Linux package systems.

Despite that, CPAN is an example of a central and global archive, and the vast majority of all public packages are uploaded to CPAN itself.

## Perl Modules

Using a Perl module is straightforward:

{% highlight perl %}
use Digest::MD5 qw(md5)
 
$digest = md5("calculate md5 on this string")
{% endhighlight %}

A Perl module is a single file on disk. This works well for Perl, since it is largely an interpreted language, and this makes factoring your own programs into modules pretty straightforward (you just create a folder hierarchy). For the above module, there is a file MD5.pm in a folder named Digest in the perl module path. There is no recursive include; each use statement points to a single .pm file.

For the most part, shared modules are published to CPAN. There is tooling to create and push modules to CPAN, and then to pull modules from CPAN to your system. This is an offline process and does not happen automatically when running a Perl program. For example, assuming that Digest::MD5 were not a built-in module, you would install it to your Perl distribution like this:

{% highlight bash %}
$ cpan Digest::MD5
{% endhighlight %}

The cpan tool is built into Perl distributions, and has lots of bells and whistles that may or may not be ignorable. The new hotness is evidentally cpanminus, which is not yet bundled into Perl distributions.

Perl modules can contain compiled code, and since most module distributions are source code, parts of the module are compiled at module install time. This is a complication that is handled by distribution tools, but it does make iterating on such modules a little painful.

Perl has some versioning support; when you use a module, you can specify what version it needs to be. It won't find that version of the module, but it will complain at runtime if the wrong version was found.

## New features introduced by CPAN

- User-driven submission/upload process (no gating)
- Command-line and GUI tooling
- Private and custom archives
- Community-driven testing, documentation, and annotating

## Syntax in detail

- use module version list
- use module version
- use module list
- use module

Perl uses the term package for the thing itself, and then module for using it; the two need to match, and generally this also means match the path on disk, although you can play games with the symbol table to get around that. Perl makes everything from a module available to access via its package namespace. It can also import zero or more symbols into the current namespace. There is no syntax to rename package namespaces, e.g. no way to handle two different packages that have a namespace collision. Package namespaces are not hierarchical; declaring package MD5 inside package Digest does not create Digest::MD5. The nesting is purely a manual organizational tactic.

{% highlight perl %}
use Digest::MD5 2.01 qw(md5); # error if Digest::MD5 not at least version 2.01, just import md5 symbol
use Digest::MD5 qw(md5); # any version, just import md5 symbol
use Digest::MD5 2.01; # error if Digest::MD5 not at least version 2.01, do default imports
use Digest::MD5 qw(); # any version, don't import anything to current namespace
use Digest::MD5; # do default imports
{% endhighlight %}

Caveat on versions; version-handling can be done in a per-module way. The default behavior is to croak if the version asked for is greater than the module version, but a module can implement any version-checking it desires. However, the caller gets to supply just one version number, not a range. But it would be possible for modules to implement semantic versioning.
To translate this into C++ namespace terms, doing use on a package means that the entire namespace is now available to the code at that point. Imports are equivalent to ```use namespace <import>```,which would import one or more symbols from one namespace into another namespace. There is no way to hide symbols from fully-qualified access in Perl modules.

# PyPI and Pip (Python) - 2003

Python has a mixed history. There was really nothing from language inception until 2000. Distutils, the first tooling to create packages, was added to the Python standard library in 2000. There was no official repository/archive until 2003, which is why PyPI (the Python Package Index) was introduced. And there was no built-in tooling until 2014, when Pip shipped with Python 3.4 (although pip itself has existed since 2008, as an alternative to easy_install). There were tools, and they worked, but they required effort from users to configure and use. Pip existed, but it had to be downloaded and installed, and this was challenging for many users. EasyInstall could download and install from PyPI, but it couldn't list local packages or update them as a group.

As a result, while Python has long since outstripped Perl in popularity, there are far fewer package uploaded to PyPI than to CPAN (72000 packages on PyPI as of end of 2015 versus 200,000 on CPAN). This may change now that Pip is part of the official distribution and Setuptools and related are getting updated via the work of the Python Packaging Authority.

At its start, PyPI was more of a location service and less of a centralized repository; packages "found" on PyPI might be hosted elsewhere. In 2014, this functionality was deprecated in favor of explicit multiple repositories; pip and setuptools support the concept of multiple repositories, so behind-the-scenes redirects are now phased out. This is in general a good thing, and [PEP 0470](https://www.python.org/dev/peps/pep-0470/) explains it more clearly. It's a cautionary tale for others.

Amazingly, Python is a counter-example to the idea that packaging systems are a large part of the reason for the success of languages; Python arguably had the worst packaging support of any popular language sans C/C++. But maybe this is the counterexample that proves the rule; Python and C/C++ are great languages, so succeeded on their own merits, whereas Perl, Ruby, Javascript etc needed the boost from package systems in order to build a viable developer base.

## Python packages

Using a Python package is straightforward

{% highlight python %}
import hashlib
print hashlib.sha1("calculate sha1 on this string").hexdigest()
{% endhighlight %}

A Python package is either a single file or a folder hierarchy with a specific layout. Just like with Perl, import names are turned into paths. In the case above, there is a file hashlib.py in the Python package path. Modules can also be folders; a folder is a package if there is a magic file __init__.py in the folder root. Modules can also be recursively included, and __init__.py can be used to guide what parts are imported by default and which need their own individual import.

For the prepackaged Python modules, the vast majority are single-file modules. Unlike Perl, a module is either a single file or a folder - even though the filesystem allows it, you cannot have MODULE.py and MODULE/ in the same folder (I imagine it would just pick the single-file one). For user-level code, modules-as-folders seems to be more common.

Getting a Python package is now simple, since pip is finally in the default distribution. Assuming hashlib were not already installed, you would get it as follows:

{% highlight bash %}
$ pip install hashlib
{% endhighlight %}

The package manager Matthew Versluys created allowed Python code to automatically pull down its own packages if not present. Since it was an addon to Python and not altering the Python language, it required extra work to bootstrap the package manager, then use it, then pull down packages (packages are cached, so little work is done if a package is already present).

{% highlight python %}
import urllib
import sys
sys.path.append(urllib.urlretrieve('http://package.battle.net/package.zip')[0])

import package.use
package.use.Package('Foo', '1.1.1')
 
import foo
 {% endhighlight %}

Python has very weak versioning support for packages. At install time, you can fetch specific versions of packages. At runtime, however, you have to check for versions yourself after the module is imported, and some packages lack the semi-standard __version__ attribute.

[PyPI - the Python Package Index](https://pypi.python.org/pypi)

# RubyGems and gem (Ruby) - 2003

RubyGems is Ruby's package manager. Packages in Ruby are called gems and the tool to work with them is called ```gem```. [https://rubygems.org/](https://rubygems.org/) is the Ruby community's gem hosting service. Like with Python, RubyGems came well after Ruby. It was created at the end of 2003 and added to the standard library in Ruby 1.9 in 2007.

The ```gem``` tool follows a pattern similar to apt-get, portage, yum and npm; there is a command-line tool with verbs to install, uninstall, list (local or remote), search and build.

To use a package

{% highlight ruby %}
require 'digest/md5'
print Digest::MD5.hexdigest('That string again')
To install a package:
gem install digest
 {% endhighlight %}

[http://guides.rubygems.org/](http://guides.rubygems.org/)

# Java - 2003

The picture here is muddled. We have Ant as a build tool, which has the Ivy dependency manager as an integrated component, which is more like a bootstrap system (download packages and add to your project's lib directory). Ivy can pull from Maven repositories, and probably should be looked at as a Maven-based tool. [http://ant.apache.org/ivy/features.html](http://ant.apache.org/ivy/features.html).

There is Maven, which is both a replacement for Ant as a build tool, and then a dependency manager/package downloader. It's not a bootstrap system, it can download packages at build time, and has a browser to find projects at [http://search.maven.org/#browse](http://search.maven.org/#browse). There is also a common global repository at [http://mvnrepository.com](http://mvnrepository.com).

Maven is too complicated to describe here. You need an XML file. It is far simpler than Ant, but still runs to thousands of lines of XML or more for a large project.

There is JPM4J, which is an attempt to make a jvm tool that is behaviorally like npm, the Javascript package manager. [https://www.jpm4j.org/#!/](https://www.jpm4j.org/#!/). It has a centralized repo and claims to have >1 million JARs from 150,000 projects in it.

There is bndtools, which is from the OSGi initiative/organization.

[http://stackoverflow.com/questions/2710266/is-there-a-package-manager-for-java-like-easy-install-for-python](http://stackoverflow.com/questions/2710266/is-there-a-package-manager-for-java-like-easy-install-for-python)

Note that the first steps were taken back in 1996 - the JAR file was proposed at roughly the same time as the public launch of Java 1.0. However, JAR files have numerous problems. [https://en.wikipedia.org/wiki/Java_Classloader#JAR_hell](https://en.wikipedia.org/wiki/Java_Classloader#JAR_hell).

There's a standard being proposed (first proposed in 2011 to be part of Java 7), but at this point is deferred until at least Java 9. [https://en.wikipedia.org/wiki/Java_Module_System](https://en.wikipedia.org/wiki/Java_Module_System).

# Cabal (Haskell) - 2005

It's actually HackageDB  + Cabal + cabal-install.

[https://ivanmiljenovic.wordpress.com/2010/03/15/repeat-after-me-cabal-is-not-a-package-manager/](https://ivanmiljenovic.wordpress.com/2010/03/15/repeat-after-me-cabal-is-not-a-package-manager/)

# npm (Node.js) - 2009

Specifically, this is a Node.js package manager, not a generalized Javascript package manager. It's also one of the newest package managers in existence. It came into existence about a year after Node.js was first released, and is bundled with the Node.js system as of Node.js 0.6.3 (Nov 2011).

[https://www.npmjs.com/](https://www.npmjs.com/)

# goget/goinstall (Go) - 2009

Part of Go from the beginning. Integrated with the tool suite, but not with the compiler.

Go packages are source code that is compiled with your code as part of the build process; packages can be precompiled as libraries depending on how you are building your Go program, or can be built each time.

Using a Go package is straightforward

{% highlight go %}
package main
 
import "crypto/sha1"
import "fmt"
 
fmt.Printf("% x", sha1.Sum(data))
{% endhighlight %}

Fetching Go packages can be done manually, or with a tool that inspects code. If you know the path of the package you want, you can just pull it down, and this can be recursive: the below pulls down all the sample source from the Go Programming Language book by Kernighan&Donovan:

{% highlight bash %}
$ go get gopl.io/...
{% endhighlight %}

But go get can also scan your source code and pull down packages that aren't present on your local machine.

{% highlight bash %}
$ go get -u /path/to/src
{% endhighlight %}

Despite the "update" name of the flag, this will not update versions of packages already on the local system, but it will scan source and fetch missing dependencies. This is not a default action.

Like with Perl and Python, even system libraries are accessed as packages. A Go package is a file or folder on disk. There is no privileged structure to a folder that is a package, other than that all the source files in the package must be in the same package namespace. Like with source, Go simply compiles all files named .go in your build tree.

Unlike with Python or Perl, the Go package namespace is flat - there is no hierarchy. For crypto/sha1, the only hierarchy is that the sha1 package is stored in a folder named crypto. Doing import crypto will not include the sha1 package. Package names in source code do not need to match the leaf folder name; this is a convention. Specifically, the path in source code is called the import path; the package name itself is determined by the package declaration in the code. The intention is that import paths are globally unique, but the actual meaning of an import path is left to the Go tools.

There is no global Go package repository, but Go has made it easy to pull directly from a Git repository. This means that big public systems like Github can be used to hold packages, and in fact Github is the defacto "Go repository" at the moment.

The down side is that there is no direct support for versioning of packages. An import from [github.com](https://github.com) will pull from master, which is problematic for repeatable builds. As a result, third-party tooling has created the idea of vendoring, or the pulling in of dependencies directly to your code, and then a way to manage that. This is still evolving in Go, in fact. More direct support for the vendoring approach was added in as of Go 1.5, and I expect this part to continue to evolve.

# Nuget (Windows) - 2010

Microsoft does have Nuget, with the Chocolatey front-end, as a package manager. It's not really a programming language package manager, but is a hybrid in between a distribution package manager and a dependency/build tool package manager.

# Crate (Rust) - 2013

Part of Rust starting with the third wave of development that began in 2012. Very integrated with the compiler.

Rust, like Go, is somewhat dogmatic about how you lay out your source code. This reduces the amount of config. In both Go and Rust, there is no need for a makefile. Unlike Go, Rust does have a manifest file, but it has a tool to create and maintain it for you; I assume it's an explicit file so that you can edit it if you have needs that can't be automatically inferred.

To get a package, you need to do several things. You need to declare a dependency in the .toml file

{% highlight bash %}
[dependencies]
regex = "0.1.41"
{% endhighlight %}

Then in your source code you pull in that library (Rust calls everything a crate). But you also have to use a module out of that crate, even when the crate contains just one module

{% highlight c++ %}
extern crate regex
use regex::Regex
{% endhighlight %}

It all comes across as being a bit verbose. The reason is that crates can have an arbitrary number of modules. And Rust gives you a lot more control over versions than Go does. But these are in two separate places and there's duplication to boot.

There is goodness here. For example, the dependencies section can point to a repository revision or branch. And there's a separate Cargo.lock file that records what specific dependency was fetched, so that you can have repeatable builds - this Cargo.lock file becomes source that gets committed. And they give you a mechanism to override dependencies so that you can fix a bug in a library deep down in the dependency chain - you use package A which uses package B which uses package C, and package C has a bug. You can override the use of package C and substitute your own version for it, fairly easily.

[https://doc.rust-lang.org/book/crates-and-modules.html](https://doc.rust-lang.org/book/crates-and-modules.html)

# C++

I list systems here, but none are widely used, and none are general solutions, instead being a specific build system with a package manager.

Of these, biicode came the closest, but it is in the process of shutting down, since it was trying to be a commercial company. Also, it was built on top of CMake, and requires you to use CMake to get most of the benefit. While this is not unlike Maven (which is a build system and a package system together), the C++ world is far more polyglot, and telling Visual Studio developers they must use CMake is perhaps not the way to go. [https://www.biicode.com/](https://www.biicode.com/).

The people behind biicode went on to make Conan, which is less tied to CMake (but still, if you don't use CMake, it's not all that useful). [https://www.conan.io/](https://www.conan.io/).

CPM is "C++ package manager". This is heavily tied to CMake. Source code: [https://github.com/iauns/cpm](https://github.com/iauns/cpm). Repository browser: [http://www.cpm.rocks/](http://www.cpm.rocks/).

ghp is GitHub Premake Packages, a Premake extension to consume packages from Github repositories. [https://github.com/mversluys/premake-github-package](https://github.com/mversluys/premake-github-package). Also, it's not really tied to C++, and can be used as a package manager for multiple languages. Its direct parent is used for Python as well as C++.

# General Reference

[The Zen of Comprehensive Archive Networks](http://www.cpan.org/misc/ZCAN.html)

[What is CTAN?](https://www.ctan.org/ctan/)

[CPAN](https://en.wikipedia.org/wiki/CPAN)

[Python Packaging Authority](https://www.pypa.io/en/latest/)

[Cargo, Rust’s Package Manager](http://doc.crates.io/)

[Package Manager Rosetta Stone](https://docs.google.com/spreadsheets/d/1qV-EV6JkLNk2SKzIL0dccMb5yxiazRq2WNE8hPbujeE/edit#gid=0)

[The Servo Browser Engine](https://github.com/servo/servo) - GitHub repo containing Servo, Mozilla browser written in Rust.
