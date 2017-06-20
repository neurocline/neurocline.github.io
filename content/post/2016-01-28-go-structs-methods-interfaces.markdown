---
categories: dev
date: 2016-01-28T14:39:00Z
tags: go programming
title: 'Understanding Go: structs, methods, interfaces'
url: /2016/01/28/go-structs-methods-interfaces/
---

Go has a simple-looking object system that is just different enough from the C++ side
of things to cause mental model problems in experienced engineers starting out in Go.

Go has two kinds of types: structs and interfaces. Structs are concrete types, interfaces
are abstract types. Go is a strongly-typed language - even types that are synonyms are
distinct types.

# Structs and methods

When we restrict things to just structs and methods, there are few surprises. Let's look at a very simple example.

```go
package main

import "fmt"

func main() {
  var h *Hatch = nil
  h.Open()
}

type Hatch struct {
  label string
  x,y,z float
}

func (h *Hatch) Open() {
  fmt.Printf("opening the hatch\n")
}
```

If you run this, you get

```
opening the hatch
```

The function `Open` is a method with a pointer receiver, taking a pointer
to a instance of type `Hatch`. Although we create a pointer that is
nil, the variable still has a type: (`*Hatch`). We can thus call the method
`Open`; it gets a nil pointer, but we don't use that pointer in our code,
so we don't crash.

If we change the method to take a value receiver

```go
func (h Hatch) notify() {
    fmt.Printf("opening the hatch\n")
}
```

then we will crash if we try to run this:

```
panic: runtime error: invalid memory address or nil pointer dereference
[signal 0xc0000005 code=0x1 addr=0x0 pc=0x401074]

goroutine 1 [running]:
main.main()
        ./gotest/m.go:7 +0x34
exit status 2
```

There's a simple reason, Go has a
courtesy dereference so that a pointer-to-object can be used to invoke
a method with a value receiver, and obviously if you try to dereference
nil, you'll crash.

Let's look at this further. Methods are just functions with an implicit
first parameter that is the receiver object. These are equivalent in functionality and
probably equivalent in code:

```go
// regular function
func Open(h *Hatch) { ... }

var h *Hatch = nil
Open(h)

// method
func (h *Hatch) Open() { ... }

var h *Hatch = nil
h.Open()
```

The compiler is just matching up type signatures for receivers, not for full parameter
lists; this is why Go claims it does not support overloading. In the context of how
methods work, it is doing overloading, but just on the receiver type signature.

For methods, think of the struct type as being open to
having methods added to it via type matching. Each method we write is only good for one
specific type. A concrete type is the union of the struct
and the methods for that struct; the difference between Go and C++ is that in Go, anyone
can add to the methods for a struct type.

When we have value receivers, the method gets a copy of the object. When we have pointer
receivers, the method gets a copy of the pointer, but both pointers lead to the same object.
Also note that we can't have both - there is no overloading in Go, either for function
parameter signatures nor for receiver signatures.

Keep that in mind as we look at the four permutations:

- value type, value receiver
- pointer type, value receiver
- value type, pointer receiver
- pointer type, pointer receiver

### value type, value receiver

When we have a value sent to a value receiver, it's bland.

```go
// Go method
func (h Hatch) Open() { ... }
var h Hatch
h.Open()

// equivalent to
func Open(h Hatch)
var h Hatch
Open(h)
```

### pointer type, pointer receiver

When we have a pointer sent to a pointer receiver, it's as boring as the first case.

```go
// Go method
func (h *Hatch) Open() { ... }
var h *Hatch
h.Open()

// equivalent to
func Open(h *Hatch)
var h *Hatch
Open(h)
```

### value type, pointer receiver

When we have a value sent to a pointer receiver, we need to manufacture a pointer. This
works most of the time, but we can't always take the address of a value - for example,
the number 42 is a constant and has no storage we can get the address of.

```go
// Go method
func (h *Hatch) Open() { ... }
var h Hatch
h.Open()

// equivalent to
func Open(h *Hatch)
var h Hatch
Open(&h)
```

### pointer type, value receiver

When we send a pointer to a value receiver, we have to dereference the pointer.
That makes sense, and that's why we can get a panic that would not happen with pointer
receivers.

```go
// Go method
func (h Hatch) Open() { ... }
var h *Hatch = nil
h.Open()

// equivalent to
func Open(h Hatch)
var h *Hatch = nil
Open(*h)
```

Note that last line - we dereference the pointer to get the value, so that we can copy
it when we call the function. If we have a nil pointer, we'll get an exception at this point.

# Interfaces

Interfaces are abstract types. There is no storage. They are best thought of as contracts. However,
you can have a value of interface type, or a pointer of interface type. Kind of.

