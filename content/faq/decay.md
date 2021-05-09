---
title: "What is decay and array-to-pointer conversion?"
tags: [""]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Decay is a set of conversion rules applied to function parameters which are passed by value.
The [`std::decay`](https://en.cppreference.com/w/cpp/types/decay) type trait lists all these conversions and can be
used to simulate them.
It is called "decay" because in all cases, some type information is lost, such as the size of an array.

## Array-to-Pointer Conversion

A commonly known form of decay is array-to-pointer conversion:
```cpp
// We seemingly accept an array by value as our parameter:
int buggy_sum(int ints[8]) {
    int sum = 0;
    // warning: 'sizeof' on array function parameter 'ints'
    //          will return size of 'int *' [-Wsizeof-array-argument]
    int limit = sizeof(ints) / sizeof(ints[0]);
    // This will most likely perform only 2 iterations, not 8, like we wanted.
    for (int i = 0; i < limit; ++i) {
        sum += ints[i];
    }
    return sum;
}
```
We have specified that our first `buggy_sum` should accept an array with size `8` of `int` in our first function
parameter.
However, arrays can not be passed by value and instead decay to pointers.
So from the compiler's point of view, our function signature is equal to:
```cpp
int buggy_sum(int *ints);
```
Using `sizeof` with arrays that have decayed to a pointer is a common source of bugs, as in the above example, where
we take obtain the size of a pointer, which is less than the size of the entire array.
Thankfully, GCC and clang warn us about this.

## Function-to-Pointer conversion

Another very similar form of decay is function-to-pointer conversion:
```cpp
void call(void callback(int), int x) {
    callback(x);
}
```
Here, our first function parameter appears to be another function `callback`, which takes an `int` and returns nothing.
However, just as an array, the function `callback` decays to a pointer.
It would be equivalent to write:
```cpp
void call(void (*callback)(int), int x) {
    callback(x);
}
```
The danger of misuse is not as great here; decay simply gives us a nicer syntax for accepting function pointers.

## Discarding `const`/`volatile` qualifiers and removing references

This form of decay is something that you have likely understood already by intuition.
When accept a type `T` as a parameter to our function by value, we don't care whether the arguments were `const`,
`volatile`, references, other values, etc. 
Inside of the function, we are simply using `T`.

This form of decay can be seen in the following examples:
```cpp
void use_int(int my_int) {
    /* ... */
}

int main() {
    // Calling use_int with a literal of type int:
    use_int(3);
    
    // Calling use_int with a const volatile int: This copies the value of x.
    // const and volatile are discarded, so inside of use_int,
    // we don't care that the argument was const or volatile.
    const volatile int x = 7;
    use_int(x);
    
    // Calling use_int with an int& copies the referenced value:
    // Inside of use_int, we don't see or care that the argument was a reference.
    int y = 5;
    int &yref = y;
    use_int(y);
}
```

## Using `std::decay`

[`std::decay`](https://en.cppreference.com/w/cpp/types/decay) and its convenience type alias `std::decay_t` simulate the
effect of decay.
Here are a few examples:
```cpp
// no decay
std::decay_t<int>           -> int  
// discarding references
std::decay_t<int&>          -> int  
// discarding references and const qualifier
std::decay_t<const int&>    -> int
// discarding volatile
std::decay_t<volatile int>  -> int
// array-to-pointer
std::decay_t<int[8]>        -> int*
// array-to-pointer, and also discarding the reference
std::decay_t<int(&)[8]>     -> int*
// function-to-pointer
std::decay_t<int(int)>      -> int(*)(int)
```
