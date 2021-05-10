---
title: "What are 'Unspecified' and 'Implementation-Defined' behavior?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Unlike [*undefined* behavior](../undefined-behaviour), *unspecified* and *implementation-defined* behavior occur all the
time in a normal C++ program.
Making use of unspecified/implementation-defined behavior is unavoidable, but relying on specific behavior can be fatal.

The C++ standard does not specify what happens in every scenario, so the implementation has the freedom to implement
behavior, constants, macro definitions, and more in multiple possible ways.

## Unspecified

Behavior unspecified by the standard is not necessary to document, but still valid.
The *range* of possible behavior behavior can be predictable and is sometimes even specified explicitly by the
C++ standard.
However, in most cases, the exact behavior can not be predicted.

### Common Examples

The following things are unspecified (list not exhaustive):
- whether all string literals are distinct (they might have different addresses)
- the initial value of storage allocated by `new` (often zero when getting new memory from the OS)
- evaluation order of operands of some operators (for `A + B`, `B` might be evaluated before `A`)
- amount of time it takes for a thread to make progress (but eventual progress is guaranteed)
- whether references require any storage (they usually do, just like pointers)

Unspecified behavior can be as predictable as *"we probably know what happens"*, such as a reference requiring some
storage when used as a class member.
However, it can also be nonsensical to define, such as the initial value of storage allocated by `new`.
The `new` operator usually uses `std::malloc` to allocate storage, but the initial value of `std::malloc`'s storage is
indeterminate.

From the C++20 standard:

> **unspecified behavior**
> *behavior, for a well-formed program construct and correct data, that depends on the implementation*
>
> Note: The implementation is not required to document which behavior occurs. \[...\]

See [**3.31 \[defns.unspecified\]**](https://timsong-cpp.github.io/cppwp/n4861/intro.defs#defns.unspecified)

## Implementation-Defined

*Implementation-defined* is a lesser evil than *unspecified*, because the implementation details **must** be
documented.
Relying on implementation-defined behavior can still cause portability issues such as code working only on one operating
system, but at least, these portability issues are known and predictable.

### Common Examples

The following things are implementation-defined (list not exhaustive):
- the exact size of fundamental types such as `int` and `long`
- definition of the `NULL` macro (possibly `0`, `((void*)0)`, or `nullptr`)
- conversions between pointers and integers (`reinterpret_cast<std::uintptr_t>(...)`)
- nesting limit for `#include` directives
- choice of larger or smaller value for `float` literals (round up or down if necessary)
- operating systems on which implementation depends
- value of `pow(0,0)` (usually `1`)

All of these details are documented somewhere, whether it is in standard library headers, manual pages of the OS, etc.
Problems only arise when the developer relies on one particular implementation.
A common example is assuming that `long` will be at least 64 bits in size, when actually, only 32 bits are guaranteed by
the standard.
Windows requires `long` to be 32 bits, while Unix-like operating systems use 64 bits.

From the C++20 standard:

> **implementation-defined behavior**
> *behavior, for a well-formed program construct and correct data, that depends on the implementation and that each
implementation documents*

See [**3.13 \[defns.impl.defined\]**](https://timsong-cpp.github.io/cppwp/n4861/intro.defs#defns.impl.defined)

## See Also

- https://en.cppreference.com/w/cpp/language/types#Data_models
- https://en.wikipedia.org/wiki/Unspecified_behavior
- https://timsong-cpp.github.io/cppwp/n4861/impldefindex
