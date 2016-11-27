title: 'Java SE 8 For the Really Impatient, Note 6'
date: 2016-03-23 15:25:54
categories:
  - Coding
  - Notes
tags:
  - Java
---

# Chapter 3 Programming with Lambdas

## Returning Functions

Consider methods whose return type is a functional interface. 
```Java
Image brightenedImage = transform(image, Color:brighter);
```

Could you supply the desired brightness as an additional parameter to `transform`?
```Java
Image brightenedImage = transform(image, (c, factor) -> c.deriveColor(0, 1, factor, 1), 1.2);

// overload transform
public static <T> Iamge transform(Image in, BiFunction<Color, T, Color> f, T arg)
```

Another way: we can make a method that returns the appropriate `UnaryOperator<Color>`, with the brightness set
```Java
// returns a functional interface instance
public static UnaryOperator<Color> brighten(double factor) {
  return c -> c.deriveColor(0, 1, factor, 1);
}

Image brightenedImage = transform(image, brighten(1.2));
```

The function(functional interface instance) can be passed to another method that expects such an interface. 

You can write a method that yields a comparator for your needs, then pass to `Arrays.sort(values, comparatorGenerator(customization arguments))`.

## Composition

Using two transformations is not very efficient. We need to store intermediate results. It would be better if we could compose the operations and then apply the composite operation to each pixel. 
```Java
public static <T> UnaryOperator<T> compose(UnaryOperator<T> op1, UnaryOperator<T> op2) {
  return t -> op2.apply(op1.apply(t));
}

Image finalImage = transform(image, compose(Color:brighter, Color:grayscale));
```

## Laziness

Accumulate all operations and then fuse them. 

```Java
LatentImage latent = transform(image, Color::brighter);

public class LatentImage {
  private Image in;
  private List<UnaryOperator<Color>> pendingOperations;
  ...
  
  LatentImage transform(UnaryOperator<Color> f) {
    pendingOperations.add(f);
    return this;
  }
}
```

To avoid duplicate `transform` methods, an initial `stream()` operation is required to turn a collection into a stream. 

Since we can't add a method to the `Image` class, we can provide a `LatentImage` constructor or a static factory method. 
```Java
LatentImage latent = LatentImage.from(image).transform(Color::brighter).transform(Color::grayscale);
```

We can provide `toImage` method that applies all operations and returns the result
```Java
Image finalImage = LatentImage.from(image)
  .transform(Color::brighter).transform(Color::grayscale)
  .toImage();
  
public Image toImage() {
  int width = (int) in.getWidth();
  int height = (int) in.getHeight();
  WritableImage out = new WritableImage(width, height);
  for (int x = 0; x < width; x++)
    for (int y = 0; y < height; y++) {
      Color c = in.getPixelReader().getColor(x, y);
      for (UnaryOperator<Color> f : pendingOperations) c = f.apply(c);
      out.getPixelWriter().setColor(x, y, c);
    }
}
```

## Parallelizing Operations

When expressing operations as functional interfaces, the caller gives up control over the processing details, as long as the correct result is achieved. We can make use of concurrency. E.g, in image processing we can split the image into multiple strips and process each strip separately. 

An example on concurrent image transformation.
```Java
public static Color[][] parallelTransform(Color[][] in, UnaryOperator<Color> f) {
  int n = Runtime.getRuntime().availableProcessors();
  int height = in.length;
  int width = in[0].length;
  Color[][] out = new Color[height][width];
  try {
    ExecutorService pool = Executors.newCacheThreadPool();
    for (int i = 0; i < n; i++) {
      int fromY = i * height / n;
      int toY = (i + 1) * height / n;
      pool.submit(() -> {
        for (int x = 0; x < width; x++)
          for (int y = fromY; y < toY; y++)
            out[y][x] = f.apply(int[y][x]);
      });
      pool.shutdown();
      pool.awaitTermination(1, TimeUnit.HOURS);
    }
  } catch (InterruptedException ex) {
    ex.printStackTrace();
  }
  return out;
}
```

In general, when you are given an object of a functional interface and you need to invoke it many times, ask yourself whether you can take advantage of concurrency. 

## Dealing with Exceptions

