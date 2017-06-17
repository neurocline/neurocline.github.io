---
categories: dev
date: 2015-10-17T01:00:00Z
tags: python exception
title: SIGINT clean handling in Python
url: /2015/10/17/sigint-clean-handling-python/
---

This is #1 in the series "It seems to be hard to make Python robust". A robust program
won't crash malfunction when ctrl-c is pressed, it will abort down clealy if possible.
This is important with multiprocessing programs, because naive code will actually hang
when you try to interrupt it with ctrl-c.

SIGINT (the exception raised when you press ctrl-c) will raise an exception when
I/O operations return. This is pretty annoying. This happens even on time.sleep().

The way around this to catch ctrl-c is to install a handler, and put try blocks around
IO operations. Real code won't ignore IOError like this, this is just a sample.

{{< highlight python >}}
# sigint-clean.py

# A SIGINT will cause an I/O operation to raise exceptions on return. This happens
# even on Windows. This means even with a ctrl-c handler, you must wrap I/O calls
# in a try/catch IOError block and check for interrupted. Annoying

import signal
import time

class catch_sigint(object):
    def __init__(self):
        self.caught_sigint = False
    def note_sigint(self, signum, frame):
        self.caught_sigint = True
    def __enter__(self):
        self.oldsigint = signal.signal(signal.SIGINT, self.note_sigint)
        return self
    def __exit__(self, *args):
        signal.signal(signal.SIGINT, self.oldsigint)
    def __call__(self):
        return self.caught_sigint

def main():
    with catch_sigint() as got_sigint:
        running = True
        while running:
            process()
            if got_sigint():
                running = False
                print("Caught SIGINT!")

def process():
    # without this try/except, you'll get an IOError when ctrl-c pressed
    try:
        time.sleep(0.1)
    except IOError:
        pass

if __name__ == "__main__":
    main()
{{< / highlight >}}

And the other other answer is to write your serious programs in C++ (or maybe Go)? I still
like Python, but the effort to get to silky smooth reliability is erasing all the gains from
using Python in the first place. The answer of course is to make C++ as easy to use as Python.
That's not a language issue, it's an environment/packages issue.

Perl, Python and R should give incredible credit to CTAN (Comprehensive Tex Archive Network) for
blazing the trail. That's the real reason Perl became successful. Python lives more on its own
merits but would still be somewhat neutered without PyPI.
