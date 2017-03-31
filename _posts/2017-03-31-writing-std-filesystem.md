---
layout: post
title:  "Writing std::filesystem"
date:   2017-03-31 1:30:00 PM
categories: dev
tags: c++
---

Let's write our own implementation of std::filesystem. Here's what we're going to work from.

- [C++17 draft standard](http://open-std.org/JTC1/SC22/WG21/docs/papers/2016/n4618.pdf) - see 27.10 File systems
- [File System Technical Specification](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4100.pdf) - essentially what's in the standard
- [Boost Filesystem Library](http://www.boost.org/doc/libs/1_42_0/libs/filesystem/doc/index.htm) - the TS came from Boost, after all
- [<filesystem> (Visual Studio 2015)](https://msdn.microsoft.com/en-us/library/hh874694.aspx)
- [libcstdc++ Filesystem](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/libstdc++/api/a01832.html)
- [Filesystem library (cppreference.com)](http://en.cppreference.com/w/cpp/experimental/fs)

This shipped as std::experimental::filesystem in Visual Studio 2015 (don't think VS2017 promoted it to
std::filesystem).

It shipped as `std::experimental::filesystem` in GCC 5.3 in libstdc++.

While it's implemented in libc++, also as `std::experimental::filesystem`,
it's not shipping with a Clang version yet (you need to build your own Clang/libc++ to enable it).

So, let's just write our own so we can start using it everywhere. Since we're starting from the fact of
the C++17 standard now including it, we will just create `std::filesystem`. This makes our life a little
easier at the moment, since no vendor has yet promoted it out of experimental.

# <filesystem>

This is somewhat defined for us in the standard. This goes in the namespace `std::filesystem`:

```
namespace std {
namespace filesystem {
inline namespace v1 {

} } } // namespace std::fileystem::v1
```

Note the `inline namespace v1`. See REFERENCE for an explanation about using inline namespaces to help
in API versioning.

The classes in our namespace:

```
class path;
class filesystem_error;
class directory_entry;
class directory_iterator;
class recursive_directory_iterator;
class file_status;
struct space_info;
enum class file_type;
enum class perms;
enum class copy_options;
enum class directory_options;
typedef chrono::time_point<trivial-clock> file_time_type;
```

There are also a host of non-member functions to implement. We're going to group these by
functionality, rather than address them all at once.

# file_time_type

The standard says that `trivial-clock` is an implementation-defined type that satisfies
the C++ concept `TrivialClock` (all this means is that this is a clock that would make the
`chrono` library happy) and is sufficient to represent the resolution and range of the file time
values offered by the filesystem. What the fuck, standard committee! Right off the bat,
we have something that could differ between filesystems and thus we can't really pass these
values between file systems.

See [http://en.cppreference.com/w/cpp/concept/TrivialClock](http://en.cppreference.com/w/cpp/concept/TrivialClock)
for something readable about `TrivialClock`.

We could take the easy route. Since all the `chrono` clocks meet the `TrivialClock` requirements,
we could just do this:

```
using file_time_type = std::chrono::time_point<std::chrono::system_clock>;
```

Let's call that the v0 implementation.

We're going to put a line in the sand. Our clock is 64-bit nanoseconds with a zero at 2000/01/01 00:00:00 UTC.
This has the happy property that it's a superset of Windows `FILETIME` and most Linux and Unix filesystems
that I've ever heard of, a superset that is losslessly convertible.
