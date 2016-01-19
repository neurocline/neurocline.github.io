---
layout: post
title:  "Go language cheat sheet"
date:   2016-01-16 22:00:00 PM
categories: dev
tags: go programming
---

I can't find a terse and usable summary of the Go language. So here it is. I'll make it all pretty at some point. In fact, it's quickly getting not-terse, so I need to employ better HTML skills and tighten it up somehow.

Maybe what would work better is callouts for things that are "different from language X", because 80% of Go
is quite like C, Python, Java etc.

## Types

Kind | Type
---- | -----
bool | true, false
integer | int, uint, uintptr, int8, int16, int32, int64, uint8, uint16, uint32, uint64
float | float32, float64
complex | complex64, complex128
byte | alias for uint8
rune | alias for int32

## Operators

No operator overloading, so operators only work on basic types

### Logical operators

Operator | Meaning          | Examples
-------- | -------------    | ------------------
```!```  | logical negation | ```!a, a = !b```  
```&&``` | conditional AND  | ```if a() && b()``` (short-circuit evaluation)
```||``` | conditional OR   | ```if a() || b()``` (short-circuit evaluation)

### Arithmetic operators (integer, float, complex, string)

Operator | Meaning          | Examples
-------- | -------------    | ------------------
```+```  | sum              | ```1 + 1 == 2, "abc" + "d" == "abcd"```

### Arithmetic operators (integer, float, complex)

```-```  | difference       | ```2 - 1 == 1```
```*```  | product          | ```2 * 2 == 4```
```/```  | quotient         | ```3 / 2 == 1```, ```5.0 / 2.0 == 2.5```

### Arithmetic operators (integer)

```%```    remainder        | ```5 % 2 == 1```
```&```    bitwise AND      | ```6 & 3 == 2```
```|```    bitwise OR       | ```4 | 3 == 7```
```^```    bitwise XOR      | ```7 ^ 2 == 5```
```&^```   bit clear (AND NOT) | ``` 11 &^ 6 == 9

### Arithmetic operators (integer OP unsigned integer)

```<<```   left shift       | ```1 << 15 == 32768```
```>>```   right shift      | ```65536 >> 8 == 256```

# Reference

[Documentation](https://golang.org/doc/)

[The Go Programming Language Specification](https://golang.org/ref/spec)

[Effective Go](https://golang.org/doc/effective_go.html)

[How to Write Go Code](https://golang.org/doc/code.html)

[Golang Examples](http://golang-examples.tumblr.com/)
