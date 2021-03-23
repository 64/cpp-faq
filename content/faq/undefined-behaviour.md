---
title: "What is 'Undefined Behaviour'?"
date: 2021-03-23T02:26:05Z
slug: "ub"
tags: ["C", "C++"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Undefined Behaviour (also known as **UB**) occurs when you violate certain language rules. These rules include (but are not limited to): dereferencing a null pointer, signed integer overflow, accessing through a [dangling reference](https://en.cppreference.com/w/cpp/language/reference#Dangling_references), or accessing through an unaligned pointer.

**When undefined behaviour occurs, the C and C++ standards do not place any restrictions on what your program might do.** In other words, your program may crash, or continue execution, or call some seemingly unrelated piece of code, or print 42 and open [xkcd](https://xkcd.com/) in your web browser. In practice, you *may* be able to reason about how your compiler will respond to UB, and in some cases compilers will guarantee that certain operations are well-defined, but for maximum portability you should aim to keep your programs UB-free.

## Examples of UB

```cpp
int x;
std::cout << x; // UB: x was used before being initialized

int y = 0;
std::cout << y; // OK
```
```cpp
int arrayA[10];
for(int x : arrayA)
    std::cout << x << ' '; // UB: array elements are uninitialized

int arrayB[10] = {};
for(int x: arrayB)
    std::cout << x << ' '; // OK
``` 

## Avoiding UB
Undefined behaviour can be difficult to diagnose. Always [compile with warnings enabled]({{< ref "faq/enable-warnings.md" >}}), but note that compilers can't detect all problems at compile time. Tools like [ASan](https://en.wikipedia.org/wiki/AddressSanitizer), [UBSAN](https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html) and [Valgrind](https://valgrind.org/docs/manual/quick-start.html#quick-start.mcrun) perform checks on your code at runtime and are good at catching invalid memory accesses, so are recommended for use during development.

