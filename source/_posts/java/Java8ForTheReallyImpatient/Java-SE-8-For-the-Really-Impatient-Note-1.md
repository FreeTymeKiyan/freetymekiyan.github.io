title: 'Java SE 8 For the Really Impatient, Note 1'
date: 2016-03-15 15:12:44
categories:
  - Coding
tags:
  - Java
  - Notes
---
  
# Chapter 1 Lambda Expressions

## Why

A block of code was passed to someone - a thread pool, a sort method, or a button. The code was called at some later time. How to work with blocks of code directly in Java? Lambda expression. 

Use `Integer.compare(x, y)` instead of `x - y` to compare x and y since that computation can overflow. 

## Syntax

Java is a strong typed language, we must specify type. 
parameters, the -> arrow, and an expression
  
```Java
(String first, String second)
  -> Integer.compare(first.length(), second.length())
```

If it doesn't fit in a single expression, enclose in {}
```Java
(String first, String second) -> {
  if (first.length() < second.length()) return -1;
  else if (first.length() > second.length()) return 1;
  else return 0;
}
```

No parameters, still supply empty parentheses
```Java
() -> { for (int i = 0; i < 1000; i++) doWork(); }
```

Parameter types of a lambda expression can be omitted if they can be inferred
```Java
Comparator<String> comp = (first, second) // same as (String first, String second)
  -> Interger.compare(first.length(), second.length())
```

If a method has a single parameter with inferred type, you can even omit the parentheses
```Java
EventHandler<ActionEvent> listener = event -> 
  System.out.println("Thanks for clicking!")
```
Instead of `(event) ->` or `(ActionEvent event) ->`

Can add annotations or final mofifier
`(@NonNull String name) -> ...`
`(final String name) -> ...`

Never specify the result type of a lambda expression. It's always inferred from context. 

It's illegal for a lambda expression to return a value in some branches but not in others. 

## Functional Interfaces

An object of an interface with a single abstract method. Such an interface is called a functional interface. Such as Runnable, Comparator...

```Java
Arrays.sort(words, (first, second) -> Integer.compare(first.length(), second.length()))
```
`Arrays.sort` method receives an object of some class that implements `Comparator<String>`. Invoking the compare method on that object executes the body of the lambda expression. 

More efficient, easier to read, short and simple

```Java
button.setOnAction(event ->
  System.out.println("Thanks for clicking!"));
```

You cannot assign lambda to an object. Only thing you can do is conversion to functional interface. 

There are generic functional interfaces in `java.util.function` package. 
`BiFunction<T, U, R>` describes functions with parameter types `T` and `U` and return type `R`. You can save lambda in a variable of that type.

```Java
BiFunction<String, String, Integer> comp = (first, second) -> 
  Integer.compare(first.length(), second.length())
```
Cannot use in Arrays.sort though.

Tag any functional interface with `@FunctionalInterface` annotation. 
Compiler will check that the annotated entity is an interface with a single abstract method. 
And the Javadoc page includes a statement that your interface is a functional interface. 

If the body of a lambda expression may throw a checked exception, that exception needs to be declared in the abstract method of the target interface. 

```Java
Runnable sleeper = () -> {
  System.out.println("Zzz");
  Thread.sleep(1000); // Error: Thread.sleep can throw a checked InterruptedException
}
```
`Runnable.run` cannot throw any exception. How to fix?
1. catch the exception in the body of the lambda expression
2. assign the lambda expression to an interface whose single abstract method can throw the exception. e.g, the `call` method of the `Callable` interface

```Java
Callable<Void> call = () -> {
  System.out.println("Zzz");
  Thread.sleep(1000); 
  return null; // add this statement
}
```

## Method References

When there is already a method that carries out exactly the action that you'd like to pass on to some other code. 

```Java
button.setOnAction(System.out::println)// just pass the println method to the setOnAction method
```
Equivalent to `x -> System.out.println(x)`

```Java
Arrays.sort(strings, String::compareToIgnoreCase)
```

The `::` operator separates the method name from the name of an object or class
* object::instanceMethod
* Class::staticMethod
* Class::instanceMethod

The first two cases: the method reference is equivalent to a lambda expression that supplies the parameters of the method.

The third case: the first parameter becomes the target of the method. `String::compareToIgnoreCase` is the same as `(x, y) -> x.compareToIgnoreCase(y)`

Invoke a method of an enclosing class or its superclass.
`this::equals` is the same as `x -> this.equals(x)`
`super::instanceMethod` uses this as the target and invokes the superclass version of the given method. 

```Java
class Greeter {
  public void greet() {
    System.out.printlin("Hello, world!");
  }
}

class ConcurrentGreeter extends Greeter {
  public void greet() {
    Thread t = new Thread(super::greet);
    t.start();
  }
}
```

In an inner class, capture the this reference of an enclosing class as `EnclosingClass.this::method` or `EnclosingClass.super::method`

## Constructor References

Just like method references, except that the name of method is new. 
`Button::new` is a reference to a Button constructor. 

Which constructor depends on the context. 

You can form constructor reference with array types. 
`int[]::new` is a constructor references with one parameter, the length of the array. 

Obtain an array of the correct type instead of object
```Java
Button[] buttons = stream.toArray(Button[]::new)
```

## Variable Scope

A lambda expression has 3 ingredients:
1. A block of code
2. Parameters
3. Values for the free variables, that is, the variables that are not parameters and not defined inside the code

These values have been _captured_ by lambda expression. Technical term is **closure**. 
A lambda expression can capture the value of a variable in the enclosing scope. 

In lambda expression, you can only reference variables whose value does not change. 
```Java
public static void repeatMessage(String text, int count) {
  Runnable r = () -> {
    while (count > 0) {
      count--; // Error: cannot mutate captured variable
      System.out.println(text);
      Thread.yield();
    }
  };
  new Thread(r).start();
}
```
Mutating variables in a lambda expression is not threadsafe. 

Trick to update a count in an enclosing local scope in lambda expression
```Java
int[] counter = new int[1];
button.setOnAction(event -> counter[0]++);
```
Still not threadsafe. Think twice before using this trick.

Cannot use same variable name as local variables. 

`this` in lambda expression refers to the `this` parameter of the method that creates the lambda. 
```Java
public class Application {
  public void doWork() {
    Runnable runner = () -> { ...;
      System.out.println(this.toString()); ... };
    ...
  }
}
```
`this.toString()` calls the toString method of the Application object, not the runnable instance. 
The scope of the lambda expression is nested inside the doWork method, and this has the same meaning anywhere in that method. 

## Default Methods

In Java 8, the forEach method has been added to the Iterable interface, a superinterface of Collection. 

```Java
interface Person {
  long getId();
  default String getName() { return "John Q. Public"; }
}
```
A concrete class that implements the Person interface must provide an implementation of getId
but it can choose to keep the implementation of getName or to override it. 

Rules when conflict: 

1. class wins
2. provide implementation if at least one has implemented the method
  
## Static Methods in Interfaces

Do not need static methods in a companion class anymore. 

Comparator class has a very useful static comparing method that accepts a "key extraction" function and yields a comparator that compares the extracted keys. 
To compare Person object by name
```Java
Comparator.comparing(Person::getName)
```
The same as `(x, y) -> x.compareToIgnoreCase(y)`

```Java
Comparator.comparing(String::length) 
```
The same as `(first, second) -> Integer.compare(first.length(), second.length())`
