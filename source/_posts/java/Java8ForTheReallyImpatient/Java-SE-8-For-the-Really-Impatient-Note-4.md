title: 'Java SE 8 For the Really Impatient, Note 4'
date: 2016-03-21 14:12:12
categories:
  - Coding
  - Notes
tags:
  - Java
---

# Chapter 2 The Stream API

## Grouping and Partioning

`groupingBy`: forms groups of values with the same characteristic
```Java
// grouping locales by country
Map<String, List<Locale>> countryToLocales = locales.collect(Collectors.groupingBy(Locale::getCountry));
```
The function `Locale::getCountry` is the _classifier function_ of the grouping. 
```Java
// look up all locales for a given country code
List<Locale> swissLocales = countryToLocales.get("CH");
```

When the classifier function is a predicate function(that is, a function returning a `boolean` value), the stream elements are partitioned into 2 list: those where the function returns true and the complement. In this case it's more efficient to use `partitioningBy` instead. 
```Java
Map<Boolean, List<Locale>> englishAndOtherLocales = locales.collect(Collectors.partitioningBy(l -> l.getLanguage().equals("en")));

List<Locale> englishLocales = englishAndOtherLocales.get(true);
```

If you call the `groupingByConcurrent` method, you get a concurrent map that, when used with a parallel stream, is concurrently populated. Analogous to `toConcurrentMap`. 

If you want sets instead of list, use downstream collector `Collectors.toSet`. 
```Java
static import java.util.stream.Collectors.*;

Map<String, Set<Locale>> countryToLocaleSet = locales.collect(groupingBy(Locale::getCountry, toSet()));
```

Other downstream collectors
`counting`: produces a count of the collected elements
```Java
// counts how many locales there are for each country
Map<String, Long> countryToLocaleCounts = locales.collect(groupingBy(Locale:: getCountry, counting()));
```

`summing(Int|Long|Double)`: takes a function argument, applies the function to the downstream elements, and produces their sum
```Java
// the sum of populations per state in a stream of cities
Map<String, Integer> stateToCityPopulation = cities.collect(groupingBy(City::getState, summingInt(City::getPopulation)));
```

`maxBy` and `minBy`: take a comparator and produce max and min of the downstream elements
```Java
// the city with most population per state
Map<String, City> stateToLargestCity = cities.collect(groupingBy(City::getState, maxBy(Comparator.comparing(City::getPopulation))));
```

`mapping`: applies a function to downstream results, and it requires yet another collector for processing its results
```Java
// group cities by state
// within each state, produce the city names and reduce by max length
Map<String, Optional<String>> stateToLongestCityName = cities.collect(
  groupingBy(City::getState, 
    mapping(City::getName, maxBy(Comparator.comparing(String::length)))));
```

```Java
// gather a set of all languages in a country
Map<String, Set<String>> countryToLanguages = locales.collect(
  groupingBy(l -> l.getDisplayCountry(), 
    mapping(l -> l.getDisplayLanguage(), toSet()))); // no combining sets
```

summary statistics object: if the grouping or mapping function has return type `int`, `long`, or `double`
```Java
Map<String, IntSummaryStatistics> stateToCityPopulationSummary = cities.collect(
  groupingBy(City::getState, summarizingInt(City::getPopulation))
);
```

`reducing`: applies a general reduction to downstream elements. 3 forms: 
* `reducing(binaryOperator)` (identity is null)
* `reducing(identity, binaryOperator)`
* `reducing(identity, mapper, binaryOperator)`, mapper function is applied and its values are reduced

```Java
// gets a comma-separated string of all city names in each state
Map<String, String> stateToCityNames = cities.collect(
  groupingBy(City::getState, reducing("", City::getName, 
    (s, t) -> s.length() == 0 ? t : s + ", " + t))
);
```

```Java
// more naturally, same result
Map<String, String> stateToCityNames = cities.collect(
  groupingBy(City::getState, 
    mapping(City::getName, joining(", ")));
);
```

Only use downstream collectors in connection with `groupingBy` or `partitioningBy` to avoid convoluted expressions. Otherwise, simply use methods like `map`, `reduce`, `count`, `max` or `min` directly on streams. 

## Primitive Type Streams

Wrap each integer into a wrapper object like `Stream<Integer>` is inefficient. Same for the other primitive types. 

`IntStream`, `LongStream`, `DoubleStream` can store primitive values directly. 
For the other primitives: 
`IntStream`: store short, char, byte and boolean
`DoubleStream`: float

Create an `IntStream`: use `IntStream.of` or `Arrays.stream`
```Java
IntStream stream = IntStream.of(1, 1, 2, 3 ,5);
// or
stream = Arrays.stream(values, from, to); // values is an int[] array
```

`IntStream` and `LongStream` have static methods `range` and `rangeClosed` that generate integer ranges with step size one
```Java
IntStream zeroToNinetyNine = IntStream.range(0, 100); // Upperbound excluded
IntStream zeroToHundred = IntStream.rangeClose(0, 100); // Upperbound included
```

