title: 'Java SE 8 For the Really Impatient, Note 10'
date: 2016-03-29 15:21:42
categories:
  - Coding
  - Reading Note
tags:
  - Java
---

# Chapter 6 Concurrency Enhancements

## ConcurentHashMap Improvements

`mappingCount`: returns the size as a `long` in case it's too large.

An attacker can slow down a program by crafting a large number of strings that hash to the same value. As of Java 8, the concurrent hash map organizes the buckets as trees, not lists, when the key type implements `Comparable`, guaranteeing O(log(n)) performance. 

### Updating Values

The following is not threadsafe
```Java
Long oldValue = map.get(word);
Long newValue = oldValue == null ? 1 : oldValue + 1;
map.put(word, newValue); // not atomic, another thread can be updating at the same time
```

`replace`: replacing a known old value with a new one. 
```Java
do {
  oldValue = map.get(word);
  newValue = oldValue == null ? 1 : oldValue + 1;
} while (!map.replace(word, oldValue, newValue));
```

You can also use a `ConcurrentHashMap<String, AtomicLong>` or, with Java 8, a `ConcurrentHashMap<String, LongAdder>`
```Java
map.putIfAbsent(word, new LongAdder()); // ensures that a LongAdder is there
map.get(word).increment();
```

`compute`: called with a key and a function to compute the new value. That function receives the key and the associated value, or null if there is none, and it computes the new value. 
```Java
map.compute(word, (k, v) -> v == null ? 1 : v + 1);
```

You cannot have null values in a `ConcurrentHashMap`. There are many methods that use a null value as an indication that a given key is not present in the map. 

`computeIfPresent`: only computes a new value when there is already an old one. 
`computeIfAbsent`: only computes a new value when there isn't yet an old one. 

A map of `LongAdder` counters can be updated with
```Java
// LongAdder constructor is only called when a new counter is actually needed
map.computeIfAbsent(word, k -> new LongAdder()).increment();
```

`merge`: can do something special when a key is added for the first time. It has a particular parameter for the initial value that is used when the key is not yet present. Otherwise, the function that you supplied is called, combining the existing value and the initial value. (Unlike `compute`, the function does not process the key.)
```Java
map.merge(word, 1L, (existingValue, newValue) -> existingValue + newValue);
// more simply
map.merge(word, 1L, Long::sum);
```

If the function that is passed to `compute` or `merge` returns null, the existing entry is removed from the map. 

The function should not do a lot of work, otherwise other updates to the map may be blocked. And it should also not update other parts of the map. 

### Bulk Operations

The bulk operations travers the map and operate on the elements they find as they go along. No effort is made to freeze a snapshot of the map in time. Unless you happen to know that the map is not being modified while a bulk operation runs, you should treat its result as an approximation of the map's state. 

3 kinds of operations:
* `search`:applies a function to each key and/or value, until the function yields a non-null result. 
* `reduce`: combines all keys and/or values, using a provided accumulation function. 
* `forEach`: applies a function to all keys and/or values. 

Each operation has 4 versions:
* `searchKeys` / `reduceKeys` / `forEachKey`: operates on keys
* `searchValues` / `reduceValues` / `forEachValue`: operates on values
* `search` / `reduce` / `forEach`: operates on keys and values
* `searchEntries` / `reduceEntries` / `forEachEntry`: operates on `Map.Entry` objects

You need to specify a _parallelism threshold_ with each of the operations. If the map contains more elements than the threshold, the bulk operation is parallelized. If you want to run single thread, use a threshold of `Long.MAX_VALUE`. If you want the max number of threads, use a threshold of 1. 

Search
```Java
U searchKeys(long threshold, Function<? super K, ? extends U> f)
U searchValues(long threshold, Function<? super V, ? extends U> f)
U search(long threshold, BiFunction<? super K, ? super V, ? extends U> f)
U searchEntries(long threshold, Function<Map.Entry< K, V>, ? extends U> f)
```

Find the first word that occurs more than 1000 times
```Java
String result = map.search(threshold, (k, v) -> v > 1000 ? k : null);
```
The result is set to the first match, or to null if the search function returns null for all inputs. 

2 variants of `forEach`: the first one simply applies a `consumer` function for each map entry:
```Java
map.forEach(threshold, (k, v) -> System.out.println(k + "->" + v));
```

The second takes an additional `transformer` function, which is applied first, and its result is passed to the consumer:
```Java
map.forEach(threshold, 
  (k, v) -> k + "->" + v, // Transformer
  System.out::println); // Consumer
```

The transformer can be used as a filter. Whenever the transformer returns null, the value is silently skipped. 
```Java
// only print large values
map.forEach(threshold, 
  (k, v) -> v > 1000 ? k + "->" + v : null, // Filter and transformer
  System.out::println); // The nulls are not passed to the consumer
```

`reduce`: combines inputs with an accumulation function
```Java
// sum of all values
Long sum = map.reduceValues(threshold, Long::sum);
```

You can also supply a transformer function.
```Java
Integer maxLength = map.reduceKeys(threshold, 
  String::length, // Transformer
  Integer::max); // Accumulator
```

The transformer can also be a filter. 
```Java
Long count = map.reduceValues(threshold, 
  v -> v > 1000 ? 1L : null, 
  Long::sum);
```

The `reduce` operation returns null, if the map is empty, or all entries have been filtered out. If there is only one element, its transformation is returned, and the accumulator is not applied. 

There are specializations for `int`, `long` and `double` outputs with suffix `ToInt`, `ToLong`, and `ToDouble`. You need to transform the input to a primitive value and specify a default value and an accumulator function. The default value is returned when the map is empty. 
```Java
long sum = map.reduceValuesToLong(threshold, 
  Long::longValue, // Transformer to primitive type
  0, // Default value for empty map
  Long::sum // Primitive type accumulator
);
```

These specializations act differently from the object versions where there is only one element to be considered. Instead of returning the transformed element, it is accumulated with the default. Therefore, the default must be the neutral element of the accumulator. 

### Set Views

The static `newKeySet` method yields a `Set<K>` that is actually a wrapper around a `ConcurrentHashMap<Km, Boolean>`. (All map values are `Boolean.TRUE`.)
```Java
Set<String> words = ConcurrentHashMap.<String>newKeySet();
```

`keySet`: yields the set of keys. The set is mutable. If you remove the set's elements, the keys(and their values) are removed from the map. But it doesn't make sense to add elements to the key set, becase there would be no corresponding values to add. Java 8 adds a second `keySet` method to `ConcurrentHashMap`, with a default value, to be used when adding elements to the set:
```Java
Set<String> words = map.keySet(1L);
words.add("Java");
```

If "Java" wasn't already present in words, it now has a value of one.  