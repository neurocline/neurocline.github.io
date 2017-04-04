---
layout: post
title:  "Writing std::filesystem: Path (part 2)"
date:   2017-04-03 3:30:00 PM
categories: dev
tags: c++
---

OK, reboot on `std::filesystem::path`. There are bits of it that require C++ trickery; my first
impulse was to dive into the hard parts first, but I ran into expression SFINAE hell with
Visual Studio 2015 (which I need to support).

So instead I want to get the bulk of `path` done before I tackle the template functions that
have complications around them. So ignore everything said before, let's try again.

# 27.10.8 Class path

## `value_type` and `preferred_separator`

A `path` has a `value_type` that is platform-specific; this is meant to map directly to the
platform filesystem character set. It also has a `preferred_separator` that is the native
separator; this seems to have been concocted mainly for Windows support. It also has
a `string_type`, but this is just `basic_string<value_type`. So we have

{% highlight c++ %}
    #if defined(_WIN32) && defined(UNICODE)
    // Windows Unicode build is UCS16LE
    using value_type = wchar_t;
    static constexpr value_type preferred_separator = L'\\';
    // Otherwise, this is MBCS, e.g. single-byte
    #elif defined(_WIN32) && !defined(UNICODE)
    // Otherwise, this is MBCS, e.g. single-byte
    using value_type = char;
    static constexpr value_type preferred_separator = '\\';
    #else
    using value_type = char;
    static constexpr value_type preferred_separator = '/';
    #endif

    using string_type = basic_string<value_type>;
{% endhighlight %}

This is part of what is called native format; fileystems accept and return path strings in native
format. Windows Unicode builds use UTF16LE as the filesystem character set and separate path components
with '\\'. The actual definition of native pathname format is "The operating system dependent pathname
format accepted by the host operating system".

## 27.10.8.1 Generic pathname format

Unlike the native format, generic format is specified. This is so that general-purpose code can
form and manipulate paths, presuming that there are conversion functions from native to generic,
and from generic to native.

For Windows and POSIX, this is mostly a moot point, because generic format is acceptable as a native path,
you can pass either as function arguments, and paths for files and directories share the same syntax.
This hedge exists so that other filesystems can be mapped to a common format.

On Windows, native format uses the preferred separator '\\', whereas generic paths prefer to join
paths with '/'. Obviously POSIX uses '/' natively, and Windows NT filesystem functions allow '/'
in most cases (with the exception of so-called UNC file paths which have to start with `\\\\`).

## 27.10.8.2 path conversions

We are going to sidestep path conversions completely to begin with, since we can use either generic
paths or native paths at will in calls.

## 27.10.8.3 path requirements

There are a number of function templates that, in the spec, take a paramter named `Source`. This
is a stand-in for a family of function templates; `Source` is either a specialization of 
`basic_string` or `basic_string_view`, or `iterator_traits<decay_t<Source>>::value_type` is valid
and denotes a (possibly const) encoded character type.

This applies to constructors, assignments, appends and concatenation, as these are the places
that data are passed to `path`.

## 27.10.8.4.1 path constructors

There are 5-7 different constructors.

- empty path constructor: `path()`
- construct from existing path: `path(const path&)`, `path(path&&)`.

# Reference

This is the definition of `std::filesystem::path` from the C++ standard section 27.10.8.

{% highlight c++ %}
namespace std::filesystem {
class path {
public:
	using value_type = see below ;
	using string_type = basic_string<value_type>;
	static constexpr value_type preferred_separator = see below ;

	// 27.10.8.4.1, constructors and destructor
	path() noexcept;
	path(const path& p);
	path(path&& p) noexcept;
	path(string_type&& source);
	template <class Source>
	  path(const Source& source);
	template <class InputIterator>
	  path(InputIterator first, InputIterator last);
	template <class Source>
	  path(const Source& source, const locale& loc);
	template <class InputIterator>
	  path(InputIterator first, InputIterator last, const locale& loc);
	~path();

	// 27.10.8.4.2, assignments
	path& operator=(const path& p);
	path& operator=(path&& p) noexcept;
	path& operator=(string_type&& source);
	path& assign(string_type&& source);
	template <class Source>
	  path& operator=(const Source& source);
	template <class Source>
	  path& assign(const Source& source)
	template <class InputIterator>
	  path& assign(InputIterator first, InputIterator last);

	// 27.10.8.4.3, appends
	path& operator/=(const path& p);
	template <class Source>
	  path& operator/=(const Source& source);
	template <class Source>
	  path& append(const Source& source);
	template <class InputIterator>
	  path& append(InputIterator first, InputIterator last);

	// 27.10.8.4.4, concatenation
	path& operator+=(const path& x);
	path& operator+=(const string_type& x);
	path& operator+=(basic_string_view<value_type> x);
	path& operator+=(const value_type* x);
	path& operator+=(value_type x);
	template <class Source>
	  path& operator+=(const Source& x);
	template <class EcharT>
	  path& operator+=(EcharT x);
	template <class Source>
	  path& concat(const Source& x);
	template <class InputIterator>
	  path& concat(InputIterator first, InputIterator last);

	// 27.10.8.4.5, modifiers
	void clear() noexcept;
	path& make_preferred();
	path& remove_filename();
	path& replace_filename(const path& replacement);
	path& replace_extension(const path& replacement = path());
	void swap(path& rhs) noexcept;

	// 27.10.8.4.6, native format observers
	const string_type& native() const noexcept;
	const value_type* c_str() const noexcept;
	operator string_type() const;
	template <class EcharT, class traits = char_traits<EcharT>,
	          class Allocator = allocator<EcharT>>
	  basic_string<EcharT, traits, Allocator>
	  string(const Allocator& a = Allocator()) const;
	std::string string() const;
	std::wstring wstring() const;
	std::string u8string() const;
	std::u16string u16string() const;
	std::u32string u32string() const;

	// 27.10.8.4.7, generic format observers
	template <class EcharT, class traits = char_traits<EcharT>,
	          class Allocator = allocator<EcharT>>
	  basic_string<EcharT, traits, Allocator>
      generic_string(const Allocator& a = Allocator()) const;
	std::string generic_string() const;
	std::wstring generic_wstring() const;
	std::string generic_u8string() const;
	std::u16string generic_u16string() const;
	std::u32string generic_u32string() const;

	// 27.10.8.4.8, compare
	int compare(const path& p) const noexcept;
	int compare(const string_type& s) const;
	int compare(basic_string_view<value_type> s) const;
	int compare(const value_type* s) const;

	// 27.10.8.4.9, decomposition
	path root_name() const;
	path root_directory() const;
	path root_path() const;
	path relative_path() const;
	path parent_path() const;
	path filename() const;
	path stem() const;
	path extension() const;

	// 27.10.8.4.10, query
	bool empty() const noexcept;
	bool has_root_name() const;
	bool has_root_directory() const;
	bool has_root_path() const;
	bool has_relative_path() const;
	bool has_parent_path() const;
	bool has_filename() const;
	bool has_stem() const;
	bool has_extension() const;
	bool is_absolute() const;
	bool is_relative() const;

	// 27.10.8.4.11, generation
	path lexically_normal() const;
	path lexically_relative(const path& base) const;
	path lexically_proximate(const path& base) const;

	// 27.10.8.5, iterators
	class iterator;
	using const_iterator = iterator;
	iterator begin() const;
	iterator end() const;

private:
	string_type pathstring; // exposition only
};
}
{% endhighlight %}
