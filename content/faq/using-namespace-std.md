---
title: "Why using namespace std is considered bad practice?"
date: 
tags: ["c++"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Consider this code:

```cpp
#include <vector>

//My own implementation of vector
template<typename T>
class vector {
    ...
};

int main() {

    vector<int> vec;
    vector<int> vector;
}
```

You can distinguish your own implementation from the std one?

Consider also this code:

```cpp
#include <iostream>
#include <string>

namespace lib {

    void print(std::string& text) {
        std::cout << text << '\n';
    }
}

namespace lib_2 {

    void print(const char* text) {

        std::string temp = text;

        //Make something with this string

        std::cout << temp << '\n';
    }
}

using namespace lib;
using namespace lib_2;

int main() {

    print("Hello");
}
```

Again, you know what function is called?

In conclusion, ```using namespace``` is considered a bad practice because makes your code less clear, and in the second example leads to function name collision. This is really dangerous, because your code still compiles, but **it silently calls the wrong function**. 


