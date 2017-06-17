---
categories: dev
date: 2016-04-03T09:04:00Z
tags: links
title: Rules for cross-platform programming
url: /2016/04/03/rules-for-cross-platform-programming/
---

Writing reusable code is in general a good thing to do, and the number one lesson there is about
managing dependencies. Writing cross-platform code has a different set of challenges, and is best
considered as a super-set of the reusable code problem.

This is a "part 1 of N" series". Consider this the notes for that book you've always wished existed.

# Use UTF-8 as your text format

Unicode has grown into its original arrogant name of "Universal character set". Of all the encodings
of Unicode, UTF-8 is the most practical. Use UTF-8 as your program's character set, keep all your
strings in UTF-8. This includes any of your own data you read and write.

Convert to the platform character set/encoding on input and output. When you get a string from an
operating system function, convert it to UTF-8. When you need to output a string, convert it to
what the platform needs.

By doing this, you'll find that all your special cases are kept at the boundaries of your code. You'll
also ensure that you only have N conversions (where N is the number of distinct operating system
encodings that you support), and not creep into N^2.

This task is actually fairly easy on modern Linux and Mac OS X, which both use UTF-8 as their
platform character set (or can be told to do so). The best current way to write Windows programs is
as UCS16-LE (their "Unicode" setting is really 16-bit UCS stored as little-endian characters). It's
best to write a set of wrappers to convert to/from "native" (your format), and have that be passthrough
functions when running on a platform that uses UTF-8.

At some point soon, I'll create a package that does this, that follows the other dictum of "carefully
manage dependencies". There are larger projects like iconv and icu that can be bent to this task, but
they lack the easy helper functions we would need to make this transparent in our code.

# A file names is the sequence of characters the OS gave you

Despite appearances, file names and the paths constructed from them aren't really text. The end
user thinks of names this way, of course, because file names by and large are meant for use by
people. But a file name is really a sequence of octets that a filesystem has matched up with
an file entry. If you change any of the bytes in the file name, then you will find that you can no longer
find that file entry, and then the user will think the file is missing.

Unfortunately, while we can usually preserve meaning in round-trips between encodings, it is all too easy
to change byte sequences. Consider Mac OS X filenames - these are UTF-8 strings stored in a mostly but
not completely decomposed form ([Text Encodings in VFS](https://developer.apple.com/library/mac/qa/qa1173/_index.html)).
And while that is possible to manage, many filesystems don't actually specify the name encoding, so it's
quite likely for you to mangle filenames by making incorrect assumptions.

The best approach is to never alter filenames, but to leave them as octet strings. This means several
things. First, you'll manipulate paths via path functions, and not as strings. Second, if you need to
output a string, you'll make your best guess as to the encoding and convert it as you need.

This is a little awkward, but means that your code will be robust across encodings. It does mean that you
have a challenge if you need to record paths in config files or databases. But since paths are only relevant
to the filesystem they came from, I suggest that you record the paths as the original byte sequences.

Some times programs can dictate the names that are used for filenames, but this is very rare, and not worth
considering.

When I say that file names are tied to file systems, I mean specifically that. If you copy a file to another
file system, it now potentially has two different sets of octets it is known by, depending on the limitations
of each filesystem. It is the name that the copy is created under that is the name on the destination
filesystem. Most of the time, the octets for the source file and the octets for the destination file are
the same. This is not guaranteed, so do not rely on it.

Well-known filenames are a different matter. Not all filenames are discovered, some are predetermined. However,
that doesn't make your life any easier. As long as there can be user-defined filenames, it's best to treat
all filenames by the same rule.
