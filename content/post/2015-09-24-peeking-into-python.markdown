---
categories: dev
date: 2015-09-24T20:47:00Z
tags: python, debugging
title: Peeking into Python
url: /2015/09/24/peeking-into-python/
---

There are a number of ways to peek into Python code and data.

## Built-in functions for output

The [built-in primitives](https://docs.python.org/2/library/functions.html) that are relevant are

* ```dir()```
* ```repr()```
* ```vars()```

```dir(object)``` shows all the attributes for the object. Of course, not all objects
have attributes. It's really made for use at an interactive prompt, not as
an in-code method. Basically, it either calls ```__dir__()```, or it looks at
the ```__dict__``` attribute and shows some of the information.

```repr(object)``` returns a printable representation of an object. Again, this sounds
good until you use it, because anything other than built-in types just returns
a string with the class name and the address of the object. Still, it's the gateway
to something useful if you also add ```__repr__()``` methods to your classes, because ```repr()```
will call the ```__repr__``` method if it exists. Of course, the downside is that you need
a systematic and consistent approach, and you have to write that code yourself.

```vars(object)``` is more useful for printing than ```dir``` - it returns
the ```__dict__``` attribute, which means that if you call ```print(vars(object))``` you'll
see keys and values together, whereas ```dir()``` just shows the keys.

## Useful classes for output

The ```pprint``` module sounds useful until you use it. Its utility is largely restricted
to pretty-printing, it doesn't give you extra disassembly powers on its own. But
see [pprint--Data pretty printer](https://docs.python.org/2/library/pprint.html).

The [```inspect```](https://docs.python.org/2/library/inspect.html) module is a lot more useful, but it doesn't do output, it just lets
you get information about objects.

## Built-in functions for introspection

Some built-ins are useful for testing objects:

* ```type```
* ```isinstance```
* ```hasattr```

```type(object)``` returns a string indicating the type of the object. As such, it can
be used for both output and testing.

```isinstance(object, classinfo)``` returns ```True``` if ```object``` is an instance
of ```classinfo``` (including inheritance). In general, it's more appropriate to use
```isinstance``` for type testing because it does handle inheritance.

The ```types``` module is useful for both ```type``` and ```isintance``` use. And type
output needs this kind of introspection so it knows what it should print.

## Links

[Guide to Python introspection](http://www.ibm.com/developerworks/library/l-pyint/)
