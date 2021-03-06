
I asked a question in the JugBD group that why in C a function that takes an array as an argument, it also takes the size of the array as an additional argument, but we don't see this usually in other languages like Java. 

Here is the link of the question:  https://www.facebook.com/groups/jugbd/permalink/821539027892784/

After digging and studying a little and also from the comments of the answer in the facebook, I've got my answer.  I'm going to explain everything I understand here,  so it might be a big post. 

We all know that a computer memory is just a long sequence of byte.  There aren't really any floating point numbers, integer or strings. They are just plain and simple bytes. A whole lot of bytes.  We use language like `C` to work on those bytes. The langauge imposes some structure or way out to work on those bytes.   

We just abstract things over the bunch of 1's and 0's.  We do creepy stuff, thats okay, lets just admit it for now. 

So basically we have memory in RAM and  processor maps them to a flat address space. 

 Now lets check a simple example - 

```c
	int i = 1234;
```

We've declared an integer which holds a value.  Here I'm telling to the compiler to set some byte to the memory with a name `i` and treat it as an integer. 

So what we can do now? It has an address in the address space. Right ? We want to know where does this variable reside in the long sequence of memory in the computer (_As a human, we have an insatiable curiosity to know everything_) . Well, the address is just a number which points the first byte in  memory holding the value of this variable. 

Lets print the address (_We are adventurous too_) . 

```c
    printf("%p\n", &i);
```

> Output: 0x7fffac4b5cc0

Although its platform specific. 

However, we don't really want to print the address always, rather we want a pointer of that address (_We love to keep strings, I'm never letting you go ;_)). 

```c
     int * p = &i;
```

Here `p` is a variable like others. It has a name and a value. The value happens to be the address of the other variable. Thats the only difference here.  

```c
	int j = *p;
```

Now we can do the opposite. We can get the value from the pointer. Here we are dereferencing the value of the pointer which is pointed to the address of  `i`. That means, now 	`j`  is a brand new variable which holds the value of `i` which is  `1234`.

```c
	*p = 2458;
```
But hey, `*p`  hasn't changed yet, we can put another value to it. 
As `p` is pointed to the value of `i`, if we want to change the value of `i`, we can use its pointer. 

Thats what I have done here. Now the value of `i` is  `2458`.

Lets print the value of I and j and see what they are- 

```c
	printf("i = %d, j = %d\n", i, j);
```

> Output: i = 2458,  j = 1234 


-----------------

Now  that we know what is a pointer .  Lets move forward. 

Pointer gets more interesting in arrays. 

Lets say, 

```c
	int arr[5];
```

So we have declared an array of integer which can hold 5 integer. 

But we haven't initialized the array. It now holds no meaningful values. 

Lets initialized it . 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };
```

We can access the elements of the array using the zero based index in to array.

```c
	printf("%d\n", arr[0]);
```

Thats the first element of the array.  How about printing them all- Lets do it using a for loop (_Let me iterate over the array, I know that I'm creepy_). 

```c
	for (int i = 0; i < 5; ++i) {
		printf("%d\n", arr[i]);
	}
```

Now we can have an array with a thousands elements. This for loop can work for that just fine.(_Well, Im not that creepy, so skipping the iteration over thousands elements for now_)

But hey, there is still a problem (_We create problems and then sell the solutions, how creepy we are!!_).

We need to manually change the for loop to indicate how many elements in the array. 

The problem with array is, it does not carry  runtime information at all. That means there is no way to tell how many elements in the array in the runtime.  

The only way to do that is, capture the size of array in the compiled time. 

Thats easy.  

There is a `sizeof` operator in `C`. We can use that to get the size of an array. 

```c
	int size = sizeof(arr);
```

But the problem with the `sizeof` is, it gives me the total byte of the array, but I'm rather interested to get the total elements of the array not the total byte it takes. 

We know that compiler knows about the size of an integer. If integer takes 4 byte, it should be 20 bytes. 

So I can calculate the size of array with simple math ( _ah, I'm extreamly good at math_)- 

```c
	int size = sizeof(arr)/ sizeof(int);
```

or 

```c
	int size = sizeof(arr)/ sizeof(arr[0]);
```

This is now even better. Because if we change the type of the array, my expression to determine the size of array will be always correct. 

So now my loop looks like this- 

```c
	for (int i = 0; i < size; ++i) {
			printf("%d", arr[i]);
	}
