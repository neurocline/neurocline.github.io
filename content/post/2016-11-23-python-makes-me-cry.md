---
categories: dev
date: 2016-11-23T04:45:00Z
tags: python
title: Python makes me cry
url: /2016/11/23/python-makes-me-cry/
---

I don't hate Python. If I hated it, I wouldn't use it. But every time I start using Python
for serious work, I run into silly problems.

For example, I used Python 2.x for a long time, but finally switched to Python 3.x because
it was pretty much impossible to correctly handle localized text - you can mostly handle it,
but if you want to be perfect, the edge cases get in your way. But then, once I switched to
Python 3, I remembered that I ran programs that didn't like Python 2. So then I had to do
shenanigans to get things to run, like mentioned here:

[Multiple Versions of Python on Windows](http://testerstories.com/2014/06/multiple-versions-of-python-on-windows/)

The main issue is that Python emphasizes quick turnaround at the cost of other factors.
For many small problems, this is great. If you're writing software you just want to use
for weeks, months or years, this is not so great. I find myself constantly fiddling with
things.

That said, Zeb Shaw went off the deep end when he wrote this.

[The Case Against Python 3](https://learnpythonthehardway.org/book/nopython3.html)

He may or may not learn the lesson that many others have - you make think you are
"exaggerating for effect', whereas your opposition will call you a liar, or an idiot,
or other very unkind things. Zeb advances some arguments that are silly, in his attempt
to have a very strong argument.

Instead, my issue is that I want it all - I want a language where I can quickly run and
test code, and I want to produce programs that just run, on anyone's machine, or on my
machine years later. I have Mac programs that I wrote in 1988 that still work just fine.
I have Windows programs that I wrote in 1995 that still work just fine. I have lots of
Python, Perl and Ruby code that stop working months after written, because there's too
much interaction with the machine environment.

The correct answer is a staticly typed language with a kick-ass package system and
a compiler that's universally available, where you distribute both binary and source, and
source can be recompiled as needed. Python has parts of this, but the language fights
against stability, and the fact that you need exactly the right VM fights against it.
I can't just distribute a Python program. But I can distribute just a Windows binary or
a Mac binary.

Time to get to work.
