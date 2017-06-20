---
categories: dev
date: 2016-04-29T22:37:00Z
tags: programming Python
title: Local ReadTheDocs
url: /2016/04/29/local-readthedocs/
---

FYI - failed. Maybe I'll succeed when I try again.

I want to have a local ReadTheDocs install. Well, eventually running on a server on AWS, but
for now on a local machine. Here's my little diary of getting a loccal ReadTheDocs server running.

I'm on Windows 7, behind the times a bit (Windows 10 is nicer).

Following [Installation](http://read-the-docs.readthedocs.io/en/latest/install.html) from ReadTheDocs. Hmm,
never used `virtualenv` before.

```
pip install virtualenv
```

and then a little Stack Overflow

- [http://stackoverflow.com/questions/4527958/python-virtualenv-questions](http://stackoverflow.com/questions/4527958/python-virtualenv-questions).

leads me to do

```
virtualenv rtd
cd rtd
Scripts\activate.bat
mkdir checkouts
cd checkouts
git clone https://github.com/rtfd/readthedocs.org.git
cd readthedocs.org
pip install -r requirements.txt
```

Total time to this point, less than 5 minutes. Now we do some database stuff, apparently.

```
manage.py migrate
```

Hmm, no Windows support?

```
(rtd) C:\projects\python\rtd\checkouts\readthedocs.org>manage.py migrate
Traceback (most recent call last):
  File "C:\projects\python\rtd\checkouts\readthedocs.org\manage.py", line 9, in <module>
    from django.core.management import execute_from_command_line
ImportError: No module named django.core.management
```

And, StackOverflow tells me that this actually means I don't have django installed. Hmm, I thought that
the `pip install -r requirements.txt` line was meant to install Django.

Ah, it did, but I didn't pay attention to errors in the `pip install` output

```
HTTP error 500 while getting https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl#md5=de307d2771370cbbdb866282a6093fc9 (from https://pypi.python.org/simple/lxml/)
Could not install requirement lxml==3.3.5 from https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl#md5=de307d2771370cbbdb866282a6093fc9 (from -r requirements/pip.txt (line 22)) because of error 500 Server Error: Internal Server Error for url: https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl
Could not install requirement lxml==3.3.5 from https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl#md5=de307d2771370cbbdb866282a6093fc9 (from -r requirements/pip.txt (line 22)) because of HTTP error 500 Server Error: Internal Server Error for url: https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl for URL https://pypi.python.org/packages/e0/88/a696ff37ef87e335f937eadec1bbbf3a014a07c3b7f2ce3ab563799503d1/lxml-3.3.5-cp27-none-win32.whl#md5=de307d2771370cbbdb866282a6093fc9 (from https://pypi.python.org/simple/lxml/)
```

This is annoying.

Apparently, this is because you can't just `pip install lxml` on Windows.

[http://stackoverflow.com/questions/23944465/installing-lxml-for-python-3-4-on-windows-x-86-32-bit-with-visual-studio-c-2](http://stackoverflow.com/questions/23944465/installing-lxml-for-python-3-4-on-windows-x-86-32-bit-with-visual-studio-c-2).

Although, I'm not sure that's what it means, now that I think about it, that's silly, I think that post is
just if you try to build from source. I was able to do this

```
$ pip install
Collecting lxml
  Downloading lxml-3.6.0-cp27-none-win32.whl (3.0MB)
    100% |################################| 3.1MB 261kB/s
Installing collected packages: lxml
Successfully installed lxml-3.6.0
```

and it got and installed the latest version. So maybe it's just that there no longer is a 3.3.5 version
on PyPI. And that appears to be the case, the oldest version listed there is 3.4.4.

So what if I edit requirements/pip.txt? Can I get a working build?

```
pip install -r requirements.txt
```

Hmm, should I have uninstalled lxml 3.6.0? It seems to be downloading stuff. Also, it looks like there was a heck
of a lot more to install. Holy crap. Virtualenv may be cool and all, but it's like Docker, a cool solution for
a problem that shouldn't exist in the first place.

Wow, this is annoying. A nice, nasty error at maybe the end of install

```
Exception:
Traceback (most recent call last):
  File "c:\projects\python\rtd\lib\site-packages\pip\basecommand.py", line 209,
in main
    status = self.run(options, args)
  File "c:\projects\python\rtd\lib\site-packages\pip\commands\install.py", line
317, in run
    prefix=options.prefix_path,
  File "c:\projects\python\rtd\lib\site-packages\pip\req\req_set.py", line 743,
in install
    requirement.remove_temporary_source()
  File "c:\projects\python\rtd\lib\site-packages\pip\req\req_install.py", line 7
59, in commit_uninstall
    logger.error(
  File "c:\projects\python\rtd\lib\site-packages\pip\req\req_uninstall.py", line
 142, in commit
    rmtree(self.save_dir)
  File "c:\projects\python\rtd\lib\site-packages\pip\_vendor\retrying.py", line
49, in wrapped_f
    return Retrying(*dargs, **dkw).call(f, *args, **kw)
  File "c:\projects\python\rtd\lib\site-packages\pip\_vendor\retrying.py", line
212, in call
    raise attempt.get()
  File "c:\projects\python\rtd\lib\site-packages\pip\_vendor\retrying.py", line
247, in get
    six.reraise(self.value[0], self.value[1], self.value[2])
  File "c:\projects\python\rtd\lib\site-packages\pip\_vendor\retrying.py", line
200, in call
    attempt = Attempt(fn(*args, **kwargs), attempt_number, False)
  File "c:\projects\python\rtd\lib\site-packages\pip\utils\__init__.py", line 10
2, in rmtree

  File "C:\Python27\Lib\shutil.py", line 247, in rmtree
    rmtree(fullname, ignore_errors, onerror)
  File "C:\Python27\Lib\shutil.py", line 247, in rmtree
    rmtree(fullname, ignore_errors, onerror)
  File "C:\Python27\Lib\shutil.py", line 247, in rmtree
    rmtree(fullname, ignore_errors, onerror)
  File "C:\Python27\Lib\shutil.py", line 247, in rmtree
    rmtree(fullname, ignore_errors, onerror)
  File "C:\Python27\Lib\shutil.py", line 252, in rmtree
    onerror(os.remove, fullname, sys.exc_info())
  File "c:\projects\python\rtd\lib\site-packages\pip\utils\__init__.py", line 11
4, in rmtree_errorhandler
    return
WindowsError: [Error 5] Access is denied: 'c:\\users\\bfitz\\appdata\\local\\tem
p\\pip-09ja6p-uninstall\\projects\\python\\rtd\\scripts\\pip.exe'
You are using pip version 8.0.2, however version 8.1.1 is available.
You should consider upgrading via the 'python -m pip install --upgrade pip' comm
and.
```

It looks like it tried to uninstall a running program. And why the heck is it trying to run something from
a temp folder? Ugly.

I'm building up notes for a second try (because maybe I broke my virtualenv Python folder), but let's continue.
Back to the database init.

```
manage.py migrate
```

Hmm, same error as before.

``
(rtd) C:\projects\python\rtd\checkouts\readthedocs.org>manage.py migrate
Traceback (most recent call last):
  File "C:\projects\python\rtd\checkouts\readthedocs.org\manage.py", line 9, in <module>
    from django.core.management import execute_from_command_line
ImportError: No module named django.core.management
```

Interestingly, I don't see django installed in my virtualenv folder. Isn't it supposed to be?
The progress from the `pip install -r requirements.txt` claimed that it download Django

```
Collecting django==1.8.3 (from -r requirements/pip.txt (line 9))
  Downloading Django-1.8.3-py2.py3-none-any.whl (6.2MB)
```

but I see no evidence of it anywhere. I see a bunch of other *.whl files
in `checkouts\readthedocs.org\deploy\wheels`. I don't know enough about Python, but it seems
like I should see a `Django-1.8.3-py2.py3-none-any.whl` file in there. Although, now I look at
things, I don't see many of the .whl files in there.

Maybe they went somewhere else on the hard disk? Temp folder? There are Django wheels in there, but
not for Django itself.

```
c:\Users\bfitz\AppData\Local\pip>dir *django* /s /b
c:\Users\bfitz\AppData\Local\pip\cache\wheels\21\79\c5\7e4204d7cc5dbb1c9941de3e9
c18106417f30dd9179c892ab2\django_haystack-2.1.0-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\25\b8\c0\839a46f5f8c4d0d8f45e49695
84913ffdbc62426216b1aa98d\django_celery_transactions-0.3.6-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\45\64\35\40b9cfc97eb9d9cbb4430accf
2018c6fb2f106b9e2a24e6e60\django_guardian-1.3-py2.py3-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\58\ae\74\09cb1c72d6c064bfca913e3e9
9d8b13299ba2714b80c1ed4ec\django_dynamic_fixture-1.8.5-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\9c\40\87\4f8ddfc22173ba38155c7caad
be36a07979403916ad57e22ce\django_copyright-1.0.0-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\9d\60\aa\145d80c18a9a99a3e8b5c5b1e
6424209a51ea831bfc7f18a3f\django_secure-0.1.2-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\a5\8e\75\c5eede5318e889b6e2257ab85
9a2db5cacc0f0208d0a3023c1\django_kombu-0.9.4-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\dc\b2\e7\35c317e3d293d88b2a515e85c
ad1a0c56ccd63675d7ae99863\django_vanilla_views-1.0.4-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\ee\f6\af\86c6954d4a2f0d811c6423780
6de246856aee0fca90d2579bd\django_gravatar2-1.0.6-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\f0\bc\87\8927de315c2f138f1811d3e6a
ccf5f40326d718f641972f713\django_cors_headers-0.13-cp27-none-any.whl
c:\Users\bfitz\AppData\Local\pip\cache\wheels\f6\04\64\ef5dc0eea5c486d22ffebb1c6
1fac5653d406a6713a5077d39\django_annoying-0.8.4-cp27-none-any.whl
```

Maybe I should go install it on a Mac instead? Or in a Linux VM? Or even just on a Linux box?

I'm done for now. Temporarily defeated.

```
(rtd) C:\projects\python\rtd>Scripts\deactivate.bat
```
