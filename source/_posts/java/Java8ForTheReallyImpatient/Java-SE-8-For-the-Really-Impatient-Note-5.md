title: 'Java SE 8 For the Really Impatient, Note 5'
date: 2016-03-22 13:44:25
categories:
  - Coding
  - Notes
tags:
  - Java
---

# Chapter 3 Programming with Lambdas

Learn how to create your own libraries that make use of lambda expressions and functional interfaces. 

Key points:
* Main reason for using lambda expression: defer the execution of the code until an appropriate time
* When a lambda expression is executed, make sure to provide any required data as inputs
* Choose one of the existing functional interfaces if you can
* It's often useful to write methods that return an instance of a functional interface
* When you work with transformations, consider how you can compose them
* To compose transformations lazily, you need to keep a list of all pending transformations and apply them in the end
* If you need to apply a lambda many times, you often have a chance to split up the work into subtasks that execute concurrently
* Think what should happen when you work with a lambda expression that throws an exception
* When working with generic functional interfaces, use `? super` wildcards for argument types, `? extends` wildcards for return types
* When working with generic types that can be transformed by functions, consider supplying `map` and `flatMap`

## Deferred Execution

The point of all lambdas is deferred execution. Reasons for executing code later:
* Running the code in a separate thread
* Running the code multiple times
* Running the code at the right point in an algorithm(e.g, the comparison operation in sorting)
* Running the code when something happens(a buttom was clicked, data has arrived, etc.)
* Running the code only when necessary

Suppose you log an event:
```Java
logger.info("x: " + x + ", y: " + y);
```
If the log level is set to suppress INFO messages, the message string still gets computed and passed to the method, then it would be thrown away. It would be nicer if the string concatenation only happened when necessary. 

Running code only when necessary is a use case for lambdas. Write a method that:
1. Accepts the lambda
2. Checks whether it should be called
3. Calls it when necessary

```Java
public static void info(Logger logger, Supplier<String> message) {
  if (logger.isLoggable(Level.info))
    logger.info(message.get());
}
```

The `info` method, as well as other logging methods, now have variants that accept a `Supplier<String>`. You can directly call `logger.info(() -> "x: " + x + ", y: " + y);`

## Parameters of Lambda Expressions

Comparator has 2 arguments
```Java
Arrays.sort(names, 
  (s, t) -> Integer.compare(s.length(), t.length()));
```

This method repeats an action multiple times
```Java
public static void repeat(int n, IntConsumer action) {
  for (int i = 0; i < n; i++) action.accept(i);
}
```
We tell the action in which iteration it occurs. The action needs to capture that input in a parameter. 
```Java
repeat(10, i -> System.out.println("Countdown: " + (9 - i)));
```

An event handler `button.setOnAction(event -> action)`. The `event` object carries information that the action may need. 

In general, you want to design your algorithm so that it passes any required information as arguments. However, if these arguments are rarely needed, consider supplying a second version that doesn't force users into accepting unwanted arguments.
```Java
public static void repeat(int n, Runnable action) {
  for (int i = 0; i < n; i++) action.run();
}
```
This version can be called as
```Java
repeat(10, () -> System.out.println("Hello, world!"));
```

## Choosing a Functional Interface

Function types are structural.

To specify a function that maps 2 strings into an integer, you use `Function2<String, String, Integer>` or `(String, String) -> int`.

In Java, you instead declare the intent of the function, using a functional interface such as `Comparator<String>`.

In the theory of programming languages this is called _nominal_ typing.

If you want to accept "any function" without particular semantics, there are some options. 

|Functional Interfaces|Parameter Types|Return Type|Abstract Method Name|Description|Other Methods|
|:--------------------|:--------------|:----------|:-------------------|:----------|:------------|
|Runnable|none|void|run|Runs an action without arguments or return value||
|Supplier&lt;T&gt;|none|T|get|Supplies a value of type T||
|Consumer&lt;T&gt;|T|void|accept|Consumes a value of type T|andThen|
|BiConsumer&lt;T, U&gt;|T, U|void|accept|Consumes values of types T and U|andThen|
|Function&lt;T, R&gt;|T|R|apply|A function with argument of type T|compose</br>andThen</br>identity|
|BiFunction&lt;T, U, R&gt;|T, U|R|apply|A function with arguments of types T and U|andThen|
|UnaryOperator&lt;T&gt;|T|T|apply|A unary operator on the type T|compose</br>andThen</br>identity|
|BinaryOperator&lt;T, T&gt;|T, T|T|apply|A binary operator of the type T|andThen</br>maxBy</br>minBy|
|Predicate&lt;T&gt;|T|boolean|test|A Boolean-valued function|and</br>or</br>negate</br>isEqual|
|Bipredicate&lt;T, U&gt;|T, U|boolean|test|A Boolean-valued function with 2 arguments|and, or</br>negate|

Most of the standard functional interfaces have nonabstract methods for producing or combining functions. 
`Predicate.isEqual(a)` is the same as `a::equals`, provided a is not null. And there are default methods `and`, `or`, `negate` for combining predicates. E.g, `Predicate.isEqual(a).or(Predicate.isEqual(b))` is the same as `x -> a.equals(x) || b.equals(x)`.

Applying `Color -> Color` function to each pixel.
```Java
// use UnaryOperator<Color>
Image brightenedImage = transform(image, Color::brighter);

public static Image transform(Image in, UnaryOperator<Color> f) {
  int width = (int) in.getWidth();
  int height = (int) in.getHeight();
  WritableImage out = new WritableImage(width, height);
  for (int x = 0; x < width; x++) 
    for (int y = 0; y < height: y++) 
      out.getPixelWriter().setColor(x, y, f.apply(in.getPixelReader().getColor(x, y)));
  return out;
}
```
No need for a `ColorTransformer` interface. 

**Functional Interfaces for Primitive Types**
</br>

| Functional Interface | Parameter Types | Return Type | Abstract Method Name |
| :------------------- | :-------------- | :---------- | :------------------- |
| BooleanSupplier | none | boolean | getAsBoolean |
| *P*Supplier | none | *P* | getAs*P* |
| *P*Consumer | *P* | void | accept |
| Obj*P*Consumer |T, *p* | void | accept |
| *P*Function&lt;T&gt; | *p* | T | apply |
| *P*To*Q*Function | *p* | *q* | applyAs*Q* |
| To*P*Function&lt;T&gt; | T | *p* | applyAs*P* |
| To*P*BiFunction&lt;T, U&gt; |T, U | *p* | applyAsP |
| *P*UnaryOperator | *p* | *p* | applyAs*P* |
| *P*BinaryOperator | *p*, *p* | *p* | applyAs*P* |
| *P*Predicate | *p* | boolean | test |

*p*, *q* is int, long, double
*P*, *Q* is Int, Long, Double

Supply your own functional interface. `(int, int, Color) -> Color`
```Java
@FunctionalInterface
public interface ColorTransformer {
  Color apply(int x, int y, Color colorAtXY);
}
```
`apply` is used for majority of standard functional interfaces. 
