---
title: "Introduction to C++ Iterators"
date: 2021-03-26T16:37:10Z
slug: "iterators"
tags: ["c++", "c++-11"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---



# iterators:
C++ iterators are a facility for traversing through a range just like in any programming language. They behave same as pointers as they're used to point to a specific location of a sequence but they're slightly different as iterators are customizable and have a more strict interface than a primitive pointer. In this article I'll give you a brief and clear explanation about iterators and how they work.

Ok so how iterators can be useful ?

Consider these examples

1. Copying N elements from a vector to another:
the old way:
```cpp
#include <iostream>
#include <vector> 

int main() {
    std::vector<int> src{5, 8, 9, 3, 1};
    std::vector<int> dest{};
    for(std::size_t i = 0; i < src.size(); i++) {
        dest.push_back(src[i]);
    }
}
```
vs range-for:
```cpp
#include <iostream>
#include <vector> 

int main() {
    std::vector<int> src{5, 8, 9, 3, 1};
    std::vector<int> dest{};
    for(int i: src) {
        dest.push_back(i);
    }
    // or even
    std::copy(src.begin(), src.end(), std::back_inserter{dest});
}
```
as you can see the 2nd way is shorter to write and easier to understand.
```cpp
for(int i: src)
```
basically this range-for will traverse through the range provided by the vector's iterator using begin(), end() and assign the value pointed by the iterator at that location to `i` by calling `operator*`, so this range-for can be simplified as:
```cpp
for(auto it = src.begin(); it != src.end(); ++it) {
    int i = *it;
    ...
} 
```
see [range-for](https://en.cppreference.com/w/cpp/language/range-for) for in-depth explanation.

2. Creating a linked list:
linked list isn't a contiguous sequence, so it's impossible to relay on pointers for iteration instead you should provide an iterator that performs the same arithmetic operations as a pointer would do.
```cpp
class linked_list_iterator {
    T* ptr;
    ...
    operator++() {
       // instead of incrementing `ptr` you simply do
       ptr = ptr->next;
    }
    ...
};
```

### interface of an iterator (i.e Bidirectional Iterator):

*iterator: returns the stored value pointed by the iterator.

iterator++, ++iterator: increments the pointer.

iterator--, --iterator: decrements the pointers. 

iterwtor !=/== iterator: compare the pointers held by both operands. Useful for indicating where a range starts and ends.

#### note:
An iterator itself mustn't have begin/end functions as it's container's responsibility to handle these functions.


### types of an iterator:

there are 5 iterators in C++ defined by the standard as:
- [input iterator](https://en.cppreference.com/w/cpp/named_req/InputIterator):
input iterator is a single direction iterator and read only means you can only increment it and read from it. It's one of the simplest iterators. Usually used for reading from input streams like files in read only mode, stdin.

- [output iterator](https://en.cppreference.com/w/cpp/named_req/OutputIterator):
same as input iterator, output iterator is a single direction iterator, but can only used for writing to a stream. 

- [forward iterator](https://en.cppreference.com/w/cpp/named_req/ForwardIterator):
forward iterator is a single direction iterator that can be used for both reading and writing, usually used in containers like [std::forward_list](https://en.cppreference.com/w/cpp/container/forward_list). 

- [bidirectional iterator](https://en.cppreference.com/w/cpp/named_req/BidirectionalIterator):
bidirectional iterator is just a forward iterator that can be moved to both direction incrementing, decrementing 

- [random access iterator](https://en.cppreference.com/w/cpp/named_req/RandomAccessIterator):
random access iterator is a bidirectional iterator that can be moved to any direction means if you want to access to the Nth element of a sequence you can just do `auto Nth_Value = container.begin() + N;` unlike previous iterators where you should increment N times. Random access iterator is the hardest and yet most powerful iterator since it supports all of the previously mentioned iterators. 
- [contiguous iterator (since C++20)](https://en.cppreference.com/w/cpp/named_req/ContiguousIterator):
contiguous iterator is a random access iterator where the elements it points to are contiguously ordered in the memory

#### conclusion:
In brief Contiguous Iterator is a superset of a Random Access Iterator which is a superset of a Bidirectional Iterator a Forward Iterator and a input/output iterator.


### iterator vs pointer:
As discussed earlier an iterator is just an abstraction of a pointer, but with more consistency (i.e we can't just use pointers for iterating over a linked list as it's not contiguous and lead to UB, but we instead create a custom iterator and move the current stored pointer to some node to the next node if the location has changed).
pointer:
```cpp
int main() {
    int arr[] = {6, 8, 5, 6};
    auto first = arr; // first points to the first element of arr: 6
    first++; // can be incremented
    ++first;
    first--; // decremented
    --first;
    use(*first); // deferenceced
    use(first[N]); // same as use(*(first + N))
    first += N; // re-assigned 
    first -= N;
    
}
```
RAI (Random Access Iterator):
```cpp
int main() {
    std::vector<int> vec{6, 8, 5, 6};
    auto first = vec.begin();
    first++;
    ++first;
    first--;
    --first;
    use(*first);
    use(first[N]);
    first += N;
    first -= N;
}
```

