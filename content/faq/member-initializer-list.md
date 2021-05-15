---
title: "What is a 'Member Initalizer List' and why should I use it?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Constructors in C++ are used to initialize the data members of a class.
First, it's important to understand the terminology:
```cpp
struct wrapper {
    int value;
    
    wrapper(int v) : value{v}
    {
    }
};
```
Our class `wrapper` has a constructor, consisting of four parts:
- the class name `wrapper`
- the parameter list `int v` in parentheses
- the member initializer list `value{v}`, separated by a `:` from the parameter list
- the constructor body `{}`, which is empty in this case

In most other languages that have classes and constructors, the initialization simply takes place in the constructor
body.
However, in C++, this is not possible as the constructor body can only be used for reassigning members after they have
already been initialized.

## A Common Mistake: Trying to Initialize in the Constructor Body

Consider the following example:
```cpp
struct point {
    int x = 0;
    int y = 0;
    
    point(int x, int y)
    {
        this->x = x;
        this->y = y;
    }
};
```
Here, when we call the constructor `point(int,int)`, the members `x` and `y` are first initialized to zero using
[copy initialization](https://en.cppreference.com/w/cpp/language/copy_initialization).
Afterwards, they get reassigned to the constructor parameters `x` and `y` respectively.
**This is not initializing the members to the parameters. It is reassigning them.**

In such a trivial example, the difference isn't noticeable.
But if we use `const` members, reference members, array members, or any other non-assignable type, we receive
compiler errors:
```cpp
struct const_value {
    // error: uninitialized const member in 'const int' [-fpermissive]
    // note: 'const int const_value::val' should be initialized
    const int val;

    const_value(int val)
    {
        // error: assignment of read-only member 'const_value::val'
        this->val = val;
    }
};
```
In this case, we should be glad that we have received a compiler error, because there are also cases where our code
compiles but performance gets degraded, unnoticeably.
For example, if we have a `std::string` member, it may be inefficient to first initialize it to an empty string and
then reassign it to another value.

## The Right Way: Using a Member Initializer List

To initialize our members, we should isntaed use a member initializer list and leave the constructor body empty:
```cpp
struct const_point {
    const int x, y;
    
    const_point(int x, int y) : x{x}, y{y} {}
};
```
This code compiles, unlike the previous example.
We are taking the parameter `x` and `y` and initializing the members `x` and `y` to their values, respectively.
Unlike in the previous, incorrect example of using `this->x =` to "initialize", we can just use `x` as a parameter
name without worrying about ambiguity with the members of our class.

Elements of the member initializer list are separated by a comma and follow the same syntax as initialization as
variables.
In this particular example with `x{x}`, `y{y}`, we are using
[list initialization](https://en.cppreference.com/w/cpp/language/list_initialization) to initialize the members.
However, we could also use `x()` to [value-initialize](https://en.cppreference.com/w/cpp/language/value_initialization)
`x` to zero, or `x(5)` to
[direct-initialize](https://en.cppreference.com/w/cpp/language/direct_initialization) `x` to a `5`.
Once again, we have the same syntax of initializing members in the member initializer list as for variables.

The constructor body can usually be left empty if we only initialize our members.

### About Initialization Order

Sometimes the order of initialization is important.
Initialization in C++ alwas takes place in the order of declaration of members inside the class, even if the member
initializer list suggests otherwise. For example:
```cpp
struct stringified_value {
    int value;
    std::string str;
    
    // warning: 'stringified_value::str' will be initialized after
    //          'stringified_value::value'
    //          when initialized here [-Wreorder]
    stringified_value(int v) : str{std::to_string(v)}, value{v} {}
};
```
To avoid compiler warnings, we should always write our member initializer list in the order of declared members:
```cpp
struct stringified_value {
    int value;
    std::string str;
    
    stringified_value(int v) : value{v}, str{std::to_string(v)} {}
};
```

### About Default Member Initializers

Default member initializers are another correct way of initializing members.
We could make our previous example more concise using them:
```cpp
struct stringified_value {
    int value;
    // default member initializer here:
    std::string str = std::to_string(value);
    
    stringified_value(int v) : value{v} {}
};
```
In this example, `value` will first be initialized to `v`, then `str` will initialized to the stringified `value`.
Remember, `str` is declared after `value`, so it's always initialized after.
Default member initializers are always executed for all constructors, unless they are overridden by a member initializer
list.
Say, we were to add a second constructor:
```cpp
stringified_value(int v, std::string s) : value{v}, str{std::move(s)} {}
```
Now `str = std::to_string(value)` will not be executed at all for this constructor, but `str` will be initialized to
`s` instead (using [`std::move`](https://en.cppreference.com/w/cpp/utility/move)).

### So What is The Constructor Body For?

The constructor body is still useful for performing work more complicated than just initialization.
It allows us to do many things, such as:
- checking pre-conditions, like whether provided names were non-empty strings, etc.
- opening files and checking for success
- managing other resources, like network connections, VRAM buffers, etc.
- generally, calling member functions of our members
- logging the creation of our new object
- counting instances of our new object
- modifying global state in some way

As a rule of thumb, if something can be done in the member initializer list, it should.
If there is no reasonable way to do it there, it should be done in the constructor body.

## Summary

The correct way to initialize members in C++ is using default member initializers, or in some cases using default member
initializers.
The constructor body can not be used for initialization and trying to abuse it this way can lead to errors.
Often, we can simply leave the constructor body empty.

Regardless of how we initialize our members and how we order our member initializer list, the members are **always**
initialized in the order of declaration.
We have many ways to initialize members and the syntax for doing so is the same as for variables.

### See Also
- https://en.cppreference.com/w/cpp/language/constructor
- https://en.cppreference.com/w/cpp/language/constructor#Initialization_order
- https://en.cppreference.com/w/cpp/language/data_members#Member_initialization
