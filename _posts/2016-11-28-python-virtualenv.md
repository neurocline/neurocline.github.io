---
layout: post
title:  "Python virtualenv"
date:   2016-11-28 12:11:00 PM
categories: dev
tags: python
---

Lately I use virtualenv reflexively, because big packages have lots of dependencies. It's
mildly annoying, I wish version conflicts didn't happen, but that's life. This happens 
at the OS level, but it's epochal - the transition from DOS to Windows, say, or from
Mac OS 9 to Mac OS X.

That said, there is an advantage to using virtualenv, and that is that it makes it
a little easier to use multiple major versions of Python on the same machine, because
activating a virtualenv environment fiddles with paths.

I have two main reasons for using virtualenv. First, I actually do have conflicts from
time to time with package versions, and I also do need to run both Python 2 and Python 3
(sigh). But the second reason is equally nice - I can install something to try it out, and
if it fails, I just trash a virtualenv directory. This is also why people use Docker, and
why things like Vagrant or Puppet also started seeing widespread use; it's easy to screw
things up when everything is installed globally, so until operating systems get fixed, use
systems like this to pretend to have a global system, or to hide the pain of constantly
re-installing. For Python, we have `pip freeze` and `pip install -r` to somewhat automate
that, and Ruby has Bundler.

# Overview

The basic operations are these

- Create a virtualenv environment based on a physical Python install; this is essentially a
new custom Python install.
- Activate a specific virtualenv environment
- Deactivate the current virtualenv environment

I don't think this is a stack, but it might be (I've never experimented).

That's it. Best practice would probably be to put all these virtualenv directories
in one place, maybe alongside the physical Python installs, because the virtualenv
folders point back to your physical installs; you can't just copy these to a new system.

They are fairly small, on the order of 20 MB, so not too painful to have lots of them, and
they only take a few seconds to create.

# Windows

Using virtualenv on Windows is a little different than on Unix (Linux/Mac). Creating is
pretty much the same, but activating is a little different.

Note: My command prompts are using the `$` character because the syntax highlighter I'm
using doesn't work for DOS/Powershell style consoles. I'll fix that at some point. I also
transcribed `\\` to `/` for the same reason. Microsoft really needs to abandon `\\` as 
the path separator char.

For the following, I have two versions of Python installed in these global directories:

* `C:/Python27`
* `C:/Python35`

## Create new virtualenv environment

The simple version is this - create a new virtual environment into the directory `matplotlib`.

{% highlight console %}
$ virtualenv matplotlib
Using base prefix 'C:/python35'
New python executable in C:/w/matplotlib/Scripts/python.exe
Installing setuptools, pip, wheel...done.
$
{% endhighlight %}

If you want, you can specify a specific version. For running Jekyll, I need Python 2.7
(because I'm using Pygments).

{% highlight console %}
$ virtualenv -p C:/Python27/python.exe py-jekyll
Using base prefix 'c:/python35'
New python executable in C:/w/py-jekyll/Scripts/python.exe
Installing setuptools, pip, wheel...done.
$
{% endhighlight %}

By default, the packages you already had installed in your base Python version are not carried
over into your new version. This is a good thing, but it means you'll have to install packages.

## Activate environment

From whatever command prompt you're going to issue further Python commands from:

{% highlight console %}
$ cd matplotlib
$ Scripts/activate
(matplotlib) $
{% endhighlight %}

Note that the prompt changes to remind you which virtual environment you have active.

If you're going to install packages, make sure you do this first. All this does is fiddle with
the environment variables to put your `virtualenv` Python version first.

## Deactivate environment

Since this is Windows, you could just close the command window. Or you can deactivate the
environment to get back to whatever you had set globally:

{% highlight console %}
(matplotlib) $ deactivate
$
{% endhighlight %}
