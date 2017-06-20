---
categories: dev
date: 2017-03-31T07:30:00Z
tags: c++
title: 'Writing std::filesystem: Path'
url: /2017/03/31/writing-std-filesystem-path/
---

Now we get to more complicated parts.

# path

The `path` class abstracts the idea of a filesystem path. First off, let me just say: this is
absurd ridiculous overkill. Just say that a path is a set of arbitrary strings of non-zero bytes
joined together by the path separator `/`, have some shim behavior for Windows, and be done with
it. Microsoft really needs to join with the sane part of the world, ditch the use of the backslash
character `\\` for path separators, turn drive letters into something reasonable, kill the
insanity that is universal paths. Blah blah. Sigh.

According to the standard, an object of class `path` represents a path and contains a pathname.
You can have a `path` object that doesn't actually map to an existing path in a filesystem - for
one, you couldn't create a new file if paths could only point to existing files.

Since `path` has a lot of functionality, let's start with the bare minimum:

```c++
class path
{
public:
	using value_type = char;
	using string_type = std::basic_string<value_type>
	static constexpr value_type preferred_separator = '/';

	path() noexcept;
	path(path&& p) noexcept;

private:
	string_type pathstring;
};
```

This `path` isn't quite useful, because there's no way to modify a path. But we could create
an empty one. Let's talk about what we have.

The first abstraction is in the type of character - all the standard says is that `value_type`
is a `typedef` for the operating system dependent encoded character type used to represent
pathnames. For many filesystems, either `char` or `unsigned char` is appropriate, because
filesystem paths are either just strings of bytes, or UTF-8 characters which can just be
treated as strings of bytes most of the time.

The second abstraction is in the path separator. Once again, this is operating system
specific and so `path` can't be passed to cross-platform code.

Note that `path` is not a template parameterized on these values, like say how `basic_string`
works, but instead these are platform-specific values. So one could imagine our code looking
like this:

```c++
class path
{
public:
#ifdef _WIN32
	// Assume that we only work on Unicode builds
	using value_type = wchar_t;
	static constexpr value_type preferred_separator = L'\\';
#else
	// Every other filesystem is UTF-8/string of bytes
	using value_type = unsigned char;
	static constexpr value_type preferred_separator = '/';
#endif

	using string_type = std::basic_string<value_type>

	path() noexcept;
	path(path&& p) noexcept;

private:
	string_type pathstring;
};
```

## constructors

```c++
template <class InputIterator>
path(InputIterator first, InputIterator last) {}
```

But if we want to use SFINAE so we don't get template expansion started when it's not an iterator,
we can use `enable_if`.

```c++
template <class InputIterator,
	class = typename enable_if<>
path(InputIterator first, InputIterator last) {}
```
