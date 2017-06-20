---
categories: dev
date: 2016-01-18T19:15:00Z
tags: go programming
title: 'Go select: blocking by default'
url: /2016/01/18/go-select-blocking-by-default/
---

I've not yet remembered all of the Go language details, and sometimes intuition fails me. Or, rather, my
mental model of how I expect things to work differs from how Go actually works.

The select language construct is cool - it's the networking select call turned into a language construct
and really beefed up. There is no switch statement, but select fills in for it, because it can have
arbitrary code as its cases.

So I was building up a program that wanted to handle terminating and aborting cleanly. I have a scanner
that should terminate if some other part of the code says to. So I had this in my program

```go
select {
case <- done:
    return // aborting early
}
```

And, nothing worked. Since my program had a goroutine for status monitoring, it ran, but it said that
nothing was happening. Hmm. A few diagnostic prints zeroed in on the above code. I had to page through
the D&K Go book until I found out the critical part. The Go select function waits until some case is
satisfied; e.g. blocking. In my case, I'd just blocked my scanner from doing anything, as opposed to
what I thought I was doing, having it check for early abort.

Adding a default case that does nothing did the trick; case statements are evaluated in order, so unless
my done channel has a value on it, the default is reached and the select exits.

```go
select {
case <- done:
    return // aborting early
default:
    // do nothing (a subtlety that needs to be reinforced)
}
```

I've used select a few dozen times by now, but I guess I always lucked out. Or didn't luck out.

If I write a Go book, it's going to focus on the parts of the language that aren't a direct map to C
or other languages.
