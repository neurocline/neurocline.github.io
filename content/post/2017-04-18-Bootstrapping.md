---
categories: dev
date: 2017-04-18T00:00:00Z
tags: text
title: Bootstrapping
url: /2017/04/18/Bootstrapping/
---

Git for Windows is at 2.12.2(2). Why is it so far behind? Git 2.1 was released in August 2014. Maybe
just stop developing on Windows, or do all source control on Unix/Mac?

Use `py` and shebang lines to handle multiple versions of Python on the same computer. Install
Python 3.6 first into path, then Python 2.7 second without modifying path.

Probably should install "for all users" for most programs. I installed Python 3.6 

I just installed Ruby again just to install Jekyll which I want to switch away from.

This is also my chance to play with boot-from-VHD on Windows 10.

My Windows dev box pattern is this:

- `C:\Dev` is where dev tools go: this includes Python, Visual Studio, etc.
- `C:\Dev\bin` contains standalone binaries, so it's in the path
- `C:\projects` is where I put source code
  - `C:\projects\github` for forks from Github
  - `C:\projects\go` for my `GOPATH` (because Go wants all source in one tree)

I don't tend to put things in `C:\User\<user>`, but maybe I should, since that's the natural
place on Unix? The reason I don't is that Microsoft already fills it with a lot of crap, although
that seems to have gone down with Windows 10.

There REALLY REALLY needs to be a universal package manager for Windows. I'm not sure Chocolatey
is it. But I should try it, because it has 4000 packages now. Except it's in the process of going
commercial, and so (given Windows) that means companies as clients. Also, I notice that Chocolatey
packages tend to be really old - their `pip` package is over 2 years old. Still...

Cool, Chocolatey has a script to refresh environment variables in a running cmd window; I copied this
and saved it as their name `RefreshEnv.cmd`. Works great!

- [Is there a command to refresh environment variables from the command prompt in Windows?](http://stackoverflow.com/questions/171588/is-there-a-command-to-refresh-environment-variables-from-the-command-prompt-in-w)

For Go, my `%GOPATH` is `C:\projects\go`.

I installed perl again, because I needed it a month ago, but I should figure out why. I'd love to
do without Perl and Ruby, and just use C++, Python and Go - and move more to Go and less to Python.

Is it my imagination, or is a new computer always fast? This reinforces the desire to do boot-from-VHD
so I can have a clean system every month.

Subversion... sigh. Passwords... sigh.

7-zip.

Perforce.

Firefox.

WinDirStat.

WinPCap.

# Links

- [Chocolatey](https://chocolatey.org/)
- [Jekyll on Windows](https://jekyllrb.com/docs/windows/#installation)
- [Jekyll 3 on Windows](https://labs.sverrirs.com/jekyll/)
