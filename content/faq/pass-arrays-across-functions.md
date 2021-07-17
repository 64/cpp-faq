---
title: "Pass Arrays Across Functions"
tags: [""]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---
Passing arrays to a function is a bit different than to passing non-array values to a function in a function call. This involves a concept called decay or array-to-pointer convesion, you should read about [decay and array-to-pointer conversion](https://64.github.io/cpp-faq/decay/). After reading this you'd be wondering if my arrays decay to a pointer to the first element, then how do I pass the array? Before answeing that you should learn about a few cases where an array will decay into a pointer or is a pointer in the following conditions:

- An array name in an expression is a pointer
- function parameters
- using the subscript `[]` operator

### Declaration of an external array

```c
extern char arr[10];
```
The above example declares arr as an array of 10 characters which is defined somewhere else. It may not be used as a pointer. The same is true for definition (a special case of declaration where memory is allocated for the declaration and a value is stored).

### Array name in an expression

An array name in an expression with a subscript operator is treated as a pointer to first element of array plus offset.
```c
myarr[i];
```
The above example can be rewritten as the following
```c
*(p+i);
```

### Function Parameters

Let us take some example declarations of functions

```c
int myfunc (int *arr){/* some body*/}

int myfunc2 (int arr[]) {/* some body*/}

int myfunc3 (int arr[50]) {/* some body*/}
```

In all the above declarations, when a call to any of the functions is made with an array as argument, only a pointer to its first arguement is passed. This may also have been clear to you after reading the decay faq page but it was worth mentioning here.

The standard (C11) enforces all compilers to treat "array of type" to be adjusted to "qualified pointer to type".

[Standard Citation](http://port70.net/~nsz/c/c11/n1570.html/full#6.7.6.3p7)

### The subscript operator

As stated earlier the subscript operator is translatedd to as "pointer to first element of array plus the offset".

The alternatives for this are demonstrated below where we will be setting each memeber of array to zero.

```c
#include <stdio.h>
#define maxlen 10

/* The normal method */

int main() {
	char arr[maxlen];
	
	/* Set all elements to zero */
	for(int i=0; i<maxlen; i++){
		arr[i] = 0;
	}
	return 0;
}
```

```c
/* Alternative method */
/* Set all elements to zero */
	char *p = arr;
	for(int i=0; i<maxlen; i++){
		*(p + i) = 0;
	}

```

```c
/* Another alternative */
/* Set all elements to zero */
	char *p = arr;
	for(int i=0; i<maxlen; i++){
		*arr++ = 0;
	}
```
Any of the above stated way is fine and works the same in terms of efficieny/performance, the notational difference is quite obvious.

Now We will be discussing a few methods we can pass arrays to functions along with their benefits and pitfalls.

## static array

```c
void modifier(char *myarr){
	/* Do something with myarr*/
}

void makearray(){
	static char arr[5] = "abcd";
	// Do stuff
	modifier(arr);
}
```
The static keyword makes it so that arr[5] is stored on stack and its value is retained accross function calls.
Note that Its value can be overwritten as it's present in the stack. You don't want your array to be overwritten so this method is quite unfeasible, for a single or a couple of calls it should be fine but if anything more than that, you should consider dynamically allocating the array or using a global array if possible.

## Dynamically Allocated Array

In this the memory for array is manually allocated on the heap using a call to malloc and it needs to manually freed. This is one of the safest methods as the values won't get overwritten and you can dynamicaly grow your array to whatever size you want using the `realloc()` routine. The pitfall for this method is that the memory needs to be _manually_ managed and you need to free it after you're done with the use of that memory. If you don't you'll be leaking memory all over the place.

Another thing to mention is that if you make frequent calls to `malloc()`-`realloc()`-`free()` to allocate/reallocate/free your array, your program will slow down as memory needs to fetched in certain block sizes and grown and the freed. You can read more about this in the Read More section present at the end.

```c
#define maxlen 10

void copy_arr_print(char *myarr){
	char carrp[maxlen] = {0};
	strcpy(carrp,myarr);
	puts(carrp);
}

void makearray(){
	char *arr = malloc(maxlen); /* Manually allocate 10 Bytes of memory*/
	arr = "abcd"; /* Use that memory to store something*/
	copy_arr_print(arr);	/* Do something else with the memory */
	free(arr);	/* Free it after you're done */
}
```

## Read More
There are other methods that can be used such as global arrays, declaring structs.

Declaring a user defined type using struct keyword which stores some array and then declaring a variable for it 
and then calling a function with the variable, makes a copy of the entire struct including the array and its values.

This method isn't very feasible as it:
- uses more memory
- copying of a lot of variables
- generally slower

[comp.lang faq on arrays](http://c-faq.com/aryptr/index.html)
[Why Global variables aren't always feasible](https://www.tutorialspoint.com/why-are-global-variables-bad-in-c-cplusplus)
[Price of dynamic memory allocation](https://johnysswlab.com/the-price-of-dynamic-memory-allocation/)