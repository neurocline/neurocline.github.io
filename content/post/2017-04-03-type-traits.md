---
categories: dev
date: 2017-04-03T09:00:00Z
tags: c++
title: Type traits
url: /2017/04/03/type-traits/
---

Type traits in conjunction with `enable_if` are turning into a required foundation for writing
template classes. Consider `std::filesystem::path`: this has overloads that require help in order
for the compiler to disambiguate some of the required constructors and operators.

Let's start with an interesting base, `integral_constant`. This turns a constant into a type,
which means that templates can use it to control which template gets instantiated.

A modern version might look like this:

{{< highlight c++ >}}
template<class T, T val>
struct integral_constant
{
    static constexpr T value = val;
    using value_type = T;
    using type = integral_constant;

    constexpr operator value_type() const noexcept { return value; }
    constexpr value_type operator()() const noexcept { return value; }
};

template <bool B> using bool_constant = integral_constant<bool, B>;

using true_type = bool_constant<true>;
using false_type = bool_constant<false>;
{{< / highlight >}}

The `bool_constant` specialization is provided as of C++17. However, except for the modern syntax
(e.g. `using` instead of `typedef`), this template is basic C++.

The entire reason for this is the side effects; otherwise, it's a very expensive (in lines of code)
way to represent a constant.

Starting with the most simple usage, we have two unique types `true_type` and `false_type` that are
specializations of `bool_constant`, itself a specialization of `integral_constant`. We can do
tag dispatching to select between two options at compile time. Let's say we have a print function and
we want integer and floating point versions

{{< highlight c++ >}}
template<typename T>
void print_impl(T val, true_type)
{
	printf("%d", val);
}
template<typename T>
void print_impl(T val, false_type)
{
	printf("%f", val);
}
template<typename T>
void print(T val)
{
	print_impl(val, is_integral<T>());
}
{{< / highlight >}}

Here, `is_integral` is a type trait that inherits from `bool_constant` and returns `true_type` if
we have a type that is integral, e.g. `int`, `short`, etc. We could use normal overloading, but the promotion
rules often defeat our attempts at overloading.

Of course, as you see, we almost never use `true_type` and `false_type` directly. Instead, they are the
base type for type traits.

## void_t

C++17 has `void_t`:

- [std::void_t](http://en.cppreference.com/w/cpp/types/void_t)

and this is simply:

{{< highlight c++ >}}
template< class... >
using void_t = void;
{{< / highlight >}}

with the example being

{{< highlight c++ >}}
template <typename T, typename = void>
struct is_iterable : std::false_type {};
template <typename T>
struct is_iterable<T, std::void_t<decltype(std::declval<T>().begin()),
                                  decltype(std::declval<T>().end())>>
    : std::true_type {};
{{< / highlight >}}

where `is_iterable` is a type trait and has a `true` value if type `T` is an iterable type, determined
by the fact that 

An earlier version was proposed in:

- [A SFINAE-Friendly std::iterator_traits](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3844.pdf)
