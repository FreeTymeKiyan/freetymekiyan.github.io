title: 'Java SE 8 For the Really Impatient, Note 9'
date: 2016-03-29 15:19:11
categories:
  - Coding
tags:
  - Java
  - Notes
---

# Chapter 6 Concurrency Enhancements

`java.util.concurrent` is a mix of useful utilities for the application programmer and power tools for library authors, without much effort to separate the two. 

Key points:
* Updating atomic variables has become simpler with the `updateAndGet/accumulateAndGet` methods.
* `LongAccumulator`/`DoubleAccumulator` are more efficient than `AtomicLong`/`AtomicDouble` under high contention. 
* Updating entries in a `ConcurrentHashMap` has become simpler with the `compute` and `merge` methods. 
* `ConcurrentHashMap` now has bulk operations `search`, `reduce`, `forEach`, with variants operating on keys, values, keys and values, and entries.
* A set view lets you use a `ConcurrentHashMap` as `Set`. 
* The `Arrays` class has methods for parallel sorting, filling, and prefix operations. 
* Completable futures let you compose asynchronous operations. 

## Atomic Values

`java.util.concurrent.atomic` package provided classes for lock-free mutation of variables since Java 5. You can safely generate a sequence of numbers like below:
```Java
public static AtomicLong nextNumber = new AtomicLong();
// In some thread...
long id = nextNumber.incrementAndGet();
```

`incrementAndGet`: atomically increments the `AtomicLong` and returns the post-increment value. 

If you want to make a more complex update, you have to use the `compareAndSet` method. Suppose you want to keep track of the largest value that is observed by different thread. 
```Java
public static AtomicLong largest = new AtomicLong();
// In some thread...
largest.set(Math.max(largest.get(), observed)); // Error - race condition!
```

Instead, compute the new value and use `compareAndSet` in a loop.
```Java
do {
  oldValue = largest.get();
  newValue = Math.max(oldValue, observed);
} while (!largest.compareAndSet(oldValue, newValue));
```
If another thread is also updating `largest`, it is possible the it has beat this thread to it. Then `compareAndSet` will return `false` without setting the new value. The loop tries again. Eventually, it will succeed replacing the existing value with the new one. The `compareAndSet` method maps to a processor operation that is faster than using a lock. 

In Java 8, you can use a lambda expression. 
```Java
largest.updateAndGet(x -> Math.max(x, observed));
// or 
largest.accumulateAndGet(observed, Math::max);
```
The `accumulateAndGet` method takes a binary operator that is used to combine the atomic value and the supplied argument. 

Also see `getAndUpdate` and `getAndAccumulate` the return the old value. 

These methods are also provided for: 
* `AtomicInteger`
* `AtomicIntegerArray`
* `AtomicIntegerFieldUpdater`
* `AtomicLongArray`
* `AtomicLongFieldUpdater`
* `AtomicReference`
* `AtomicReferenceArray`
* `AtomicReferenceFieldUpdater`

`LongAdder` and `LongAccumulator` can be used to solve the problem that a large number of threads accessing the same atomic values. 

`LongAdder`: composed of multiple variables whose collective sum is the current value. Multiple threads can update different summands, and new summands are automatically provided when the number of threads increases. Efficient when the value of the sum is not needed until after all work has been done. If you anticipate high contention, you should simply use a `LongAdder` instead of an `AtomicLong`. Call `increment` to increment a counter, or `add` to add a quantity, and `sum` to retrieve the total. 

```Java
final LongAdder adder = new LongAdder();
for (...) 
  pool.submit(() -> {
    while (...) {
      ...
      if (...) adder.increment();
    }
  });
long total = adder.sum();
```

`increment` does not return the old value which would undo the efficiency gain. 

`LongAccumulator`: generalizes the idea to an arbitrary accumulation operaiton. Provide the operation and its neutral element in the constructor. Call `accumulate` to incorporate new values. Call `get` to obtain the current value. 
```Java
LongAccumulator adder = new LongAccumulator(Long::sum, 0);
// In some thread...
adder.accumulate(value);
```

Internally, the accumulator has variables a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>n</sub>. Each variable is initialized with the neutral element. 

When `accumulate` is called with value v, then one of them is atomically updated as a<sub>i</sub> = a<sub>i</sub> op v, where op is the accumulation operation written in infix form. In the above example, a call to `accumulate` computes a<sub>i</sub> = a<sub>i</sub> + v for some i. 

The result of `get` is a<sub>1</sub> op a<sub>2</sub> op ... op a<sub>n</sub>. 

If you choose a different operation, you can compute maximum or minimum. 

The operation must be associative and commutative, meaning that the final result must be independent of the order. 

`DoubleAdder` and `DoubleAccumulator` work in the same way with `double` values. 

`StampedLock`: can be used to implement optimistic reads. Not recommended to use locks.

First call `tryOptimisticRead`, upon which you get a "stamp". Read your values and check whether the stamp is still valid(no other thread has obtained a write lock). If so, you can use the values. If not, get a read lock (which blocks any writers).
```Java
public class Vector {
  private int size;
  private Object[] elements;
  private StampedLock lock = new StampedLock();
  
  public Object get(int n) {
    long stamp = lock.tryOptimisticRead();
    Object[] currentElements = elements;
    int currentSize = size;
    if (!lock,validate(stamp)) { // Someone else had a write lock
      stamp = lock.readLock(); // Get a pessimistic lock
      currentElements = elements;
      currentSize = size;
      lock.unlockRead(stamp);
    }
    return n < currentSize ? currentElements[n] : null;
  }
  ...
}
```