title: Memory Usage Estimation in Java
date: 2016-10-07 21:06:23
categories:
  - Coding
tags:
  - System Design
  - Notes
---
  
I mentioned in the previous post that memory usage estimation is also very important when it comes to evaluate a design. And I was impressed deeply by my two former tech leads when they do a quick estimate of my proposal on a small piece of paper, or even with just a mental calculation. Now it is the time for me to grasp the gist behind.  
  
## Premises  
  
The actual memory calculation can be very complicated in terms of implementation, synchronization, and etc. Take Java as an example. Java is implemented on a very wide range of computational devices, and memory consumption is implementation-dependent. So there is no guarantee that the memory usage is same for all of those devices. And If an object's synchronization lock is contented, or the object is under garbage collection, the usage would not be the same.  
  
So what we are going to discuss are based on these following premises:   
  
1. Memory usage in general conditions  
2. For Java only  
3. Heap memory, not stack memory  
4. 64-bit machine, which means that 8 bytes are needed to represent addresses  
  
Many older machines use a 32-bit architecture. I also read some older posts based on it. In this situation, the only difference is that each machine address is just 4 bytes.   
  
## Baiscs  
  
Here comes the very basic idea. Almost the same as time cost estimation: 
  
1. Know typical memory usage in Java  
2. Decompose a design into these basic usages  
  
This post mainly focuses on the typical memory usage in Java and will not cover any decomposition, because once we have the design, it would be fairly simple for us to come up with the count of each usage.  
  
### Primitives
  
First and foremost, let us take a look at the memory requirements for primitive types.   
  
Table 1.1 Typical Memory Requirements for Java Primitive Types  
  
|type|bytes|
|:---|:---:|
|boolean|1|
|byte|1|
|char|2|
|short|2|
|int|4|
|float|4|
|long|8|
|double|8|
  
A problem one may have is that why boolean consumes 1 byte, since the information can be expressed well with a single bit. This is because computers access memory one byte at a time. It would be awkward if we had to cope with sub-byte offsets for certain fields, and it would require extra logic to read/write individual bits at a given position rather than just the whole byte each time the boolean was accessed.  
  
Why is `int` 4 bytes? Since the Java `int` data type is the set of integer values between -2,147,483,648 and 2,147,483,647(2^31), a grand total of 2^32 different values, typical Java implementations use 32 bits to represent int values.  
  
If we know the amount of memory available, we can calculate limitations from these values. Just a simple division. For example, if we have 1GB of memory on our computer (1G = 10^9 or 2^30), we cannot put more than about `2^30 / 4 = 2^28 = 2^8 * 2^20 = 256 million` int values or 128 million double values in memory at any time.   
  
### Objects  
  
In general, the heap memory used by a Java object consists of:  
  
* an object header, consisting of a few bytes of "housekeeping" information  
* memory for primitive fields  
* memory for reference fields  
* padding  
  
To determine the memory usage of an object, we add the amount of memory used by those cases above. Let us figure out each part of them.  
  
The first one is the header. What is the "housekeeping" information? It includes a reference to the object’s class, garbage collection information, and synchronization information. Typically 16 bytes.   
  
We already mentioned the memory consumption for each primitive fields. The total memory is a sum of each primitive field. E.g, if an object contains only two primitive fields, which are `int`, they will cost 8 bytes.  
  
Reference fields are actually pointers to other objects. This "pointer" is just a memory address, thus uses 8 bytes of memory.  
  
What is padding then? The memory usage is padded to be a multiple of 8 bytes for a 64-bit machine. We can take it as a few "wasted" unused bytes after the object data, to make every object start at an address that is a convenient multiple of bytes and reduce the number of bits required to represent a pointer to an object.  
  
#### Examples  
  
How many bytes does an Integer object use? We know that Integer is a wrapper of int and that is its only primitive field, 4 bytes. Then as an object there will be a header of 16 bytes. So 20 bytes in total? No. Do not forget about the padding. We still need a few bytes to reach the closest multiple of 8, which is 24. So 4 bytes of padding should be added and the result is:  
  
`16 bytes of overhead + 4 bytes int + 4 bytes padding = 24 bytes`  
  
How about a Date object then? Similarly, the result consists of an overhead, the three int fields (day, month, year), and a padding.   
  
`16 bytes of overhead + 3 * 4 bytes int + 4 bytes padding = 32 bytes`  
  
How about the `Counter` class below which also has a reference to a String?  
  
```java
class Counter {
    String s;
    int c;
}
```
  
According to our general rule, the result now not only contains an overhead and an int field, but a reference to another object as well. That is: 

`16 bytes of overhead + 4 bytes int + 8 bytes of reference + 4 bytes padding = 32 bytes`  
  
When we account for the memory for a reference, we account separately for the memory for the object itself, so this total does not count the memory for the String value.  
  
### Linked lists  
  
A speical case is when there is an inner class in the object. A nested non-static (inner) class requires an extra 8 bytes of overhead for a reference to the enclosing instance.   
  
For example, suppose we have a stack with N integers built with a linked-list representation like below:   
  
```java
public class Stack<Integer> implements Iterable<Integer> {
    private Node first;   // top of stack (most recently added node)
    private int N;        // number of items
    private class Node {  // nested class to define nodes
        Integer i;
        Node next;
    }
    ...
}
```
  
