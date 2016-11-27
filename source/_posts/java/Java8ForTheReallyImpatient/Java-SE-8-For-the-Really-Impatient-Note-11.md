title: 'Java SE 8 For the Really Impatient, Note 11'
date: 2016-03-30 14:56:21
categories:
  - Coding
  - Notes
tags:
  - Java
---

# Chapter 6 Concurrency Enhancements

## Parallel Array Operations

`Arrays` class now has a number of parallelized operations. 

`Arrays.parallelSort`: sort an array of primitive values or objects. 
```Java
String contents = new String(Files.readAllBytes(Paths.get("alice.txt")), StandardCharsets.UTF_8);
String[] words = contents.split("[\\P{L}]+"); // Split along nonletters
Arrays.parallelSort(words);
```

You can supply a `Comparator`. 

You can supply the bounds of a range.
```Java
Arrays.parallelSort(values, values.length / 2, values); // Sort the upper half
```

`parallelSetAll`: fills an array with values that are computed from a function. The function receives the element index. There are versions for all primitive type arrays and for object arrays. 
```Java
Arrays.parallelSetAll(values, i -> i % 10);
```

`parallelPrefix`: replaces each array element with the accumulation of the prefix for a given associative operation. 
```Java
// [1, 2, 3, 4, ...] and x
Arrays.parallelPrefix(values, (x, y) -> x * y);
// [1, 1 x 2, 1 x 2 x 3, 1 x 2 x 3 x 4, ...]
```

This can be parallelized in this way: join neighboring elements first, then update the indicated elements by multiplying them with elements that are one or two positions below. 

## Completable Futures

The `java.util.concurrent` library provides a `Future<T>` interface to denote a value of type `T` that will be available at some point in the future. 

_Completable futures_ make it possible to compose asynchronous operations. 

### Futures

Consider a method that reads a web page in a separate thread, which is going to take a while. When you call it, the method returns right away, and you have a `Future<String>`. 
```Java
public Future<String> readPage(URL url)

Future<String> contents = readPage(url);
```

Suppose we want to extract all URLs from the page in order to build a web crawler. We have a class `Parser` with a method
```Java
public static List<URL> getLinks(String page)
```
How can we apply it to the future object? First, call the `get` method on the future to get its value when it becomes available. Then, process the result:
```Java
String page = contents.get(); // blocking
List<URL> links = Parser.getLinks(page);
```
No better off than with a method `public String readPage(URL url)` that blocks until the result is available. 

### CompletableFuture<T>

Provides the feature to set "when the result becomes available, here is how to process it". A `CompletableFuture` has a method `thenApply` to which you can pass the post-processing function. 

```Java
CompletableFuture<String> contents = readPage(url);
CompletableFuture<List<URL>> links = contents.thenApply(Parser::getLinks);
```

The `thenApply` method doesn't block either. It returns another future. When the first future has completed. its result is fed to the `getLinks` method, and the return value of that method becomes the final result. 

This _composability_ is the key aspect of the `CompletableFuture` class. Composing future actions solves a problem in programming asynchronous applications. 

### The Composition Pipeline

Pipeline of futures starts out by generating a `CompletableFuture`, usually with the static method `supplyAsync`. That method requires a `Supplier<T>`, that is, a function with no parameters yielding a `T`. The function is called on a separate thread. 
```Java
CompletableFuture<String> contents = CompletableFuture.supplyAsync(() -> blockingReadPage(url));
```

`runAsync`: takes a `Runnable`, yielding a `CompletableFuture<Void>`. Useful if you simply want to schedule one action after another, without passing data between them. 

All methods ending in `Async` have two variants. One runs the provided action on the common `ForkJoinPool`. The other has a parameter of type `java.util.concurrent.Executor`, and it uses the given executor to run the action. 

Next, you can call `thenApply` or `thenApplyAsync` to run another action, either in the same thread or another. With either method, you supply a function and you get a `CompletableFuture<U>`, where `U` is the return type of the function. 
```Java
CompletableFuture<List<URL>> links = CompletableFuture
  .supplyAsync(() -> blockingReadPage(url))
  .thenApply(Parser::getLinks);
```

You can have additional processing steps. Eventually, you will be done. 
`thenAccept`: takes a `Consumer`, a function with return type `void`. Ideally, you would never call `get` on a future. The last step in the pipeline simply deposits the result where it belongs. 
```Java
CompletableFuture<Void> links = CompletableFuture.supplyAsync(() -> blockingReadPage(url)).thenApply(Parser::getLinks)
  .thenAccept(System.out::println);
```

You don't explicitly start the computation. The static `supplyAsync` method starts it automatically, and the other methods cause it  to be continued. 

### Composing Asynchronous Operations

**Adding an Action to a CompletableFuture<T> Object**
<br>

| Method | Parameter | Description |
| :----- | :-------- | :---------- |
| thenApply | T -> U | Apply a function to the result |
| thenCompose | T -> CompletableFuture<U> | Invoke the function on the result and execute the returned future |
| handle | (T, Throwable) -> U | Process the result or error |
| thenAccept | T -> void | Like thenApply, but with void result |
| whenComplete | (T, Throwable) -> void | Like handle, but with void result |
| thenRun | Runnable | Execute the Runnable with void result |

For each method shown, there are also two `Async` variants. 

T -> U is Function<? super T, U>

The calls `CompletableFuture<U> future.thenApply(f)` and `CompletableFuture<U> future.thenApplyAsync(f)` return a future that applies f to the result of future when it is available. The second call runs f in another thread. 

`thenCompose`: takes a function `T -> CompletableFuture<U>`. 

Here we have two functions: T -> CompletableFuture<U> and U -> CompletableFuture<V>. Clearly, they compose to a function T -> CompletableFuture<V> by calling the second function when the first one has completed. That is exactly what `thenCompose` does. 
```Java
public CompletableFuture<String> readPage(URL url)

public CompletableFuture<URL> getURLInput(String prompt)
```

`handle`: handles an exception thrown in a CompletableFuture. The supplied function is called with the result (or null if none) and the exception (or null if none), and it gets to make sense of the situation. 

**Combining Multiple Composition Objects**
<br>

| Method | Parameters | Description |
| :----- | :--------- | :---------- |
| thenCombine | CompletableFuture<U>, (T, U) -> V | Execute both and combine the results with the given function. |
| theAcceptBoth | CompletableFuture<U>, (T, U) -> void | Like thenCombine, but with void result. |
| runAfterBoth | CompletableFuture<?>, Runnable | Execute the runnable after both complete. |
| applyToEither | CompletableFuture<T>, T -> V | When a result is available from one or the other, pass it to the given function |
| acceptEither | CompletableFuture<T>, T -> void | Like applyToEither, but with void result. |
| runAfterEither | CompletableFuture<?>, Runnable | Execute the runnable after one or the other completes. |
| static allOf | CompletableFuture<?>... | Complete with void result after all given futures complete. |
| static anyOf | CompletableFuture<?>... | Complete with void result after any of the given futures completes. |

The first three methods run a `CompletableFuture<T>` and a `CompletableFuture<U>` action in parallel and combine the results. 

The next three methods run two `CompletableFuture<T>` actions in parallel. As soon as one of them finishes, its result is passed on, and the other result is ignored. 

The static `allOf` and `anyOf` methods take a variable number of completable futures and yield a `CompletableFuture<Void>` that completes when all of them, or any one of them, completes. No results are propagated. 

Technically speaking, the methods accept parameters of `CompletionStage`, not `CompletableFuture`. That is an interface type with almost forty abstract methods, currently implemented only by `CompletableFuture`. 