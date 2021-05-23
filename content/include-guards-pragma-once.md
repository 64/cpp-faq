---
title: "What are 'Include Guards' and `#pragma once`?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Include guards, or sometimes called *macro guards*, *header guards*, or *file guards* are a common C/C++ idiom that
allows including a header file multiple times safely.
The non-standard preprocessor directive `#pragma once` is an almost equivalent alternative to this idiom.

## The Problem

The preprocessor directive `#include` effectively copies the named header into our source file.
If we `#include` a header file more than once, this will result in redefinitions and possibly compiler errors.
Consider this example:
```cpp
// mymath.hpp

inline int sqr(int x) {
    return x * x;
}
```
```cpp
// main.cpp

#include "mymath.hpp"
#include "mymath.hpp"
#include <iostream>

int main() {
    int x;
    std::cin >> x;
    std::cout << sqr(x) << '\n';
}
```
This code does not compile, because we have included `mymath.hpp` twice.
After these two includes are processed by the preprocessor, `main.cpp` (ignoring comments) will be the following:
```cpp
inline int sqr(int x) {
    return x * x;
}
inline int sqr(int x) {
    return x * x;
}
#include <iostream>

int main() {
    int x;
    std::cin >> x;
    std::cout << sqr(x) << '\n';
}
```
The program is ill-formed and we get a compiler error:
```txt
<source>:4:12: error: redefinition of 'sqr'
inline int sqr(int x) {
           ^
<source>:1:12: note: previous definition is here
inline int sqr(int x) {
           ^
```
At this point you might think: *"Okay then just don't include it twice and the problem is solved."*.
But including headers multiple times is not always easy to avoid.
We could have two more header files `vectormath.hpp` and `extramath.hpp`, both of which `#include "mymath.hpp"`.
The include-tree would look as follows:
```txt
main.cpp
  └─ vectormath.hpp
       └─ mymath.hpp
  └─ extramath.hpp
       └─ mymath.hpp
```
`main.cpp` indirectly includes `mymath.hpp` twice.
And this is by no means unusual.
Smaller standard library headers such as `<utility>` or `<new>` are included by many other headers like `<vector>`, so
each source file may have many copies of `<new>`.

## Solution A: Include Guards

Include guards are put around the entirety of the header file:
```cpp
// mymath.hpp
#ifndef MYPROJECT_MYMATH_HPP_GUARD
#define MYPROJECT_MYMATH_HPP_GUARD
inline int sqr(int x) {
    return x * x;
}
#endif
```
This idiom works by letting the preprocessor skip the entirety of the header file if `MYPROJECT_MYMATH_HPP_GUARD` has
already been defined.
Only the first time we include the header file will `MYPROJECT_MYMATH_HPP_GUARD` be undefined.
We `#define` this macro inside of the `#ifndef` block, so all subsequent copies of the header will not be processed.
Here is how this works if we process the first two includes in `main.cpp`:
```cpp
#ifndef MYPROJECT_MYMATH_HPP_GUARD // not defined yet, so we enter the ifndef-block
#define MYPROJECT_MYMATH_HPP_GUARD // we define the guard
inline int sqr(int x) {
    return x * x;
}
#endif                             // we exit the block
#ifndef MYPROJECT_MYMATH_HPP_GUARD // the guard has already been defined => skip
#define MYPROJECT_MYMATH_HPP_GUARD // this is not processed
inline int sqr(int x) {
    return x * x;
}
#endif                             // we stop skipping code here
#include <iostream>

int main() {
    int x;
    std::cin >> x;
    std::cout << sqr(x) << '\n';
}
```
After processing the `#ifndef` blocks, the code will effectively look like this:
```cpp
#define MYPROJECT_MYMATH_HPP_GUARD
inline int sqr(int x) {
    return x * x;
}
#include <iostream>

int main() {
    int x;
    std::cin >> x;
    std::cout << sqr(x) << '\n';
}
```
We could now include our `mymath.hpp` header as many times as we want in as many headers as we want without worrying
about redefining our functions and receiving compiler errors.

Note: `MYPROJECT_MYMATH_HPP_GUARD` is an arbitrary definition, we could name it something else too.
But the name should be long and unique so we don't accidentally use the same guard twice.

## Solution B: #pragma once

`#pragma once` is a non-standard preprocessor directive which tells the preprocessor to skip the current header if it
has already been included.
The goal is to achieve the same effect as our include guards, but more concisely.
We can use it like this:
```cpp
// mymath.hpp
#pragma once
inline int sqr(int x) {
    return x * x;
}
```
At first glance, this is much more elegant than include guards and we could just use `#pragma once` in all cases.
However, as already mentioned, `#pragma` directives are not part of the C or C++ standard, so the behavior is dependent
on the compiler.
One commonly stated advantage of `#pragma once` is that it can be faster, since the preprocessor doesn't need to read
the entire file to find a matching `#endif` for the `#ifndef` directive.
The file contents can be skipped at an earlier stage.

It is worth noting that identifying whether a file is included multiple times is not a trival problem.
In modern file systems, many symbolic links or hardlinks can lead to the same file.
Usually the file contents and sometimes the timestamp are used by preprocessor to test for duplicates.

In most cases and with virtually every compiler, `#pragma once` will work as intended.
But it is the subject of scepticism because it is neither fully standardized nor perfectly reliable.
An example where `#pragma once` fails can be seen here:
```cpp
// a.hpp
#pragma once
struct Foo {};
```
```cpp
// b.hpp
#pragma once
struct Foo {};
```
```cpp
// main.cpp
namespace a {
#include "a.hpp"
}
namespace b {
#include "b.hpp"
}
int main() {
  b::Foo x;
}
```
We get a compiler error:
```txt
main.cpp:8:3: error: ‘Foo’ is not a member of ´b´
   b::Foo x;
   ^~~
```
The contents of `a.hpp` and `b.hpp` are completely identical (ignoring the comments at the top).
When we attempt to `#include "b.hpp"`, the preprocessor will falsely believe that `b.hpp` has already been included,
because it is identical to `a.hpp`.

Arguably, this is an unusual example and including inside of a `namespace` is considered bad practice by many.
To get the reliablity of include guards and also the alleged performance benefits of `#pragma once`, we could simply
use both in our file:
```cpp
// mymath.hpp
#pragma once
#ifndef MYPROJECT_MYMATH_HPP_GUARD
#define MYPROJECT_MYMATH_HPP_GUARD
inline int sqr(int x) {
    return x * x;
}
#endif
```

### See Also
- https://en.wikipedia.org/wiki/Include_guard
- https://en.wikipedia.org/wiki/Pragma_once
- https://en.cppreference.com/w/cpp/preprocessor/include
