---
layout: post
title:  "Package manager failure"
date:   2016-11-07 5:39:00 PM
categories: dev
tags: python
---

Package managers shouldn't do this. TL/DR - I just wanted to install Sphinx. It
installed fine on one machine, but the install barfed on another. I have no idea
how to fix this, and casual googling provided no relief. So - I am actually
uninstalling and re-installing Python, for lack of any better idea.

I tried upgrading to the newest pip as suggested, but of course that did nothing.
Maybe it's because I'm running Python 3.6 beta on the machine in question. Of course,
I would like to know why it didn't give me a sensible error instead of this. So I'll
uninstall Python 3.6 beta and install latest Python 3.5, because I'm guessing it's
something related to that.

My actual point is that this is not sensible behavior for a package manager. Package
managers should just work. This indicates a design flaw to me somewhere in the system.

Update: a slightly better Google search did turn up a Stackoverflow page (of course),
and I guess Python 3.6 beta is a little too bleeding edge for me. So my point might
still stand, but not precisely pointed at Python and pip, just in general.

Things should work, dammit.

{% highlight bash %}
C:\w>pip install sphinx
Collecting sphinx
  Downloading Sphinx-1.4.8-py2.py3-none-any.whl (1.6MB)
    100% |████████████████████████████████| 1.6MB 618kB/s
Collecting snowballstemmer>=1.1 (from sphinx)
  Downloading snowballstemmer-1.2.1-py2.py3-none-any.whl (64kB)
    100% |████████████████████████████████| 71kB 3.8MB/s
Collecting docutils>=0.11 (from sphinx)
  Downloading docutils-0.12-py3-none-any.whl (508kB)
    100% |████████████████████████████████| 512kB 1.2MB/s
Collecting Pygments>=2.0 (from sphinx)
  Downloading Pygments-2.1.3-py2.py3-none-any.whl (755kB)
    100% |████████████████████████████████| 757kB 282kB/s
Collecting alabaster<0.8,>=0.7 (from sphinx)
  Downloading alabaster-0.7.9-py2.py3-none-any.whl
Collecting babel!=2.0,>=1.3 (from sphinx)
  Downloading Babel-2.3.4-py2.py3-none-any.whl (7.1MB)
    100% |████████████████████████████████| 7.1MB 136kB/s
Collecting imagesize (from sphinx)
  Downloading imagesize-0.7.1-py2.py3-none-any.whl
Collecting six>=1.5 (from sphinx)
  Downloading six-1.10.0-py2.py3-none-any.whl
Collecting colorama>=0.3.5; sys_platform == "win32" (from sphinx)
  Downloading colorama-0.3.7-py2.py3-none-any.whl
Collecting Jinja2>=2.3 (from sphinx)
  Downloading Jinja2-2.8-py2.py3-none-any.whl (263kB)
    100% |████████████████████████████████| 266kB 345kB/s
Collecting pytz>=0a (from babel!=2.0,>=1.3->sphinx)
  Downloading pytz-2016.7-py2.py3-none-any.whl (480kB)
    100% |████████████████████████████████| 481kB 480kB/s
Collecting MarkupSafe (from Jinja2>=2.3->sphinx)
  Downloading MarkupSafe-0.23.tar.gz
Installing collected packages: snowballstemmer, docutils, Pygments, alabaster, pytz, babel, imagesize, six, colorama, MarkupSafe, Jinja2, sphinx
Exception:
Traceback (most recent call last):
  File "c:\python36b2\lib\site-packages\pip\basecommand.py", line 215, in main
    status = self.run(options, args)
  File "c:\python36b2\lib\site-packages\pip\commands\install.py", line 317, in run
    prefix=options.prefix_path,
  File "c:\python36b2\lib\site-packages\pip\req\req_set.py", line 742, in install
    **kwargs
  File "c:\python36b2\lib\site-packages\pip\req\req_install.py", line 831, in install
    self.move_wheel_files(self.source_dir, root=root, prefix=prefix)
  File "c:\python36b2\lib\site-packages\pip\req\req_install.py", line 1032, in move_wheel_files
    isolated=self.isolated,
  File "c:\python36b2\lib\site-packages\pip\wheel.py", line 493, in move_wheel_files
    maker.make_multiple(['%s = %s' % kv for kv in console.items()])
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\scripts.py", line 383, in make_multiple
    filenames.extend(self.make(specification, options))
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\scripts.py", line 372, in make
    self._make_script(entry, filenames, options=options)
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\scripts.py", line 276, in _make_script
    self._write_script(scriptnames, shebang, script, filenames, ext)
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\scripts.py", line 212, in _write_script
    launcher = self._get_launcher('t')
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\scripts.py", line 351, in _get_launcher
    result = finder(distlib_package).find(name).bytes
  File "c:\python36b2\lib\site-packages\pip\_vendor\distlib\resources.py", line 324, in finder
    raise DistlibException('Unable to locate finder for %r' % package)
pip._vendor.distlib.DistlibException: Unable to locate finder for 'pip._vendor.distlib'
You are using pip version 8.1.2, however version 9.0.1 is available.
You should consider upgrading via the 'python -m pip install --upgrade pip' command.
{% endhighlight %}
