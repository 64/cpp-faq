---
title: "What is 'Undefined Behaviour'?"
date: 2021-03-23T02:26:05Z
slug: "ub"
# weight: 1
# aliases: ["/ub"]
tags: ["c++"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


undefined behavior (UB) may confuse lot of newcomers to C++.


# What UB means: 

## undefined behavior:
UB basically means violating certain language rules, like overlow, dealing with stuff out of their lifetime, allocations with explicitly creating objects etc....
And what makes UB hard to spot is that the compiler isn't supposed to warn/error about it, well may warn but for simple and common uses like access out of bounds, retuning a reference to function's local variable. most of these examples lead to [segmentation fault](https://en.wikipedia.org/wiki/Segmentation_fault) of course if you're lucky enough.
## UB in action: 

### unintialized scalar
```cpp
int main() {
    std::size_t x; // x is used without being initialized bad
    std::cout << x;
}
```
### fix
```cpp
int main() {
    std::size_t x{};
    std::cout << x; // x is value initialized, x is now 0
}
``` 

## uninitialized array
```cpp
int main() {
     int array[10];
    for(int x: array)
        std::cout << x << ' ';
}
``` 

### fix
```cpp
int main() {
    int array[10] = {}; // now `array` contains 10 value initialized ints
    for(int x: array)
        std::cout << x << ' ';
}
``` 

## UB in code:
sometimes you may keep the UB in your code for the sake of performance, stdlib sometimes does that for example [std::minmax](https://en.cppreference.com/w/cpp/algorithm/minmax).


## avoiding UB:
some UB needs simple code changes or checks like initialization and in the previously mentioned examples. However sometimes it's really hard to detect UB in your code specially if you aren't experienced enough, luckily there are some for purposes like these, like UBSan.