How many bytes does it use? In addition to the usual 16 bytes for object overhead for Stack, it also has 8 bytes for its reference to Node, 4 for the int N, 4 for padding. That is 32 bytes. And for each entry, it contains a Node and an Integer. A Node class takes 40 bytes, including 16 bytes overhead, 8 bytes to Integer i, 8 bytes to the next Node, and an extra 8 bytes overhead to the enclosing instance (Integer i). Plus an Integer takes 24 bytes. So it costs 64 bytes for each entry. The grand total is 32 + 64N bytes.   
  
### Arrays  
  
Now we already know how to estimate the memory usage of a single object, what if we put these object together in an array or a collection?  
  
First we need to know the how arrays are implemented in Java. Arrays in Java are implemented as objects, with some extra overhead for the length. So an array itself takes:  
  
`16 bytes of overhead + 4 bytes length + 4 bytes padding = 24 bytes`  
  
Besides, the memory needed to store the values in the array should be included. It is different in terms of the types of the value.  
  
#### Array of Primitives  
  
For primitive types all we need to do is multiplying the length of the array by the bytes used by that type. For example, an array of N int values uses 24 + 4N bytes (then rounded up to be a multiple of 8), and an array of N double values uses 24 + 8N bytes (no padding needed, already a multiple of 8).  
  
#### Array of Objects  
  
An array of objects is an array of references to the objects, so we also need to add the space for the references to the space required for the objects. For instance, an array of N Date objects uses 24 bytes (array overhead) plus 8N bytes (references) plus 32 bytes for each object, for a grand total of 24 + 40N bytes.  
  
#### Multi-dimensional Array  
  
Multi-dimensional array is a little more complicated. When we estimate, we should decompose the usage into two parts: 1) the outer array 2) the inner array or the values.  
  
Let us start with a two-dimensional array first. What is the implementation? A two-dimensional array is an array of arrays and each array is an object.  
  
Suppose we have an M-by-N array of double values. The outer array uses 24 bytes overhead plus 8M bytes references to the each array object. Then for the M inner arrays, each of them has 24 bytes overhead plus N times 8 bytes for the N double values. The grand total is: 
  
`24 bytes overhead + 8M bytes references + M * (24 bytes overhead + 8N bytes double) = 8MN + 32M + 24 ~ 8MN bytes`  
  
What if the array entries are objects? First, we replace the cost of double values with the cost of references to the objects, which happens to be the same and leads to a total of 8MN + 32M + 24 ~ 8MN bytes for the array of arrays filled with references to objects. Then we add up the memory for the objects themselves.  
  
For arrays of more than two dimensions, the above logic repeats: each row of the "outer" array is now an array of references to a further array, which contains the actual primitive data (or references if it is an object array).  
  
### Strings  
  
We put String after array as we know the implementation of String contains an array of `char` primitives. The standard String representation (used in Java 7 and after) has two instance variables: a reference to a character array value[] that stores the sequence of characters and an int value hash that stores a hash code that saves recomputation in certain circumstances.   
  
How many bytes does a String of length N use then? For the String object itself, it consists of an overhead, a reference to the array, an int field, and a padding. In total:   
  
`16 bytes overhead + 8 bytes reference + 4 bytes int + 4 bytes padding = 32 bytes`  
  
Besides that, we need to include the memory used by the array, which is an array of N characters. It contains a 24 bytes overhead and N * 2 bytes char primitives. So the grand total is:   
  
`32 bytes + (24 + 2N) bytes = 56 + 2N bytes`  
  
The implementation was different in Java 6 and earlier versions. An alternate String representation maintains two extra int instance variables, offset and count, and represents the sequence of characters value[offset] through value[offset + count - 1]. Now, a String of length N typically uses 40 bytes for the String object plus 24 + 2N bytes (for the character array) for a total of 64 + 2N bytes. This representation saves memory when extracting substrings because two String objects can share the same underlying character array.   
  
## Summary  
  
In this post, we introduced the step-by-step method to estimate memory usage in Java. The first thing is to know the some basic numbers for simple situations like primitive types and single object. Then we dive deeper into more complex cases, such as inner classes and arrays. Building on that, we turn to a widely used special object, String, to estimate its usage.  
  
Still, we can do this fairly quick on the back of an envelope or on a piece of small post-it paper. And memory usage estimation is actually easier than time cost estimation, primarily because not as many program statements are involved (just declarations) and because the analysis reduces complex objects to the primitive types, whose memory usage is well-defined and simple to understand: we can count up the number of variables and weight them by the number of bytes according to their type.  
  
Of course, for most of the times, we care more about time cost and ignore memory usage. But, again, for a performance critical system, we need to make sure both time and memory consumption are acceptable for now and the future. Memory is cheap nowadays, but it is not free. In fact, none of the resources are free. Hope that we can grasp the estimation methods of these resources and make the best use of them.  
  
## References  
  
1. Chapter 1.4 Analysis of Algorithms, Algorithms, 4th Edition  
2. [Memory usage of Java objects: general guide, javamex.com](http://www.javamex.com/tutorials/memory/object_memory_usage.shtml)  
3. [Memory usage of Java Strings and string-related objects, javamex.com](http://www.javamex.com/tutorials/memory/string_memory_usage.shtml)  
4. [How to calculate the memory usage of a Java array, javamex.com](http://www.javamex.com/tutorials/memory/array_memory_usage.shtml)  
  