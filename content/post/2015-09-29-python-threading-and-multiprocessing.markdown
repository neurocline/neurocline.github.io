---
categories: dev
date: 2015-09-29T15:12:00Z
tags: python threads multiprocessing
title: 'Python: threading and multiprocessing'
url: /2015/09/29/python-threading-and-multiprocessing/
---

The basic threading model in Python is extremely simple.

{{< highlight c++ >}}
# simmple thread example

import threading
import time

def myfunc(id):
    print("I am thread %d" % id)

for t in range(5):
    threading.Thread(target=myfunc, args=(t,)).start()

print("I am not a thread")
{{< / highlight >}}

Python threads have some advantages and disadvantages, and they spring from the same source;
you can do threads, but only one thread can execute Python code at a time. There are still
big advantages to using threads, but you can't take advantage of multiple processors/cores.

Why use threads? Threads are still useful for simplifying complex flows. Events are better,
but event processing has a higher minimum of complexity.

Threads are also very useful for I/O; the I/O is not done in Python code, and I/O calls often
are waiting on hardware. This means that you can have multiple threads doing I/O and all of them
can make parallel progress.
