---
title: "Why is `using namespace std` considered bad practice?"
tags: ["beginner"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

Consider this code:

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

int swap = 0;

int main() {
	cout << swap << endl; // ERROR: reference to "swap" is ambiguous
}
```

Here, the compiler throws an error because it doesn't know whether you refer to your `swap` global variable, or the `std::swap` function inside the `<algorithm>` header.

That is to say, **`using namespace` is considered bad practice because it leads to name collisions**, and it also makes your code less clear.
If you *absolutely must*, you can use a **using-declaration** inside a local scope:

```cpp
#include <iostream>

int main() {
	using std::cout; // This only affects the current function

	cout << "Hello" <<'\n';
}
```

Generally speaking, it is better to specify the namespace.

### See also
* https://isocpp.org/wiki/faq/coding-standards#using-namespace-std
