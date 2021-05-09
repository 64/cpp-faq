---
title: "What are 'Unspecified' and 'Implementation-Defined' behavior?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Unlike *undefined* behavior, *unspecified* and *implementation-defined* behavior occur all the time in a normal
C++ program.
Making use of unspecified/implementation-defined behavior is unavoidable, but relying on specific behavior can be fatal.

The C++ standard does not specify what happens in every scenario, so the implementation has the freedom to implement
behavior, values, macro definitions, and more in multiple possible ways.

## Unspecified

Things unspecified by the standard are not necessary to define, but still valid.
The program should not crash or show other abnormal behavior as a result of unspecified behavior.
However, in most cases, the exact behavior is not predictable.

### Common Examples

The following things are unspecified (not exhaustive):
- whether all string literals are distinct (they might have different addresses)
- the initial value of storage allocated by `new` (often zero when getting new memory from the OS)
- evaluation order of operands of some operators (for `a + b`, `b` might be evaluated first)
- amount of time it takes for a thread to make progress (but eventual progress is guaranteed)
- underlying type of `enum E {A};` (usually `int`)

Unspecified behavior can be as predictable as *"we probably know what happens"*, such as `int` being used for the last
example.
However, it can also be nonsensical to define, such as the initial value of storage allocated by `new`.
The `new` operator usually uses `std::malloc` to allocate storage, but the initial value of its storage is
indeterminate.

From the C++20 standard:

> **unspecified behavior**
> *behavior, for a well-formed program construct and correct data, that depends on the implementation*
>
> Note: The implementation is not required to document which behavior occurs. \[...\]

See [**3.31 \[defns.unspecified\]**](https://isocpp.org/files/papers/N4860.pdf)

## Implementation-Defined

*Implementation-defined* is a lesser evil than *unspecified*, because the implementation details **must** be specified.
Relying on implementation-defined behavior can still cause portability issues such as code working only on one operating
system, but at least, these portability issues are known and predictable.

### Common Examples

The following things are implementation-defined (not exhaustive):
- the width of fundamental types such as `int` and `long`
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

See [**3.13 \[defns.impl.defined\]**](https://isocpp.org/files/papers/N4860.pdf)

## See Also

- https://en.cppreference.com/w/cpp/language/types#Data_models
