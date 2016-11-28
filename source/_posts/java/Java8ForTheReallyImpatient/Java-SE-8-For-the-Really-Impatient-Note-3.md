title: 'Java SE 8 For the Really Impatient, Note 3'
date: 2016-03-17 17:05:32
categories:
  - Coding
tags:
  - Java
  - Notes
---

# Chapter 2 The Stream API

## Simple Reductions

reductions: reduces the stream to a value. 
terminal operation: the stream ceases to be usable. 

List of reductions: 
* count
* max
* min
* findFirst
* findAny
* anyMatch
* allMatch
* noneMatch

`count`: returns the number of elements of the stream.

`max`: returns the largest value.
`min`: returns the smallest value.
These methods return an `Optional<T>` value that either wraps the answer or indicates that there is none(empty stream). 
```Java
// get the maximum of a stream
Optional<String> largest = words.max(String::compareToIgnoreCase);
if (largest.isPresent()) 
  System.out.println("largest: " + largest.get());
```

`findFirst`: returns the first value in a nonempty collection. Useful when combined with `filter`. 
```Java
// find the first word that starts with letter Q
Optional<String> startsWithQ = words.filter(s -> s.startsWith("Q")).findFirst();
```

`findAny`: returns any match. Effective when you parallelize the stream since the first match in any of the examined segments will complete the computation. 
```Java
Optional<String> startsWithQ = words.parallel().filter(s -> s.startsWith("Q").findAny());
```

`anyMatch`: just want to know there is a match. Takes a predicate argument. 
```Java
boolean aWordStartsWithQ = words.parallel().anyMatch(s -> s.startsWith("Q"));
```

`allMatch`: return true if all elements match a predicate.
`noneMatch`: return true if no elements match a predicate.
These methods always examine the entire stream, but they still benefit from being run in parallel. 

## The Optional Type

Either a wrapper for an object of type `T` or for no object. 
Intended as a safer alternative than a reference of type `T` that returns to an obejct or `null`. 

### Working with Optional Values

`get`: gets the wrapped element if it exists, or throw a `NoSuchElementException` if it doesn't
`isPresent`: reports whether an `Optional<T>` object has a value
```Java
Optional<T> optionalValue = ...;
if (optionalValue.isPresent())
  optionalValue.get().someMethod();
```
No easier than `if (value != null) value.someMethod();`

The key to using `Optional` effectively is to _use a method that either consumes the correct value or produces an alternative_. 
`ifPresent`: accepts a function. If the optional value exists, it is passed to that function. Otherwise, nothing happens. 
```Java
optionalValue.ifPresent(v -> Process v);
// add value to a set
optionalValue.ifPresent(v -> results.add(v));
optionalValue.ifPresent(results::add);
```

No value is returned in `ifPresent`. Instead, `map` returns the result. 
```Java
Optional<Boolean> added = optionalValue.map(results::add);
```
`added` has one of 3 values: true or false wrapped into an `Optional`, if `optionalValue` was present, or an empty optional otherwise. 

`orElse`: sets a default when there is no match
```Java
String result = optionalString.orElse("");
```
```Java
// invoke code to compute default
String result = optionalString.orElseGet(() -> System.getProperty("user.dir"));
```
```Java
// throw an exception if there is no value
String result = optionalString.orElseThrow(NoSuchElementException::new);
```

### Creating Optional Values

`Optional.of(result)` and `Optional.empty()` are static methods to create Optionals. 
```Java
public static Optional<Double> inverse(Double x) {
  return x == 0 ? Optional.empty() : Optional.of(1 / x);
}
```

`ofNullable`: a bridge from the use of null values to optional values. Returns `Optional.of(obj)` if obj is not null, otherwise, `Optional.empty()`

### Composing Optional Value Functions with `flatMap`

Suppose you have a method `f` yielding an `Optional<T>`, and the target type `T` has a method `g` yielding an `Optional<U>`.
If they were normal methods, you could compose them by call s.f().g(). This doesn't work for Optionals because `s.f()` has type `Optional<T>`, not `T`. 
```Java
Optional<U> result = s.f().flatMap(T::g);
```
If `s.f()` is present, then `g` is applied to it. Otherwise, an empty `Optional<U>` is returned. 

Chaining `flatMap`
```Java
Optional<Double> result = Optional.of(-4.0).flatMap(Test::inverse).flatMap(Test::squareRoot);
```
If either the `inverse` method or the `squareRoot` returns `Optional.empty()`, the result is empty. 

The `flatMap` method of Optional works in the same way if you consider an optional value to be a stream of size zero or one. 

## Reduction Operations

If you want to compute a sum, or combine the elements of a stream to a result in another way, use one of the `reduce` methods. 

The `reduce` method below computes v<sub>0</sub>+v<sub>1</sub>+v<sub>2</sub>+... 
```Java
Stream<Integer> values = ...;
Optional<Integer> sum = values.reduce((x, y) -> x + y); // or values.reduce(Integer::sum);
```
The method returns an `Optional` because there is no valid result if the stream is empty. 

In general, if the `reduce` method has a reduction operation _op_, the reduction yields v<sub>0</sub> _op_ v<sub>1</sub> _op_ v<sub>2</sub> _op_ ..., where we write v<sub>i</sub> _op_ v<sub>i+1</sub> for the function call _op_(v<sub>i</sub>, v<sub>i+1</sub>)
The operation should be associative: it should not matter in which order you combine the elements. 