The `CharSequence` interface has methods `codePoints` and `chars` that yield an `IntStream` of the Unicode codes of the characters or of the code units in the UTF-16 encoding
```Java
String sentence = "\uD835\uDD46 is the set of octonions";
IntStream codes = sentence.codePoints(); // hex values
```

Use `mapToInt`, `mapToLong`, `mapToDouble` methods to transform a stream of objects to primitive types
```Java
Stream<String> words = ...;
IntStream lengths = words.mapToInt(String::length);
```

`boxed`: converts a primitive type stream to an object stream
```Java
Stream<Integer> integers = IntStream.range(0, 100).boxed();
```

Differences between primitive type streams and object streams:
* `toArray` returns primitive type arrays
* Methods that yield an optional result return an `OptionalInt`, `OptionalLong` or `OptionalDouble`. They have methods `getAsInt`, `getAsLong` and `getAsDouble` instead of `get`. 
* `sum`, `average`, `max`, `min` are defined.
* The `summaryStatistics` method yield an object of type `IntSummaryStatistics`, `LongSummaryStatistics`, or `DoubleSummaryStatistics`

The `Random` class has methods `ints`, `longs` and `doubles` that return primitive type streams of random numbers

## Parallel Streams

Must have a parallel stream to parallelize bulk operations. 

By default, stream operations create sequential streams, except for `Collection.parallelStream()`. 

`parallel`: converts any sequential stream into a parallel one
```Java
Stream<String> parallelWords = Stream.of(wordArray).parallel();
```

The operations are stateless and can be executed in arbitrary order. 
A bad example, something you cannot do
```Java
int[] shortWords = new int[12];
words.parallel().forEach(
  s -> { if (s.length() < 12) shortWords[s.length()]++; }
);
System.out.println(Arrays.toString(shortWords));
```
The function passed to `forEach` runs concurrently in multiple threads, updating a shared array. Race condition!

Ensure that any functions you pass to parallel stream operations are threadsafe. You can use an array of `AtomicInteger` objects. Or you can simply use the facilities of streams library and group strings by length. 

By default, streams that arise from ordered collections (arrays and lists), from ranges, generators, and iterators, or from calling `Stream.sorted`, are ordered. 

Some operations can be more effectively parallelized when the ordering requirement is dropped. 
`Stream.unordered` means there will be no ordering. `Stream.distinct` can benefit from it because on an ordered stream, `distinct` retains the first of all equal elements.That impedes parallelization. `limit` can be speeded up if you just want any n elements from a stream and don't care which ones you get. 
```Java
Stream<T> sample = stream.parallel().unordered().limit(n);
```

Merging map is expensive. The `Collectors.groupingByConcurrent` method uses a shared concurrent map. The collector is unordered already. 
```Java
Map<String, List<String>> result = cities.parallel().collect(
  Collectors.groupingByConcurrent(City::getState) // values aren't collected in stream order
);
```

**Noninterference**
Do not modify the collection that is backing a stream while carrying out a stream operation, even if it's threadsafe. Remember that streams don't collect their own data - the data is always in a separate collection. 

Since intermediate stream operations are lazy, it's possible to mutate the collection up to the point when the terminal operation executes. 
```Java
List<String> wordList = ...;
Stream<String> words = wordList.stream();
wordsList.add("END"); // OK
long n = words.distinct().count();
```

Bad example updating collection during operation
```Java
Stream<String> words = wordList.stream();
words.forEach(s -> if (s.length() < 12)) wordList.remove(s)); // interference
```

## Functional Interfaces

`Predicate`: an interface with one nondefault method returning a boolean value
```Java
public interface Predicate<T> {
  boolean test(T argument);
}
```
boolean return type is important. 

**Functional Interfaces Used in the Stream API**
</br>

|Functional Interfaces|Parameter Types|Return Type|Description|
|:--------------------|:--------------|:----------|:----------|
|Supplier&lt;T&gt;|None|T|Supplies a value of type T|
|Consumer&lt;T&gt;|T|void|Consumes a value of type T|
|BiConsumer&lt;T, U&gt;|T, U|void|Consumes values of types T and U|
|Predicate&lt;T&gt;|T|boolean|A Boolean-valued function|
|ToIntFunction&lt;T&gt;</br>ToLongFunction&lt;T&gt;</br>ToDoubleFunction&lt;T&gt;|T|int</br>long</br>double|An int-, long-, or double-valued function|
|IntFunction&lt;R&gt;</br>LongFunction&lt;R&gt;</br>DoubleFunction&lt;R&gt;|int</br>long</br>double|R|A function with argument of type int, long, or double|
|Function&lt;T, R&gt;|T|R|A function with argument of type T|
|BiFunction&lt;T, U, R&gt;|T, U|R|A function with arguments of types T and U|
|UnaryOperator&lt;T&gt;|T|T|A unary operator on the type T|
|BinaryOperator&lt;T&gt;|T, T|T|A binary operator on the type T|

