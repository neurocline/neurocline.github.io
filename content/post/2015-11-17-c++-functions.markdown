---
categories: dev
date: 2015-11-17T10:48:00Z
tags: c++ programming
title: C++ functions
url: /2015/11/17/c++-functions/
---

The subroutine is the original programming innovation, introduced in 1952
(see [The Use of Sub-routines in Programmes](http://www.laputan.org/pub/papers/wheeler)).
Before subroutines, we used to program by writing the whole
program in a single block of code. Libraries were just collections of snippets;
we copied lines of code from libraries and inserted them in place in program.

In C++, we call these functions, and we have a variety of function types to
choose from.

* plain functions
* pointer to function
* class member functions
* functors, operator()
* pointer to member function
* template functions
* lambda expressions
* inline functions
* constexpr functions
* bind expressions

In all of these, a function has the same basic definition: a name, a list of zero or
more parameters, a return type, and a body. Functions are invoked through the call
operator, which is a pair of parentheses. The return type doesn't get or need a name,
because it is only operated on anonymously, through a return statement.

## Plain old function

There is the original kind of function, inherited from C, and still relevant today.
It takes one or more parameters, and can return a value (which could be
a reference or an efficient move of a value, and not just a copy of a value).

```c++
void printv(int param)
{
    std::cout << param << std::endl;
}

int factorial(int val)
{
    int ret = 1;
    while (val > 1) ret *= val--;
    return ret;
}

int sum2(int augend, int addend)
{
    return augend + addend;
}
```

In C++, functions can be overloaded on number and type of parameter, but not on
return type: originally, overloading worked because C++ did name mangling
to encode types into the function name, and the return type was not part
of the name-mangling.

OK, yes, it still works that way today, but there's no real need. We're smart
people, and we could do overloading without needing name mangling.

```c++
void printv(int numerator, int denominator)
{
    std::cout << numerator << "/" << denominator << std::endl;
}

int printv(std::complex<int> param)
{
    std::cout << param << std::endl;
}

enum color { red=0, blue=1 };
void printv(color param)
{
    char* cnames[2] = { "red", "blue" };
    std::cout << cnames[param] << std::endl;
}
```

You can overload on integral types that have promotion rules, but this
can be tricky. You can declare the functions, and they'll get called, but
unless you master the promotion rules, you can end up with situations
where the wrong overload is called.

```c++
void printv(int param)
{
    std::cout << param << " (int)" << std::endl;
}

void printv(short param)
{
    std::cout << param << " (short)" << std::endl;
}

int printone()
{
    short i = 0;
    printv(i);     // this prints "0 (short)"
    printv(i + 1); // this prints "1 (int)"
}
```

## Pointer to function

A function definition matches a function name to a function body. Further
use of that function name in a call operator results in calling the function
body in that definition.

Just like we can have pointers to data types, we can have pointers to
function types, assign bodies to pointers, and then invoke bodies through
pointers.

```c++
void funcptr()
{
    int(*pfact)(int) = nullptr; // a pointer to a function
    pfact = factorial;
    pfact = &factorial; // & is allowed but not required

    pfact(10);
    (*pfact)(10); // dereference is allowed but not required
}
```

The C++11 `decltype` keyword can come in handy when declaring function
pointers. Since `decltype` returns the exact function type, we need to add a `*`
to declare a pointer:

```c++
decltype(factorial) *pfact = nullptr; // copy function signature from factorial
```

## Class member function

Classes can have members that are functions. These behave almost exactly
like plain old functions, except that each function has an automatic
first parameter called "this" that is the pointer to the object. And
inside the body of class member functions, the this is implicit; if
name lookup finds a class member function or variable, the this pointer
is applied automatically.

Here we have a memoizing factorial implemented as a class:

```c++
class Factorial
{
public:
    int factorial(int val)
    {
        auto r = cache.find(val);
        if (r != cache.end())
            return r->second;
        int ret = 1;
        int v = val;
        while (v > 1) ret *= v--;
        cache.insert(std::pair<int, int>(val, ret));
        return ret;
    }
    std::map<int, int> cache;
};

int printfact()
{
    Factorial f;
    printf("factorial of %d is %d\n", 9, ff.factorial(9));
}
```

While the results are still out on object-oriented programming itself, the
idea of structured programming is still running strong, and classes without
inheritance are a form of structured programming.

## Functors with operator()

The call operator can be overloaded. When it is overloaded, this creates
a functor, a function-like object. The main advantage of functors is
state, and we often use this pattern in callbacks.

Consider the classic C style callback.

## Template function

There is the C++ template function, which is still just a function
that can take parameterized types. It can return parameterized types as well,
although you can't overload on just return type.

## Reference

[The Dreaded Two-Phase Name Lookup](http://blog.llvm.org/2009/12/dreaded-two-phase-name-lookup.html)

[Dependent name lookup for C++ templates](http://eli.thegreenplace.net/2012/02/06/dependent-name-lookup-for-c-templates)

[C++ Functors - and their uses](http://stackoverflow.com/questions/356950/c-functors-and-their-uses)
