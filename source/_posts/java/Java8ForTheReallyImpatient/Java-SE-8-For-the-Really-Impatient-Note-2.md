title: 'Java SE 8 For the Really Impatient, Note 2'
date: 2016-03-16 15:31:28
categories:
  - Coding
  - Notes
tags:
  - Java
---

# Chapter 2 The Stream API

Processing collections of values and specifying what you want to have done, leaving the scheduling of operations to the implementation. E.g, compute the average of the values of a certain method. 

Key points:
* Iterators imply a specific traversal strategy and prohibit efficient concurrent execution. 
* You can create streams from collections, arrays, generators, or iterators. 
* Use `filter` to select elements and `map` to transform elements. 
* Other operations for transforming streams include `limit`, `distinct`, and `sorted`. 
* To obtain a result from a stream, use a reduction operator such as `count`, `max`, `min`, `findFirst`, or `findAny`. Some of these methods return an `Optional` value. 
* The `Optional` type is intended as a safe alternative to working with `null` values. To use it safely, take advantage of the `ifPresent` and `orElse` methods. 
* You can collect stream results in collections, arrays, strings or maps. 
* The `groupingBy` and `partitioningBy` methods of the `Collectors` class allow you to split the contents of a stream into groups, and to obtain a result for each group. 
* There are specialized streams for the primitive types, `int`, `long`, and `double`.
* When you work with parallel streams, be sure to avoid side effects, and consider giving up ordering constraints. 
* You need to be familiar with a small number of functional interfaces in order to use the stream library. 

## From Iteration to Stream Operations

Usually iterate over collection's elements and do some work with each of them. 

```Java
long count = words.stream().filter(w -> w.length() > 12).count();
```
The `stream` method yields a stream for the `words` list. The `filter` method returns another stream that contains only the words of length greater than twelve. The `count` method reduces that stream to a result. 

Differences between stream and collections
1. Stream does not store its elements.
2. Stream operations don't mutate source. They return new streams that hold the reuslt.
3. Stream operations are lazy when possible. Not executed until their result is needed. 

Easily parallelized by changing `stream` to `parallelStream`
```Java
long count = words.parallelStream().filter(w -> w.length() > 12).count();
```
**What. not how** principle: we describe what needs to be done, don't specify in which order, or in which thread, this should happen. 

Setup a pipeline of operations in 3 stages:
1. Create a stream.
2. Specify _intermediate operations_ for transforming the initial stream into others
3. Apply a _terminal operation_ to produce a result. 
Afterwards, the stream can no longer be used. 

Stream operations are not executed on the elements in the order in which they are invoked on the streams. Nothing happens until `count` is called. When the `count` method asks for the first element, then the `filter` method starts requesting elements, until it finds one that has length > 12. 

## Stream Creation

Turn any collection in to a stream with the `stream` method that Java8 added to the `Collection` interface

Use the static `Stream.of` method for arrays
```Java
Stream<String> words = Stream.of(contents.split("[\\P{L}+"))
```
The `of` method has a varargs parameters
```Java
Stream<String> song = Stream.of("gently", "down", "the", "stream");
```

Use `Array.stream(array, from, to)` to make stream from a part of an array. 

Use the static `Stream.empty` method to make a stream with no elements
```Java
Stream<String> silence = Stream.empty();
```

2 interfaces for making infinite streams: 
`generate` takes a function with no arguments(or, an object of the `Supplier<T>` interface). Whenever a stream value is needed, that function is called to produce a value
```Java
Stream<String> echos = Stream.generate(() -> "Echo"); // get a stream of constant values

Stream<Double> randoms = Stream.generate(Math::random); // get a stream of random numbers
```

`iterate` takes a seed value and a function(a `UnaryOperator<T>`), and repeatedly applies the function to the previous result. 
```Java
Stream<BigInteger> integers = Stream.iterate(BigInteger.ZERO, n -> n.add(BigInteger.ONE));
```

`Pattern` class has a method `splitAsStream` that splits a `CharSequence` by a regular expression. 
```Java
// split a string into words
Stream<String> words = Pattern.compile("[\\P{L}+").splitAsStream(contents);
```

`File.lines` method returns a `Stream` of all lines in a file. 
```Java
try (Stream<String> lines = Files.lines(path)) {
  // Do something with lines
}
```
The stream and the underlying file with it will be closed when the `try` block exits normally or through an exception.

## The filter.map and flatMap Methods

`filter`: yields a new stream with all elements that match a certain condition
```Java 
// transform a stream of strings into another stream contaning only long words
List<String> wordList = ...;
Stream<String> words = wordList.stream();
Stream<String> longWords = words.filter(w -> w.length() > 12);
```
The argument of `filter` is a `Predicate<T>`, that is, a function from `T` to `boolean`

`map`: transform the values in a stream in some way, a function is applied to each element.
```Java
// transform all words to lowercase
Stream<String> lowercaseWords = words.map(String::toLowercase);

// get first character of each word
Stream<Character> firstChars = words.map(s -> s.charAt(0));
```

Suppose you have 
* a generic type `G`(such as Stream)
* function `f` from some type `T` to `G<U>`
* function `g` from `U` to `G<V>`

Then you can compose them, that is, first `f` and then `g`, by using `flatMap`. 

```Java
// words = [..., "your", "boat", ...];
public static Stream<Character> characterStream(String s) {
  List<Character> new ArrayList<>();
  for (char c : s.toCharArray()) result.add(c);
  return result.stream();
}
// generate [... ['y', 'o', 'u', 'r'], ['b', 'o', 'a', 't'], ...]
Stream<Stream<Character>> result = words.map(w -> characterStream(w));

// generate [... 'y', 'o', 'u', 'r', 'b', 'o', 'a', 't', ...]
Stream<Character> letters = words.flatMap(w -> characterStream(w));
```
In the above case, `G` is Stream, `T` is String, `U` is Character, `V` is also Character. 

## Extracting Substreams and Combining Streams

`limit`: returns a new stream that ends after n elements(or when the original stream ends if it is shorter)
```Java
Stream<Double> randoms = Stream.generate(Math::random).limit(100);
```

`skip`: discards the first n elements
```Java
Stream<String> words = Stream.of(contents.split("[\\P{L}+").skip(1));
```

`concat`: concatenate 2 streams
```Java
Stream<Character> combined = Stream.concat(characterStream("Hello"), characterStream("World"));
```
The first strema should not be infinite. 

`peek`: yields another stream with the same elements as the original, but a function is invoked every time an element is retrieved. 
```Java
Obejct[] powers = Stream.iterate(1.0, p -> p * 2)
  .peek(e -> System.out.println("Fetching " + e))
  .limit(20).toArray();
```
This way you can verify that the infinite stream returned by iterate is processed lazily. 

## Stateful Transformations

`distinct`: yields elements from the original stream, in the same order, except that duplicates are suppressed. The stream must obviously remember the elements that it has already seen. 
```Java
Stream<String> uniqueWords = Stream.of("merrily", "merrily", "gently").distinct();
```

`sorted`: must see the entire stream and sort it before it can give out any elements. You can't sort an infinite stream. One for Comparable elements, the other accepts a Comparator. Useful when the sorting process is a part of a stream pipeline. 
```Java
Stream<String> longestFirst = words.sorted(Comparator.comparing(String::length).reversed())
```

The `Collections.sort` method sorts a collection in place, whereas `Stream.sorted` returns a new sorted stream. 
