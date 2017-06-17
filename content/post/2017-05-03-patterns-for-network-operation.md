---
categories: dev
date: 2017-05-03T03:30:00Z
tags: C++
title: Patterns for network operations
url: /2017/05/03/patterns-for-network-operation/
---

This is a grandiose topic, so I'll focus on a subset. What's the best way to do HTTP operations?
As a reminder, these are GET, PUT, POST, DELETE, and PATCH.

These are request/response operations; you send a request to the server and it gives you
a response. Let's assume we have a `class HttpConnection` and `conn` is an instance of
that class:

```c++
auto [headers, body] = conn.HttpGet(url);
```

The interval between sending the request and getting the response is a very long
time in computer terms; even on a local machine, it can take hundreds of microseconds, and over
larger distances like thousands of miles, it can take tens of milliseconds.

A blocking approach is trivial to code and reason about, but does not produce satisfactory results,
especially given that non-responses require a timeout, and this timeout could be a hundred seconds.
Parallel blocking calls can be made via the use of threads, but that only works well when each
network operation is independent of the others.

First, we rewrite the code in terms of sending a request and getting a response. Both of these
are blocking calls:

```c++
(void) conn.HttpRequest(Get, url);
auto [headers, body] = conn.HttpResponse();
```

Now, let's turn both of these into non-blocking operations.

```c++
auto request = conn.HttpRequest(Get, url);
request.wait();
auto response = conn.HttpResponse();
response.wait();
auto [headers, body] = response.data();
```

As written, this is still blocking. But now we can do other things while waiting for our calls
to finish. We might have an event loop we can tickle:

```c++
auto request = conn.HttpRequest(Get, url);
requests.push_back(request);
...
for (auto& r: requests)
	if (r.done())
	{
		auto response = conn.HttpResponse();
		responses.push.back(response);
	}
...
for (auto& r: responses)
	if (r.done())
	{
		auto [headers, body] = r.data()
		... do something with the response
	}
```

Of course, it's not as simple as that; how do we know what to do with our response?