Useful associative operations: sum, product, string concatenation, maximum, minimum, set union and intersection.

subtraction is not associative. 

_identity_: e such that e op x = x. E.g, 0 is the identity for addition. 
```Java
Stream<Integer> values = ...;
Integer sum = values.reduce(0, (x, y) -> x + y);
```
The identity value is returned if the stream is empty, and you no longer need to deal with the `Optional` class. 

Simple form of `reduce` requires a function `(T, T) -> T`, with the same types for the arguments and the result. 

If you want to calculate all lengths in a stream of strings, you need to use accumulator. 
```Java
int result = words.reduce(0, 
  (total, word) -> total + word.length(), // accumulator is called repeatedly forming the cumulative total
  (total1, total2) -> total1 + total2); // combine multiple totals
```

Easier to map to a stream of numbers and use one of its methods to compute sum, max or min. 
```Java
words.mapToInt(String::length).sum(); // simpler and more efficient
```

## Collecting Results

Just want to look at the result. 

`iterator`: yields an old fashioned iterator that you can use to visit the elements. 
`toArray`: get an array of the stream elements. 

`stream.toArray()` returns an `Object[]` array. If you want an array of the correct type, pass in the array constructor:
```Java
String[] result = words.toArray(String[]::new);
```

`collect`: takes 3 arguments
1. A _supplier_ to make new instances of the target object, e.g, a constructor for hash set
2. An _accumulator_ that adds an element to the target, e.g, an `add` method
3. A _combiner_ that merges 2 objects into 1, such as `addAll`
```Java
HashSet<String> result = stream.collect(HashSet::new, HashSet::add, HashSet:addAll);
```
The target object need not to be a collection. It could be a `StringBuilder` or an object that tracks a count and a sum. 

`Collector`: convenient interface
`Collectors`: class with factory methods for common collectors
```Java
// collect a stream into a list or set
List<String> list = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
```

Control which kind of set you get
```Java
TreeSet<String> result = stream.collect(Collectors.toCollection(TreeSet::new));
```

Collect all strings in a stream by concatenating them
```Java
String result = stream.collect(Collectors.joining());
```

Add delimiter between elements
```Java
String result = stream.collect(Collectors.joining(","));
```

Convert non-string to string first 
```Java
String result = stream.map(Object::toString).collect(Collectors.joining(","));
```

If you want to reduce the stream results to a sum, average, maximum, or minimum, use one of the methods `summarizing(Int|Long|Double)`. These methods take a function that maps the stream objects to a number and yield a result of type `(Int|Long|Double)SummaryStatistics`, with methods for obtaining the sum, average, maximum and minimum. 
```Java
IntSummaryStatistics summary = words.collect(Collectors.summarizingInt(String::length));
double averageWordLength = summary.getAverage();
double maxWordLength = summary.getMax();
```

`forEach`: just want to print them or put them in a database
```Java
stream.forEach(System.out::println);
```
On a parallel stream, you must ensure that the function can be executed concurrently. 

`forEachOrdered`: execute in stream order. No parallelism benefits anymore. 

`forEach` and `forEachOrdered` are terminal operations. If you want to continue using the stream, use `peek` instead. 

## Collecting into Maps

Suppose you want to collect the elements in `Stream<Person>` into a map so that you can later look up people by their id. 
`Collectors.toMap`: has 2 function arguments that produce the map keys and values
```Java
Map<Integer, String> idToName = people.collect(Collectors.toMap(Person::getId, Person::getName));
```

`Funtion.identity()`: values should be the actual elements
```Java
Map<Integer, Person> idToPerson = people.collect(Collectors.toMap(Person::getId), Function.identity());
```

If there is more than one element with the same key, the collector will throw an `IllegalStateException`. Supply a third function argument that determines the value for the key, given the existing and the new value. 

Construct a map that contains, for each language in the available locales, as key its name in your default locale(such as "German"), and as value its localized name(such as "Deutsch"). 
```Java
Stream<Locale> locales = Stream.of(Locale.getAvailableLocales());
Map<String, String> languageNames = locales.collect(Collectors.toMap(
  l -> l.getDisplayLanguage(), 
  l -> l.getDisplayLanguage(l), 
  (existingValue, newValue) -> existingValue)); // keep old value
```

Suppose we want to know all languages in a given country, we need a `Map<String, Set<String>>`
```Java
Map<String, Set<String>> countryLanguageSets = locales.collect(Collectors.toMap(
  l -> l.getDisplayCountry(),
  l -> Collections.singleton(l.getDisplayLanguage()),
  (a, b) -> {
    Set<String> r = new HashSet<>(a);
    r.addAll(b);
    return r;
  }
));
```

If you want a `TreeMap`, then you supply the constructor as the 4th argument. 
```Java
Map<Integer, Person> idToPerson = people.collect(Collectors.toMap(
  Person::getId,
  Function.identity(),
  (existingValue, newValue) -> { throw new IllegalStateException(); }
  TreeMap::new
));
```

For each of the `toMap` methods, there is an equivalent `toConcurrentMap` method that yields a concurrent map. A single concurrent map is used in the parallel collection process. When used with a parallel stream, a shared map is more efficient than merging maps, but of course, you give up ordering. 
