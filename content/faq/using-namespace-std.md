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

#include <iostream>
#include <algorithm>

using namespace std;

int swap = 0;

int main() {

	cout << swap << endl; // error: reference to "swap" is ambiguous

	return 0;
}

```

There the compiler throws an error because swap is also a function inside the algorithm header. Compiler doesn't know what to do in this case.

So ```using namespace``` is considered a bad practice because makes your code less clear, and in the example leads to name collision. 
If you really want to avoid writing  ```std::```, you can use a **using-declaration** inside a local scope:

```cpp

#include <iostream>

int main() {

	using std::cout; // This will be valid only inside this function
	
	cout << "Hello" <<'\n';

}

```

Generally, is always better to specify the namespace.
If you want to know something more about this topic, I recommend watching this video: https://www.youtube.com/watch?v=4NYC-VU-svE
