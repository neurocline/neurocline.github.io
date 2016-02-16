---
layout: post
title:  "The superset approach"
date:   2016-02-13 22:27:00 PM
categories: dev
tags: programming design
---

The software world is a diverse place. There's more than one way to do it, whether we like it
or not. This can be exhilarating when it works in our favor, frustrating when it does not,
and problematic when we want to have common behavior in the face of all this choice.

Take filesystems, for example. There are several families of filesystems; all have the same
basic features. Files are collections of bytes that can be located by a name, almost always
organized as a hierarchy of directories. Files have attributes: different kinds of time values
(birth, change, modify, access), access control, file type, and more. Names themselves can
be just a meaningless string of bytes, but more typically as an encoding such as ASCII or
Unicode. I didn't leave out size; strictly speaking, size is not an attribute, it's a
consequence of the file data. However, most filesystems do treat this like an attribute.

So imagine you want to make a backup program, or some kind of catalog system, that wants
to record some or all of a filesystem's information. Or imagine that you are trying to
present a uniform API to access any filesystem. There are two approaches. One is the
least common denominator approach, and indeed many filesystem libraries take this approach.
But it is an unsatisfying and even dangerous approach. Imagine that you wrote a backup
program using least-common denominator APIs. How likely is it that you can restore files
to the original filesystem? In fact, this leads to inferior programs using the subset
API, and drives developers who want to excel into writing programs specific to each domain.

A far better approach is the superset approach. Support every feature, and have fallbacks
when filesystems don't fully support a particular feature. This is a little more work at
first, or even a lot of work, but can have a big payoff.

Credit to Node.js for calling this out in their guide on working with filesystems. See
[Working with Different Filesystems](https://nodejs.org/en/docs/guides/working-with-different-filesystems/).
But this is a general approach, and not just for filesystems. So let's look at it in the context
of several different feature sets that see great variety. We'll start with filesystems.

# Filesystem superset

## File times

Windows NTFS files have 100-nanosecond resolution, and are 64-bit values with the epoch set at
1600 A.D UTC. There are four values:

- Create time: when the file was first created
- Last metadata change: when the file attributes were altered
- Last modified time: when the file contents were last altered (write)
- Last access time: when the file contents were last accessed (read or write)

Posix files have 1-second resolution, and are 32-bit or 64-bit values with the epoch set
at 1970 A.D. Unfortunately, it is not specified as to whether times are UTC or some other
time zone. Posix has three values

- Change time: when the file inode was changed
- Modified time: when the file data was changed
- Access time: when the inode or data was accessed (read or write)

Mac HFS+ files have 1-second resolution and are 32-bit values with the epoch set at 1904 A.D
UTC.

- Create date: when the file was created
- Modify date: when the file was modified
- Attribute mod date: when file attributes or directory contents where changed (Mac OS X only)
- Backup date: when the file was last backed up
- Access date: when the file was last read

Note that there are similarities, but none of those filesystems is exactly like any other.
If we decide that all filetimes are in seconds, then we don't record Windows NTFS file times
properly. Also, while Posix talks about 1-second resolution for file times, this is decidedly
not true for all filesystems; ext4 supports nanosecond resolution on stored files if the volume
is configured for inodes that are 256 bytes or bigger.

Note that the C library, Python, Go and so forth present a uniform API that is a subset API.
This makes getting "real" filetimes impossible.

Instead, we should just get the full data from a filesystem, and then have various ways for
our user code to access it. We could query for what is really stored, or we can ask for it in
a format we want and have the underlying data converted to what we want to see. So if our
user program wants to work with nanosecond times, we'd be given Windows NTFS times multipied
by 10, and Posix times multiplied by 1 billion. But we could be given ext4 files at their
full resolution.

Or if we wanted our epoch to be 2000 A.D., we could ask for file times set to this epoch, and
have their metadata translated where possible. We would not permanently convert in any case,
although some operations would require a one-way and potentially lossy conversion. For example,
assume we have a NTFS backup, and we need to restore it to an HFS+ volume; some time resolution
would be lost.

And then we have time zones. Some filesystems and operating systems do all of the underlying
time operations in UTC, and conversion to the local time zone is just for the convenience of
the user. There's a problem here with daylight savings, the practice in many countries to move
the clock back or forth by an hour depending on the season. If filetimes are stored in UTC, and
displayed in the local timezone, then a naive algorithm will cause all the filetimes to appear
to shift backwards or forwards by an hour when daylight savings comes or goes. This in fact
caused all kinds of havoc to build systems and source control systems on Windows, so much so
that Microsoft introduced a "fix" that was worse than the problem; when saving a file created
during when daylight savings is in effect, it writes a UTC time offset by the daylight savings
offset (typically 1 hour), so that the adjustment to local time stays consistent when daylight
savings changes. Of course, this means that the wrong time is written for half of the year.

There is a slightly better answer, and that is to do the time zone conversion with the awareness of when
in each year daylight savings occurred. It has only happened a few dozen times since we first
started saving files to disk, so we could just remember all the times when this happened, and
adjust file times in the past to their adjusted local time zone. To my knowledge, no one is doing
this, which is sad.

There is one further problem in that there are discontinuities in time values. Even with UTC,
we are adjusting the clock by seconds to account for the slowing of the Earth (UT is Universal
Time which has no adjustments, UTC is Universal Time Coordinated which has had 20-odd 1-second
adjustments in the past 40 years). And with daylight savings, we have entire hours that are either
missing or duplicated.

## File name encodings

Another problematic area is in recording file names. While file names inside the file system
are really just very long opaque identifiers, file names are usually for the benefit of people, who
want to read them, sort them, do substring searches, and synthesize them. This requires the
use of an encoding.

Of course, there is no one true encoding. Before the 1990s, file systems skirted the issue by
just writing bytes, but interpreting them with the local encoding. This quickly stopped being feasible
when use of computers and filesystems spread across the world. Even the introduction of Unicode
didn't result in this being solved completely, due to the multiplicity of Unicode encodings and
normalizations.