When an exception is thrown in a lambda expression, it is propagated to the caller. 

```Java
public static void doInOrder(Runnable first, Runnable second) {
  first.run();
  second.run();
}
```
If `first.run()` throws an exception, then the `doInOrder` method is terminated, `second` is never run, and the caller gets to deal with the exception. 

Suppose we execute the tasks asynchronously.
```Java
public static void doInOrderAsync(Runnable first, Runnable second) {
  Thread t = new Thread() {
    public void run() {
      first.run();
      second.run();
    }
  };
  t.start();
}
```
If `first.run()` throws an exception, the thread is terminated, and `second` is never run. However, the `doInOrderAsync` returns right away and does the work in a separate thread, so it's not possible to have the method rethrow the exception.

In this situation, it is good to supply a handler.
```Java
public static void doInOrderAsync(Runnable first, Runnable second, Consumer<Throwable> handler) {
  Thread t = new Thread() {}
    public void run() {
      try {
        first.run();
        second.run();
      } catch (Throwable t) {
        handler.accept(t);
      }
    }
  };
  t.start();
}
```

Suppose that the `first` produces a result that is consumed by `second`.
```Java
public static void doInOrderAsync(Supplier<T> first, Consumer<T> second, Consumer<Throwable> handler) {
  Thread t = new Thread() {}
    public void run() {
      try {
        T result = first.get();
        second.accept(result);
      } catch (Throwable t) {
        handler.accept(t);
      }
    }
  };
  t.start();
}
```

Alternatively, we could make `second` a `Biconsumer<T, Throwable>` and have it deal with the exception from `first`.

It's often inconvenient that methods is functional interfaces don't allow check exception. You methods can accept functional interfaces whose methods allow checked exceptions, such as `Callable<T>` instead of `Supplier<T>`. A `Callable<T>` has a method that is declared as `T call() throws Exception`. 

If you want an equivalent for a `Consumer` or a `Function`, you have to create it yourself.

Fix this problem with a generic wrapper.
```Java
public static <T> Supplier<T> unchecked(Callable<T> f) {
  return () -> {
    try {
      return f.call();
    } catch (Exception e) {
      throw new RuntimeException(e);
    } catch (Throwable t) {
      throw t;
    }
  };
} 
```

Then you can pass a 
```Java
unchecked(() -> new String(Files.readAllBytes(Paths.get("/etc/passwd")), StandardCharsets.UTF_8))
```
to a `Supplier<String>`, even though the `readAllBytes` method throws an `IOException`. 

This method cannot generate a `Consumer<T>` or a `Function<T, U>`. You would need to implement a variabtion of `unchecked` for each functional interface. 

## Lambdas and Generics

You cannot construct a generic array at runtime. E.g, the `toArray()` method of `Collection<T>` and `Stream<T>` cannot call `T[] result = new T[n]`. Therefore, these methods return `Object[]` arrays. 

With lambdas, we can pass the constructor.
```Java
String[] result = words.toArray(String[]::new);
```
When you implement such a method, the constructor expression is an `IntFunction<T[]>`, since the size of the array is passed to the constructor. In your code, you call `T[] result = constr.apply(n)`.

`T` of `List<T>` is *invariant*. A method can decide to accept a `List<? extends Person>` if it only reads from the list. Then you can pass either a `List<Person>` or a `List<Employee>`. Or it can accept a `List<? super Employee>` if it only writes to the list. 

In general, reading is covariant(subtypes are okay) and writing is contravariant(supertypes are okay). 

As the implementor of a method that accepts lambda expressions with generic types, you simply add `? super` to any argument type that is not also a return type, and `? extends` to any return type that is not also an argumetn type. 

## Monadic Operations

A design pattern for providing compositions funtions that yield values from generic types. 

Consider a generic type `G<T>`, such as `List<T>`, `Optional<T>`, `Future<T>`. Also consider a function `T -> U`, or a `Function<T, U>` object. It often makes sense to apply this function to a `G<T>`. 

Generally, when you design a type `G<T>` and a function `T -> U`, think whether it makes sense to define a `map` that yields a `G<U>`. Then generalize to functions `T -> G<U>` and, if appropriate, provide `flatMap`.
