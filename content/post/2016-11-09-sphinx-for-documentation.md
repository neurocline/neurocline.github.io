---
categories: dev
date: 2016-11-09T10:00:07Z
tags: python
title: Sphinx for documentation
url: /2016/11/09/sphinx-for-documentation/
---

Periodically I re-hunt for something better to write with - blogs and books, but
most of all technical documentation. I have been using Markdown and Jekyll for a
blog, and have long since run into limitations: lots of blog articles make the
Jekyll generator take a long time, Liquid markup sucks, Markdown itself is too
limited for what I want.

OK, on the long time bit. I have about 100 articles in my _posts folder. It
takes Jekyll about 10 seconds to run now, and if I preview with `jekyll serve`,
it takes 10 seconds to pick up a change. That's unacceptable, given that it's
supposed to be incremental. It's written in Ruby, which is part of the reason
it's slow, and also holds me back from wanting to fix it (I have no interest
in learning Ruby).

So now I'm writing technical docs with Sphinx and reStructuredText. I'm liking
it for now, but the true test will be in the long run, but also "can I write a
book with it?". I cannot conceive of writing a book in Markdown.

I did use TeX a long time ago, but I found that I got lost in the code when
I was writing large things. And that was with a relatively friendly program
like Bluesky TexTures. I was a TeX fan in the 1980s, but while it made awesome
strides compared to everything else in the 1970s and 1980s, it just didn't stick
for me as something I could write in.

Likewise with FrameMaker. I loved FrameMaker and used it successfully for a long
while, but then Adobe basically abandoned the Mac version and I fell out of love
with it. Also, the direction that FrameMaker took went in a direction different
than what I was interested in.

Sphinx is best set up in a virtualenv, because it pulls in a lot of things. And
since I've never used virtualenv before, here's my crib notes for Windows.

## Create new virtualenv environment

{{< highlight bash >}}
C:\w>virtualenv sphinx
Using base prefix 'c:\\python35'
New python executable in C:\w\sphinx\Scripts\python.exe
Installing setuptools, pip, wheel...done.
{{< / highlight >}}

## Activate environment

{{< highlight bash >}}
C:\w>cd sphinx
C:\w>Scripts\activate
(sphinx) C:\w\sphinx>
{{< / highlight >}}

The activate script alters your prompt to remind you that you are in the virtualenv
environment.

## Installing packages into the virtual environment

Once you've activated the environment, any new packages just go into that environment.
That's the entire point and reason for using virtualenv.

{{< highlight bash >}}
(sphinx) C:\w\sphinx>pip install sphinx
Collecting sphinx
  Using cached Sphinx-1.4.8-py2.py3-none-any.whl
Collecting snowballstemmer>=1.1 (from sphinx)
  Using cached snowballstemmer-1.2.1-py2.py3-none-any.whl
...
Building wheels for collected packages: MarkupSafe
  Running setup.py bdist_wheel for MarkupSafe ... done
  Stored in directory: C:\Users\bfitz\AppData\Local\pip\Cache\wheels\a3\fa\dc\0198eed9ad95489b8a4f45d14dd5d2aee3f8984e46862c5748
Successfully built MarkupSafe
Installing collected packages: snowballstemmer, imagesize, alabaster, MarkupSafe, Jinja2, pytz, babel, Pygments, six, colorama, docutils, sphinx
Successfully installed Jinja2-2.8 MarkupSafe-0.23 Pygments-2.1.3 alabaster-0.7.9 babel-2.3.4 colorama-0.3.7 docutils-0.12 imagesize-0.7.1 pytz-2016.7 six-1.10.0 snowballstemmer-1.2.1 sphinx-1.4.8
{{< / highlight >}}

## Deactivate environment

You could just close the cmd shell you have open, or you could deactivate the virtualenv

{{< highlight bash >}}
(sphinx) C:\w\doc>deactivate
C:\w\doc>{{< / highlight >}}

## Using Sphinx

The Sphinx documentation isn't very clearly written. Or rather, it is good reference documentation,
but it's a little hard to get started.

[Sphinx](http://www.sphinx-doc.org/) documentation can be found starting here. 1.4.8
is the latest version as of this moment.

The quickstart at [First Steps with Sphinx](http://www.sphinx-doc.org/en/1.4.8/tutorial.html)
has you run `sphinx-quickstart`. This asks a ton of questions that you're not likely to be 
sure on the answers. Worse, it's not very friendly on Windows, it seems to assume that
terminal escape codes work (not the case on cmd.exe for the most part).

I ran it from within my newly created doc folder and said "." for the root path. I then
answered everything else to the best of my knowledge.

In the end, all that gets created is a text config file named `conf.py`. It's commented
liberally along the model of Apache and other programs with lots of config. Usefully,
it has a lot of the options in the file, and if they don't apply, they are commented out.

The defautl use is HTML, but it supports LaTeX output, and that is useful for feeding to
a TeX-to-PDF converter.

All this is the simple part. The most important step is "how do I add stuff", and then "how do
I turn this into something others can read".

### Adding text

The index.rst file in the root of your documentation tree is the top page and the master
index all in one. My simple one looks like this

{{< highlight bash >}}
.. Provenance documentation master file, created by
   sphinx-quickstart on Mon Nov  7 13:56:13 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Provenance's documentation!
======================================

Contents:

.. toctree::
   :maxdepth: 2

   intro
   github-info
   references

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

{{< / highlight >}}

This is the top-level page, so anything that's not a directive will show up in your
output.

Each entry in toctree is the name of another file to process, ending in .rst. These are
relative paths and for a big project you'll have some organization to it. For now I have
a handful of files.

The absolute coolest thing about Sphinx is that it creates a search page for your
documentation.

### Building

The long-form is to run `sphinx-build -b html . _build`. There are some convenience
makefiles that work for most use cases. Just running `make html` is enough for
generating your HTML site into the `_build` folder (or whatever you named as your
output). The site is in `_build\html` and since it's making a web site, there is
a `_build\html\index.html` that is the root page.

## Further thoughts

Sphinx isn't incremental like Jekyll, so I'm not sure how happy I'll be writing a blog
in it. But I like restructuredText far more than Markdown.

# Reference

[Sphinx cheat sheet](http://matplotlib.org/sampledoc/cheatsheet.html)

[Restructured Text (reST) and Sphinx CheatSheet](http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html)

[Invocation of sphinx-quickstart](http://www.sphinx-doc.org/en/latest/invocation.html)
