title: 'Java SE 8 For the Really Impatient, Note 12'
date: 2016-03-31 15:26:27
categories:
  - Coding
tags:
  - Java
  - Notes
---

# Chapter 8 Miscellaneous Goodies

Key points:
* Joining strings with a delimiter is finally easy: `String.join(",", "a", "b", "c")` instead of `a + ", " + b + ", " + c"`.
* Integer types now support unsigned arithmetic.
* The `Math` class has methods to detect integer overflow.
* Use `Math.floorMod(x, n)` instead of `x % n` if x might be negative.
* There are a few mutators in `Collection(removeIf)` and `List(replaceAll, sort)`.
* `Files.lines` lazily reads a stream of lines.
* `Files.list` lazily lists the entries of a directory, and `Files.walk` traverses them recursively. 
* There is finally official support for Base64 encoding. 
* Annotations can now be repeated and applied to type uses.
* Convenient support for null parameter checks can be found in the `Object` class. 

## Strings

A common task is to combine several strings, separating them with a delimiter such as ", " or "/". This has now been added to Java 8. The strings can come from an array or an `Iterable<? extends CharSequence>`. 
```Java
String joined = String.join("/", "usr", "local", "bin"); // "usr/local/bin"

String ids = String.join(", ", ZoneId.getAvailableZoneIds());
```

Think of `join` as the opposite of the `String.split` instance method. 

## Number Classes

Since Java 5, each of seven numeric primitive type wrappers(i.e, not `Boolean`) had a static `SIZE` field that gives the size of the type in bits.

There is now `BYTES` field that reports the size in bytes. 

All eight primitive type wrappers now have static `hashCode` methods that return the same hash code as the instance methods, but without the need for boxing. 

The five types `Short`, `Integer`, `Long`, `Float`, and `Double` now have static methods `sum`, `max` and `min`, which can be useful as reduction functions in stream operations. 

The `Boolean` class has static `logicalAnd`, `logicalOr`, and `logicalXor` for the same purpose. 

Integer types now support unsigned arithmetic. E.g., instead of having a `Byte` represent the range from -128 to 127, you can call the static method `Byte.toUnsignedInt(b)` and get a value between 0 and 255. The `Byte` and `Short` classes have methods `toUnsignedInt`, and `Byte`, `Short` and `Integer` have methods `toUnsignedLong`. 

The `Integer` and `Long` classes have methods `compareUnsigned`, `divideUnsigned`, and `remainderUnsigned` to work with unsigned values. Integer multiplication would overflow with unsigned integers larger than `Integer.MAX_VALUE`, so you should call `toUnsignedLong` and multiply them as long values. 

The `Float` and `Double` classes have static methods `isFinite`. The call `Double.isFinite(x)` returns true if x is not infinity, negative infinity, or a NaN. In the past, you had to call the instance methods `isInfinite` and `isNaN` to get the same result. 

The `BigInteger` class has instance methods `(long|int|short|byte)ValueExact` that return the values as a long, int, short, or byte, throwing an `ArithmeticException` if the value is not within the target range. 

## New Mathematical Functions

The `Math` class provides several methods for "exact" arithmetic that throw an exception when a result overflows. E.g., 100000 * 100000 quietly gives the wrong result 1410065408, whereas `multiplyExact(100000, 100000)` throws an exception. The provided methods are `(add|subtract|multiply|increment|decrement|negate)Exact` with `int` and `long` parameters. The `toIntExact` method converts a `long` to the equivalent `int`. 

The `floorMod` and `floorDiv` methods aim to solve a long-standing problem with integer remainders: n % 2 is -1 when n is negative and odd. `floorMod(position + adjustment, 12)` always yields a value between 0 and 11. 

Unfortunately, `floorMod` gives negative results for negative divisors, but that situation doesn't often occur in practice. 

The `nextDown` method, defined for both `double` and `float` parameters, gives the next smaller floating-point number for a given number. E.g., if you promise to produce a number < b, but you happen to have computed exactly b, then you can return `Math.nextDown(b)`.(The corresponding `Math.nextUp` method exists since Java 6.) 

