---
layout: post
title:  "Python and UTF-8 and Windows"
date:   2016-10-13 11:10:00 AM
categories: dev
tags: python unicode
---

Some day, everyone will have their act together when it comes to encoding and decoding
of strings that have meaning. Until then, we need hacks.

This is what I do in Python programs that print to the screen. Currently I try to make my
Python code work in both Python 2 and Python 3 - I look forward to being able to ditch
Python 2, but not yet.

{% highlight python %}
from __future__ import print_function, unicode_literals
import sys
PY2 = sys.version_info < (3,)

# This is a hack so that we have UTF-8 throughout in Python 2. The "correct" way is to use
# encode(s) at the point of output, http://farmdev.com/talks/unicode/. Or switch to Python 3.
if PY2:
    reload(sys)
    sys.setdefaultencoding('utf-8')

    # note - on windows, use chcp 65001 (default is chcp 437)
    # Fixed in Python 3.6 and up, apparently
    if sys.platform == "win32":
        import codecs
        codecs.register(lambda name: codecs.lookup('utf-8') if name == 'cp65001' else None)
{% endhighlight %}

This way, my programs don't croak when they encounter Unicode codepoints.

This is *mostly* better in Python 3.5 and up, but still not totally seamless.

# Reference

[Windows cmd encoding change causes Python crash](http://stackoverflow.com/questions/878972/windows-cmd-encoding-change-causes-python-crash)

[Python, Unicode, and the Windows console](http://stackoverflow.com/questions/5419/python-unicode-and-the-windows-console/32176732)

[Python Helpers for String/Unicode Encoding, Decoding and Printing](https://www.metachris.com/2015/11/python-tools-for-string-unicode-encoding-decoding-printing/)

[Print to the console in Python without UnicodeEncodeErrors](http://chase-seibert.github.io/blog/2014/01/12/python-unicode-console-output.html) - note, this did not work for me in Python 2.7.9

[Defining Python Source Code Encodings](http://legacy.python.org/dev/peps/pep-0263/) - I think this is irrelevant now, doesn't Python assume that your source code is UTF-8?

[Setting the correct encoding when piping stdout in Python](http://stackoverflow.com/questions/492483/setting-the-correct-encoding-when-piping-stdout-in-python)

[Writing unicode strings via sys.stdout in Python](http://stackoverflow.com/questions/1473577/writing-unicode-strings-via-sys-stdout-in-python)
