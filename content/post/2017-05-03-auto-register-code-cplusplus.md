---
categories: dev
date: 2017-05-03T04:06:00Z
tags: C++
title: Auto-registering code in C++
url: /2017/05/03/auto-register-code-cplusplus/
---

It is possible to automatically stitch code together without explicit lines to do so, by
using global objects and their constructors. This is a technique used by unit test frameworks,
among others.

What does this look like?

```c++
class ObjectOne
{
public:
	ObjectOne() {}
} ObjectOne_instance;
```

This will declare a class and create a global instance of it. At runtime, before main, all the constructors
for global objects are called, including `ObjectOne::ObjectOne()`. Of course, this constructor does nothing,
but it does run (usually - see linker issues).

We want to do something specific. Assume we have a base class `Object` and a `Object::Register`
function that adds this object to some list. We're going to make this a static function.

```c++
class Object
{
public:
	Object() {}
	static void Register(Object* o) { Add(Registry(), o); }

};

class ObjectOne : Object
{
public:
	ObjectOne() { Register(this); }
} ObjectOne_instance;
```

Now, when we run, this will call `Register` with our `this` pointer. We would like to have
this insert into a list of Objects. We can do this without allocation if we use an
intrusive linked list where the pointers are part of each (statically allocated) object.

```c++
class Object
{
public:
	Object() { next = nullptr; last = this; }
	static void Register(Object* o) { Add(Registry(), o); }
	static Object& Registry() { static Object registry; return registry; }
	static void Add(Object& head, Object* o)
	{
		if (head.next == nullptr)
			head.next = o;
		else
			head.last->next = o;
		head.last = o;
	}
	Object* next;
	Object* last;
};

class ObjectOne : public Object
{
public:
	ObjectOne() { Register(this); }
} ObjectOne_instance;
```

Of course, it's not enough to just build a list. We want to use it. In particular, we
want to call a `Run()` method on each object in the list.

```c++
class Object
{
public:
	Object() { next = nullptr; last = this; }
	static void Register(Object* o) { Add(Registry(), o); }
	static Object& Registry() { static Object registry; return registry; }
	static void Add(Object& head, Object* o)
	{
		if (head.next == nullptr)
			head.next = o;
		else
			head.last->next = o;
		head.last = o;
	}
	virtual void Run() {}
	static void RunAll()
	{
		for (auto n = Registry().next; n != nullptr; n = n->next)
			n->Run();
	}
	Object* next;
	Object* last;
};

class ObjectOne : public Object
{
public:
	ObjectOne() { Register(this); }
	void Run() { /* do something */ }
} ObjectOne_instance;

int main(int argc, char* argv[])
{
    Object::RunAll();
}
```

This is a lot of scaffolding to do nothing. But we have a building block that doesn't allocate
before main, adds all our objects into a list (in a compiler-specific order), and then lets
us iterate the list at some point.

We can hide some of the boilerplate with macros. Instead of declaring each new class to
register a `Run` function, we have a macro create and register the object for us, and we just
supply the body of the `Run` function.

```c++
#define OBJECT(name) \
class Object ## name : public Object \
{ \
public: \
    Object ## name () { Register(this); } \
    void Run(); \
} Object ## name ## instance; \
void Object ## name :: Run()

OBJECT(one)
{
    printf("Hello from one\n");
}
```

The idea is that the macro and the `Object` class are in some header file that we include, and
then our task is just to write `OBJECT` bodies. We have to give each one a unique name, but this
is no different than the need to give each global function a unique name.

This is a factory of sorts. What if we want more than one factory? Well, we can parameterize
the factory itself, and pass that to the `OBJECT` macro, either as a parameter, or by nesting
`OBJECT`s inside a scope we create.

And we can continue to elaborate. We can provide sort orders - once all the objects are
registered, we could re-sort however we want. We could have multiple lists either as explicit
separate factories, or with parameters in the objects and passed to the `RunAll` method.

Keep it simple. Don't add functionality you won't need.

TBD - add note about linker challenges when doing this.