```

Well, enough of array , lets move on and find out how array is related to the pointer (_ah, I love my life, I know now how pointer looks like-- it looks like asterisk (*) totally! trust me!)_ . 

Here is an interesting fact, that is in `C` an array variable is also a pointer.

For example - 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };

	int * p = arr;

	for (int i = 0; i < size; ++i) {
		printf("%d\n", p[i]);
	}
```

It works just fine. 

Now we can dereference the pointer. Let's see how we can do that. 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };

	int * p = arr;
	int a = * p;
	int b = * arr;
```

Now if we print the `a` and `b`, we will get the first value of the array. 

Interestingly we can increment the pointer also. 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };


	int * p = arr;
	int a = *(p + 1);
	int b = *(arr + 2);

	printf("%d, %d \n", a, b);
```

Here, `*(arr + 1)` is now 4 bytes after arr and `*(arr + 2)` is 8 bytes afterwards and so `a` we have 2nd value and in` b` we have 3rd value. 

So we can do a little arithmetic here. 

Normally we get the value of an array using index- like 

```c
arr[i].
```

Basically `arr[i]` is defined using pointer arithmetic.

Think this way-

```c
    arr[i] = *(arr + i) 
```

or,

```c
    p[i] =  *(p + i) 
```

 Fact is..  when it increments, it increments to the next element, not the next byte

`p` has no idea about the array but it knows that it points to an integer. 
So compilers knows how to update the value. 

> In C, a pointer is not only an address, but also it  tells you what the data type at the address is.

So we can come up with a formula now : - 

If we want to compute address of `arr + i` where `arr` has type `T *` then the formula for the address is:

    addr( arr + i ) = addr( arr ) + [ sizeof( T ) * i ]

Here `T`  is a pointer. 


Now that we know how array works like pointer, lets re-write the for loop-  ( _We'll be safe enough once we make the jump to hyperspace_) 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };

	int size = sizeof(arr) / sizeof(arr[0]);

	int * p = arr;
	int * end = p + size;

	for (; p != end; ++p) {
		printf("%d\n", *p);
	}
```

Here `*end` is a pointer of a address pointing to the end of the array and we can loop through the array until it gets to the end of the array and every time we just increment the pointer and dereference it to get the value. 

-----------------

Here things can go terribly wrong if we just mess up with the math ( _Don't do this if you are terribly bad at elementary math_). Its extremely powerful and dangerous as well. 

We have  covered array and pointer arithmetic. But we need to know something that is little tricky (_Life is messy, Ren. It's not easy and it's definitely not for the timid_).

Using `sizeof` operator, we can determine the size of an array. But we can do it only when it is applied to a variable. 

For example - 

```c
    int arr[5] = { 1, 2, 3, 4, 5 };
    int * p = arr;

	printf("sizeof(arr) = %d, sizeof(p) = %d\n", (int) (sizeof(arr)), (int) (sizeof(p)));
```

> Output:  sizeof(arr) = 20, sizeof(p) = 8

So we can see that, when `sizeof` `arr` is 20  and the  `sizeof` `p` is only 8. 

Here `8` is not the number of byte it requires to allocate an integer, rather, number of byte requires for one address.  As I'm using 64 bit machine , thats why it is 8.

The pointer `p` can do as much the `arr `variable can do, but it does not carrry the compiled time information. It has no idea how long the array is. 

So when we pass an array to a function, we have only choice to pass it as a pointer. 
Lets say we have a function - 

```c
	void foo(int * array){    }
```

We can pass the actual array or the pointer, it doesn't matter what we do, it is always passed as a pointer. 

```c
	int arr[5] = { 1, 2, 3, 4, 5 };
	int * p = arr;

	foo(arr);
	foo(p);
```

So here we have got our problem-

In this foo function , there is no way to know about the size of the given pointer. 
 
Here is an option, we can pass the size of the array as an additional argument . 

```c
    void foo(int * array, int size){
    }
```

Now the caller is compelled to provide the size.

Thats why we in `C`, when we see a function that takes an array or a pointer, it also takes a size as an additional argument. 


Ah finally I'm incredibly excited to complete the tutorial! 


## TL;DR

In `C` array works like a pointer, so array is always passed to a method as a pointer.
 
The only difference from a proper pointer is that you can not re-assign it. So in runtime, there is no way to know about the size of the given pointer. 

Thats why in `C`, when we see a function that takes an array or a pointer, it also takes a size as an additional argument and  the caller is compelled to provide the size.


