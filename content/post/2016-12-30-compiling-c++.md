---
categories: dev
date: 2016-12-30T07:11:00Z
tags: C++
title: Compiling C++
url: /2016/12/30/compiling-c++/
---

To all C fans - everything I'm writing applies equally to C and C++, I just got tired of saying
C/C++, so picked the covering term. Also, I apologize in advance for anyone who runs across this;
I'm writing this as notes for myself, and while this will end up in a more polished form, I will
probably forget to put a link from here to the final version.

I am working from the C++14 standard draft N4296, which is freely available (the formal version
of the standard costs money, but is functionally identical to the last draft). And none of this
stuff changes with C++17, I believe.

The standard uses language that keeps options open, but this means the standard doesn't talk about
the really interesting bits. Let's start with files.

# Files

We put source code into files with names; these are often organized in hierarchical file systems.
This does not mean that this will be the only way we ever work with source code, but we do work
with it this way at the moment.

What is a file? It's a sequence of bytes. It's also a sequence of bits, but no known file system
handles a file of say 1023 bits, it handles files with an integral number of bytes. But if you
want to be pedantic, substitute "sequence of bits" for everything I write and it will work.
It's just extra mental work that will go unused until some future date where such a bit-oriented
file system comes into existence.

We can also think of this as a stream of bytes, and you'll see a lot of file APIs expressed
as stream APIs. I prefer to not use this terminology, because the length of a stream is not
nececessarily known until the end of the stream is encountered. A sequence is a predetermined
and finite list.

The model of "file" that I use here is of an immutable sequence of bytes; if
we have a different sequence, we have a different file. We have ambiguity if the same name refers
to two different sequences of bytes. This will be important later.

Of course, it's not just any sequence of bytes. The bytes have to be comprehendable as C++ source
characters. This is the basic C++ source character set:

    a b c d e f g h i j k l m n o p q r s t u v w x y z
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
    0 1 2 3 4 5 6 7 8 9
    _ { } [ ] # ( ) < > % : ; . ? * + - / ^ & | ~ ! = , \ " ’

The standard also allows for characters as identifed by universal-character-name, e.g. Unicode.

Also note that while this set includes all the printable ASCII characters except `@` and `$`,
most compilers accept those characters as well - `@` is needed for Objective-C, and the Gnu docs
say something about `$` used for VMS compatibility (still a thing, but HP announced end-of-life
for OpenVMS in 2020).

# Separate translation

Per 2.1 in the standard, the source program is handled in independent translation units; each
translation unit is handled by itself and the output of all translation units is combined
together to create the executable program.

A translation unit is a source file and all the files inserted with `#include` preprocessor
directives. Since files inserted by `#include` are themselves source files, there is some
external mechanism that decides which source files are the starts of translation units.

One reason for the persistent use of stream APIs is that files couldn't necessarily fit into
memory, even just the files for a single translation unit. Since C++ can be parsed with a
context-free grammar augmented with semantic rules, a source file can be processed in a
linear sequence from beginning to end, and so could be processed with stream API calls. It
adds just enough complexity that it bleeds into the various steps of the compiler, so we
will ignore this.

Not considering anything else, we have this:

    file ... ==> compiler ==> linker ==> executable

where translation of each file could be done in any arbitrary order or even in parallel.

# Character sets

When compiling C++, there are three character sets we will encounter:

* File character set
* Source character set
* Execution character set

These may all be the same, or may be distinct. A file may have an arbitrary character set, as
long as the compiler can translate this into the source character set. In practice, compilers
only deal with a few character sets, and may require that this be specified in some manner by
the user. GCC expects that the file character set is isomorphic with ISO 10646 - this does not
mean that it must be Unicode, just that there is a 1-to-1 translation between the file character
set and Unicode. Also, note that UTF-8 is a very common file encoding.

The source character set was mentioned above. It's not ISO 646 or Unicode, and it can be unique
to a specific compiler. The standard just says that a certain set of characters must be
supported. In practice, modern compilers use ISO 10646 internally. The entire reason to have
the formality of a source character set is that real-world files are messy, so this gives the
compiler writer a chance to fix things up to be canonical. Microsoft used to put a BOM into
UTF-8 files even though this is not needed and makes invalid files. Or the author of a file
mixed line endings due to copy/paste (lines with LF and lines with CRLF). Or really horrible things
like mixed encodings, where lines were merged from a CP1252-encoded file into a UTF-8 encoded file.

The execution character set is the character set that strings in the executable are formatted in.
This is very implementation-specific, and driven by the target platform. Note that only strings
and character constants need to be translated - the C++ source at this point is gone, converted to
whatever execution language was targetted. Again, UTF-8 is a common execution character set, because
Unix operating systems have converged to that as the default system character set. Since the compiler
is in charge of this, it generally comes out pristine, although garbage-in-garbage-out applies here.

# Translate to basic source character set

The first step in the C++ compilation process is to translate characters from the in-file format
to the source-character format. This is section 2.2.1 in the C++14 standard

Now, there are trivial ways to solve this. If source files are encoded in ASCII, ISO/IEC 10646,
or UTF-8, and if the compiler knows about UTF-8, then all these characters would be the same
in the file and in the source representation. And this is how compilers worked for many years.

However, the world is polyglot, and the standard has advanced. Newer versions of the standard
mention that you can use Unicode characters, inserted with universal character names, and that
means that the character set is now much larger than it used to be. And these Unicode characters
can be used in identifiers, not just in comments and strings.

You don't literally need to encode characters with universal-character-names, which would look
like this:

```
\u00F6
```

(which is the letter ö, if you were curious). The standard uses very careful language to allow
for old file encodings that cannot contain Unicode characters; by using universal-character-name
sequences, you can put Unicode characters into any file format that can hold all of the basic
characters.

So now we have

    file ... ==> source-chars ==> compiler ==> linker ==> executable

But really, it's a pipeline

    file1 ==> source-chars ==> compiler ==> object1
    file2 ==> source-chars ==> compiler ==> object2
    ...

Each stage of the pipeline transforms a sequence of bytes, although the transform may be the
identify transform. For source character translation, if the file is already in the source character
format, then nothing needs to happen.

This is a part where the standard is either insufficient or not fully implemented, because GCC
only supports identifiers with `\u` or `\U` escapes, and not the Unicode characters themselves.

# other

With the include model, we have:

    (file, file, ...) ==> logical-source ==> compiler ==> executable

where `logical-source` is the file that would be created by inserting contents of files
in place of `#include` preprocessor directives.