All methods described in this section also exist in the `StrictMath` class. 

## Collections

### Methods Added to Collection Classes

**Methods Added to Collections Classes and Interface in Java 8, other than the stream, parallelStream, and spliterator**
<br>

| Class/Interface | New Methods |
| :-------------- | :---------- |
| Iterable | forEach |
| Collection | removeIf |
| List | replaceAll, sort |
| Map | forEach, replace, replaceAll, remove(key, value)(removes only if key mapped to value),<br>putIfAbsent, compute, computeIf(Absent &#124; Present), merge|
| Iterator | forEachRemaining |
| BitSet | stream |

`removeIf`: can be thought of as the opposite of `filter`, removing rather than producing all matches and carrying out the removal in place. The `distinct` method would be costly to provide on arbitrary collections. 

The `List` interface has a `replaceAll` method, which is an in-place equivalent of `map`, and a `sort` method that is obviously useful. 

The `Iterator` interface has a `forEachRemaining` method that exhausts the iterator by feeding the remaining iterator elements to a function. 

The `BitSet` class has a method that yields all members of the set as a stream of int values. 

### Comparators

The static `comparing` method takes a "key extractor" function that maps a type T to a comparable type (such as String). The function is applied to the objects to be compared, and the comparison is then made on the returned keys. 
```Java
// sort an array of Person by name
Arrays.sort(people, Comparator.comparing(Person::getName));
```

You can chain comparators with `thenComparing` method for breaking ties
```Java
Arrays.sort(people, 
  Comparator.comparing(Person::getLastName))
    .thenComparing(Person::getFirstName));
```
If two people have the same last name, then the second comparator is used. 

You can specify comparator to be used for the keys that the `comparing` and `thenComparing` methods extract.
```Java
Arrays.sort(people, Comparator.comparing(Person::getName, (s, t) -> Integer.compare(s.length(), t.length())));
```

Both the `comparing` and `thenComparing` methods have variants that avoid boxing of int, long, or double values. 
```Java
Arrays.sort(people, Comparator.comparingInt(p -> p.getName().length()));
```

If your key function can return null, use `nullsFirst` or `nullsLast` adapters. These static methods take an existing comparator and modify it so that it doesn't throw an exception when encountering null values but ranks them as smaller or larger than regular values. Suppose `getMiddleName` returns a null when a person has no middle name. Then you can use `Comparator.comparing(Person::getMiddleName(), Comparator.nullsFirst(...))`. 

The `naturalOrder` method makes a comparator for any class implementing `Comparable`. A `Comparator.<String>natrualOrder()` is what we need. 
```Java
static import java.util.Comparator.*;

Arrays.sort(people, comparing(Person::getMiddleName, nullsFirst(naturalOrder())));
```

The static `reverseOrder` method gives the reverse of the natural order. To reverse any comparator, use the `reversed` instance method. `anturalOrder().reversed()` is the same as `reverseOrder()`. 

### The Collections Class

Java 6 introduces `NavigableSet` and `NavigableMap` interfaces that take advantage of the ordering of the elements or keys, providing efficient methods to locate, for any given value v, the smallest element >= or > v, or the largest element <= or < v.

The `Collections` class supports these interfaces as it does other collections, with methods `(unmodifiable|synchronized|check|empty)Navigable(Set|Map)`. 

A `checkedQueue` wrapper has also been added. As as reminder, the `checked` wrappers have a `Class` parameter and throw a `ClassCastException` when you insert an element of the wrong type. These classes are intended as debugging aids. Suppose you declare a `Queue<Path>`, and somewhere in your code there is a `ClassCastException` trying to cast a String to a Path. If you temporarily replace the queue with a `CheckedQueue(new LinkedList<Path>, Path.class)`, then every insertion is checked at runtime, and you can locate the faulty insertion code. 

There are `emptySorted(Set|Map)` methods that give lightweight instances of sorted collections, analogous to the `empty(Set|Map)` methods. 
