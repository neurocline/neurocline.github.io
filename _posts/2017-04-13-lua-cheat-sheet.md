---
layout: post
title:  "Lua cheat sheet"
date:   2017-04-13 10:30:00 AM
categories: dev
tags: lua
---

Semicolons are optional. `~=` is not-equals.

Strings are single-quote or double quote, it's arbitrary.

String contatenation operator is `..`

`0` and `''` are true! Only `nil` and `false` are false.

`nil` is the undefined type, also used to free vars.

A range is begin, end [,step], and ranges include both ends.

If a function has a single parameter that is a string or a table, the parens `()` are optional.

Return, function call and assignment take lists, and lists can be mismatched in length;
unmatched receivers are nil, unmatched senders are discarded.

Tables are it, used for maps and arrays. Array index starts at 1. `#table` is length of table.
String keys are the default, and you can index with table.key or table["key"]. Non-string
keys require aray indexing. Can index by anything except nil. Index by value for numbers and
strings but identity for tables - e.g. two successive {} are different tables.

a.x is the same as a["x"]

Assign nil to a table entry to delete it.

`for k,v in pairs(table)` is generic-for table iteration. `pairs` is an iterator function.

`ipairs` stops at the first non-initialized index.

`_G` is a table of all globals.

Can assign to a table with a list, uses int keys starting at 1.

Objects are tables. `Dog = {}` creates a table, which can be used as an object.

Modules are tables.

The `:` "operator" is a synonym to add `self` to declarations and uses of object and module functions.

Numeric for: `for begin, end, step`. For loop variable is local to for loop.

`function print(...)` - all the arguments are put into an `arg` table for the function to access, and
arg.n is the number of elements in the table. Use `unpack(arg)` to unpack into list, e.g. to send to
another function.

`f{...} lets you make a function call and simulate named arguments - this actuall gets a single
parameter that is a table. It's really `f({...})`.

Creating a new instance of a class requires some metatable magic so that the new table will forward
its index operations to the class instance", which has the methods

```
functin Account:new(o)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  return o
``

So, in other words, `a:deposit(100)` is the same as `Account.deposit(a, 100)`.

# Reference

Others have tried to summarize Lua.

[The Lua Language (v5.1)](http://lua-users.org/files/wiki_insecure/users/thomasl/luarefv51single.pdf)

[Lua Cheat Sheet for Programmers](http://coffeeghost.net/2010/11/01/lua-cheat-sheet-for-programmers/)

[Learn Lua in 15 Minutes](http://tylerneylon.com/a/learn-lua/)
