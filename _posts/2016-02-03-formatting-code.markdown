---
layout: post
title:  "Formatting code"
date:   2016-02-04 16:55:00 PM
categories: dev
tags: code formatting
---

A long time ago, I was looking into writing a parser that treated comments and whitespace
as first-class citizens; not in the language grammar, but because I wanted to write
code formatters that handled comments more gracefully. It looks like the Go team ran
into this when making ```go fmt```.

[The Cultural Evolution of gofmt](https://talks.go-zh.org/2015/gofmt-en.slide#1)

The notes in Robert Griesemer's presentation mention doing stuff ad-hoc and TBD to make
it more integrated with the AST. And that's what I had been considering - there's the grammar
for generating code, and then there's the grammar for the meaning that the source code
itself conveys, which includes where comments are "attached" to code.

And on a related note, and also in ```go fmt```, tab stops are a royal pain in the ass.
It's much better to think of tab stops at the semantic level and not at the implementation
level of "go to fixed tab stop". This is a proposal from 10 years ago to do exactly that.

[Elastic Tabstops](http://nickgravgaard.com/elastic-tabstops/)

It's interesting how problems can live for years and years, and keep coming up sporadically,
and never actually be "solved". It's getting close to the time where this needs to be solved.
