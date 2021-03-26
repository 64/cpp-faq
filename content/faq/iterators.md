---
title: "Introduction to C++ 11 Iterators"
date: 2021-03-26T16:37:10Z
slug: "iterators"
tags: ["c++", "c++-11"]
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



# iterators:
C++ iterators are a facility for traversing through a range. They behave same as pointers at the end they're used to point to some specific location of a sequence but they're slightly different as they're customizable and have a more strict interface than a primitive pointer. Ok so how iterators can be useful ?
Consider copying N elements from a vector to another: 

the old way
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
vs
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
but how ? C++11 introduced iterators and range-for (as `for(type identifier ':' range)`), so what the 2nd code does ?
```cpp
for(int i: src)
```
ok so let's explain what's actually is done here, basically the range-for will traverse the range provided by the vector using (begin(), end()) and assign the value pointed by the iterator at that location to `i` by calling `operator*`, ok hold up what are `begin, end, operator*` ? Don't worry everything will be detailed later. So this range-for can be simplified as:
```cpp
for(auto it = src.begin(); it != src.end(); ++it) {
    int i = *it;
    ...
} 
```


### interface of an iterator:
begin: points to the beginning of a sequence.

end: points to the end of a sequence.

*iterator: returns the stored value pointed by the iterator.

iterator++: increments the iterator by 1

iterator--: decrements the iterator. 


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

#### conclusion:
In brief Random Access Iterator can be treated as a Bidirectional Iterator which itself can be treated as a Forward Iterator then input/output iterator.


### iterator vs pointer:
As discussed earlier an iterator is just an abstraction of a pointer, but with more consistency (i.e we can't just use pointers for iterating over a linked list as it's not contiguous and lead to UB, but we instead create a custom iterator and move the current stored pointer to a node to the next node whenever moving in a direction).
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
seems like just a RAI ? 
### writing your first iterator:
let's start by implementing a simple forward iterator:
layout of our iterator:
```cpp
template<class T>
struct my_custom_iterator {
    T* ptr;
    my_custom_iterator(T* p);
    my_custom_iterator& operator++(); // ++my_custom_iterator
    my_cusrom_iterator operator++(int); // my_custom_iterator++
    T& operator*();
    T* operator->();
    bool operator!=(const my_custom_iterator&);
    bool operator==(const my_custom_iterator&);
};
```
implementation:
```cpp
template <class T>
struct my_custom_iterator {
    T* ptr;
    my_custom_iterator(T* p) : ptr(p) {}
    my_custom_iterator& operator++() {
        ++ptr;
        return *this;
    }
    my_custom_iterator operator++(int) {
        auto tmp = *this;
        ++(*this);
        return tmp;
    }
    T& operator*() { return *ptr; }
    T* operator->() { return ptr; }
    bool operator!=(const my_custom_iterator& rhs) { return (ptr != rhs.ptr); }
    bool operator==(const my_custom_iterator& rhs) { return (ptr == rhs.ptr); }
};
```
now our iterator is usable for simple iterations:
```cpp
template<class T,  std::size_t N>
struct proxy_array {
    T arr[N];
     std::size_t size{};
     ...
     my_custom_iterator begin() { 
         return my_custom_iterator<int>{arr}; 
         // can also use &arr[0]
     }
     my_custom_iterator end() {
         return my_custom_iterator<int>{arr + size};
     }
     ...
}
```
testing:
```cpp
int main() {
    proxy_array<int, 5> parray{6, 8, 5, 4, 10};
    for(int i : parray)
        std::cout << i << ' ';
}
```
