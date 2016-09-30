---
layout: post
title:  "Recent links"
date:   2016-09-30 9:35:00 AM
categories: dev
tags: links
---

[Requests: HTTP for Humans](http://docs.python-requests.org/en/latest/). A much easier to use HTTP library for Python. I wish something like this existed in C++.

[Certifi, Trust Database for Humans](https://certifi.io/en/latest/). The Python requests project decided that system-installed certs are a bad thing, so they curate their own collection.

[Suppress InsecureRequestWarning: Unverified HTTPS request](http://stackoverflow.com/questions/27981545/suppress-insecurerequestwarning-unverified-https-request-is-being-made-in-pytho). Requests has one issue in that if you disable cert validation (a bad idea but sometimes you have to, then you get a big fat warning to stderr. This post details a way to suppress that warning:

{% highlight python %}
import requests
from requests.packages.urllib3.exceptions import InsecureRequestWarning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
{% endhighlight %}

[Using APIs with Python Requests Module](http://engineering.hackerearth.com/2014/08/21/python-requests-module/). Tidbits on using requests.

[GitHub v3 API](https://developer.github.com/v3/). The GitHub API is fairly clean.

[Creating an access token for command-line use](https://help.github.com/articles/creating-an-access-token-for-command-line-use/). Most uses of the GitHub API need to do the work on authenticated logins, and the easiest way to do that is to create an access token to avoid needing to implement the full OAuth flow.
