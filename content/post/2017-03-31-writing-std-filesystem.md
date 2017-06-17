---
categories: dev
date: 2017-03-31T01:30:00Z
tags: c++
title: Writing std::filesystem
url: /2017/03/31/writing-std-filesystem/
---

Let's write our own implementation of std::filesystem. Here's what we're going to work from.

- [C++17 draft standard](http://open-std.org/JTC1/SC22/WG21/docs/papers/2016/n4618.pdf) - see 27.10 File systems
- [File System Technical Specification](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4100.pdf) - essentially what's in the standard
- [Boost Filesystem Library](http://www.boost.org/doc/libs/1_42_0/libs/filesystem/doc/index.htm) - the TS came from Boost, after all
- [filesystem (Visual Studio 2015)](https://msdn.microsoft.com/en-us/library/hh874694.aspx)
- [libcstdc++ Filesystem](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/libstdc++/api/a01832.html)
- [Filesystem library (cppreference.com)](http://en.cppreference.com/w/cpp/experimental/fs)

This shipped as `std::experimental::filesystem` in Visual Studio 2015 (don't think VS2017 promoted it to
std::filesystem).

It shipped as `std::experimental::filesystem` in GCC 5.3 in libstdc++.

While it's implemented in libc++, also as `std::experimental::filesystem`,
it's not shipping with a Clang version yet (you need to build your own Clang/libc++ to enable it).

So, let's just write our own so we can start using it everywhere. Since we're starting from the fact of
the C++17 standard now including it, we will just create `std::filesystem`. This makes our life a little
easier at the moment, since no vendor has yet promoted it out of experimental.

# &lt;filesystem&rt;

This is somewhat defined for us in the standard. This goes in the namespace `std::filesystem`. We
wrap it like this:

{{< highlight c++ >}}
namespace std {
namespace filesystem {
inline namespace v1 {

... contents ...

} } } // namespace std::fileystem::v1
{{< / highlight >}}

Note the `inline namespace v1`. See [What are inline namespaces for?](http://stackoverflow.com/questions/11016220/what-are-inline-namespaces-for) for an explanation about using inline namespaces to help
in API versioning.

The classes in our namespace:

{{< highlight c++ >}}
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
{{< / highlight >}}

There are also a host of non-member functions to implement. We're going to group these by
functionality, rather than address them all at once.

Let's cover trivial stuff first.

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

{{< highlight c++ >}}
using file_time_type = std::chrono::time_point<std::chrono::system_clock>;
{{< / highlight >}}

Let's call that the v0 implementation.

We're going to put a line in the sand. Our clock is 64-bit nanoseconds with a zero at 2000/01/01 00:00:00 UTC.
This has the happy property that it's a superset of Windows `FILETIME` and most Linux and Unix filesystems
that I've ever heard of, a superset that is losslessly convertible.

# file_type

This one is easy, it's a mandated enum class. The values aren't mandated, but we'll put the exceptional
case as -1 and then just number others starting with 0.

{{< highlight c++ >}}
enum class file_type {
    not_found = -1,
    none = 0,
    regular,
    directory,
    symlink,
    block,
    character,
    fifo,
    socket,
    unknown,
};
{{< / highlight >}}

Note that we end our last enum item with a comma; this is a new best practice in my opinion, because
it means that adding new items can't go awry or cause extra lines to be edited.

# copy_options

This one is also easy, another mandated enum class without mandated values. This is a bitmask enum, which
means values will be combined together into a single field. So we just start
with zero and roll up by powers of 2 to create distinct bitmaps.

{{< highlight c++ >}}
enum class copy_options
{
    none                    = 0,

    // options for existing target files (pick at most one)
    skip_existing           = 0b000000001,      // 0x001,
    overwrite_existing      = 0b000000010,      // 0x002,
    update_existing         = 0b000000100,      // 0x004,

    // options for sub-directories
    recursive               = 0b000001000,       // 0x008,

    // options for symbolic links (pick at most one)
    copy_symlinks           = 0b000010000,       // 0x010,
    skip_symlinks           = 0b000100000,       // 0x020,

    // options for choosing the form of copying (pick at most one)
    directories_only        = 0b001000000,       // 0x040,
    create_symlinks         = 0b010000000,       // 0x080,
    create_hardlinks        = 0b100000000,       // 0x100,
};
{{< / highlight >}}

Along with the enum class, we need some operators so that we can combine copy_options together; in the spec,
it's stated as a requirement that the concept BitmaskType is supported: see [C++ concepts: BitmaskType](http://en.cppreference.com/w/cpp/concept/BitmaskType).

# perms

This one is also easy, another mandated enum class but with mandated values and the operators to support
the BitmaskType concept.

{{< highlight c++ >}}
enum class perms
{
    none                = 0,        // There are no permissions set for the file

    owner_read          = 0400,     // S_IRUSR: Read permission, owner
    owner_write         = 0200,     // S_IWUSR: Write permission, owner
    owner_exec          = 0100,     // S_IXUSR: Execute/search permission, owner
    owner_all           = 0700,     // S_IRWXU: read+write+execute, by owner

    group_read          = 0040,     // S_IRGRP: Read permission, group
    group_write         = 0020,     // S_IWGRP: Write permission, group
    group_exec          = 0010,     // S_IXGRP: Execute/search permission, group
    group_all           = 0070,     // S_IRWXG: read+write+execute, by group

    others_read         = 0004,     // S_IROTH: Read permission, others
    others_write        = 0002,     // S_IWOTH: Write permission, others
    others_exec         = 0001,     // S_IXOTH: Execute/search permission, others
    others_all          = 0007,     // S_IRWXO: read+write+execute, by others

    all                 = 0777,     // owner_all+group_all+others_all
    set_uid             = 04000,    // S_ISUID: Set-user-ID on execution
    set_gid             = 02000,    // S_ISGID: Set-group-ID on execution
    sticky_bit          = 01000,    // S_ISVTX: Operating system dependent
    mask                = 07777,    // all + set_uid + set_gid + sticky_bit

    unknown             = 0xFFFF,   // The permissions are not known, such as when a file_status object is created without specifying the permissions
    add_perms           = 0x10000,  // permissions() shall bitwise-or the perm argument's permissions bits to the file's current permissions bits
    remove_perms        = 0x20000,  // permissions() shall bitwise-and the complement of perm argument's permissions bits to the file's current permission bits
    symlink_nofollow    = 0x40000,  // permissions() shall change the permissions of symbolic links.
};
{{< / highlight >}}

# directory_options

This one is also easy, another mandated enum class without mandated values. This is a bitmask enum, which
means values will be combined together into a single field. So we just start with zero and roll up by powers
of 2 to create distinct bitmaps, and add the operators as per BitmaskType.

{{< highlight c++ >}}
enum class directory_options
{
    none                        = 0,    // (Default) Skip directory symlinks, permission denied is an error
    follow_directory_symlink    = 0b01, // Follow rather than skip directory symlinks
    skip_permission_denied      = 0b10, // Skip directories that would otherwise result in permission denied
};
{{< / highlight >}}

# space_info

This is defined in the standard, so hurray, but then the commentary about the `space()` function itself
has a bunch of as-if POSIX, instead of just stating the obvious.

Specifically, `capacity` is the total size of the filesystem in bytes; `free` is the size of the
unallocated part of the filesystem in bytes; and `available` is the number of bytes available to
a non-super user. As such, `available` can be less than `free`.

{{< highlight c++ >}}
struct space_info {
    uintmax_t capacity;
    uintmax_t free;
    uintmax_t available;
};
{{< / highlight >}}
