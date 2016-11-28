title: 'Java SE 8 For the Really Impatient, Note 13'
date: 2016-04-04 15:34:48
categories:
  - Coding
tags:
  - Java
  - Notes
---

# Chapter 8 Miscellaneous Goodies

## Working with Files

Java 8 brings a small number of convenience methods that use stream for reading lines from files and for visiting directory entries. Also, there is an official way of performing Base64 encoding. 

### Streams of Lines

`Files.lines`: read the lines of a file lazily. It yields a stream of strings, one per line of input:
```Java
Stream<String> lines = Files.lines(path);
Optional<String> passwordEntry = lines.filter(s -> s.contains(“password”)).findFirst();
```
As soon as the first line containing password is found, no further lines are read from the underlying file. 

`Files.lines` defaults to UTF-8, unlike `FileReader` which opens files in local character encoding. You can specify other encodings by supplying a `Charset` argument. 

The `Stream` interface extends `AutoCloseable`. The `Files.lines` method produces a stream whose `close` method closes the file. The easiest way to make sure the file in indeed closed is to use a Java 7 try-with-resources block:
```Java
try (Stream<String> lines = Files.lines(path)) {
  Optional<String> passwordEntry = lines.filter(s -> s.contains("password")).findFirst();
  ...
} // The file will be closed here
```

When a stream spawns another, the `close` methods are chained. 
```Java
try (Stream<String> filteredLines = Files.lines(path).filter(s -> s.contains("password"))) {
  Optional<String> passwordEntry = filteredLines.findFirst();
  ...
}
```
When filteredLines is closed, it closes the underlying stream, which closes the underlying file. 

Attach an `onClose` handler to be notified when the stream is closed. 
```Java
try (Stream<String> filteredLines = Files.lines(path).onClose(() -> System.out.println("Closing")).filter(s -> s.contains("password"))) {
  ...
}
```

If an `IOException` occurs as the stream fetches the lines, that exception is wrapped into an `UncheckedIOException` which is thrown out of the stream operation. 

If you want to read lines from a source other than a file, use the `BufferedReader.lines` method instead:
```Java
try (BufferedReader reader = new BufferedReader(new InputStreamReader(url.openStream()))) {
  Stream<String> lines = reader.lines();
  ...
}
```
With this method, closing the resulting stream does not close the reader. So you must place the `BufferedReader` object, and not the stream object, into the header of the `try` statement.

### Streams of Directory Entries

`Files.list`: returns a `Stream<Path>` that reads the entries of a directory. The directory is read lazily, making it possible to efficiently process directories with huge numbers of entries. 
```Java
try (Stream<Path> entries = Files.list(pathToDirectory)) {
  ...
}
```

The `list` method does not enter subdirectories. 

`Files.walk`: processes all descendants of a directory. 
```Java
try (Stream<Path> entries = Files.walk(pathToRoot)) {
  // Contains all descendants, visited in depth-first order 
}
```

You can limit the depth of the tree by calling `Files.walk(pathToRoot, depth)`. Both `walk` methods have a varargs parameter of type `FileVistOption...`, but there is currently only one option you can supply: `FOLLOW_LINKS` to follow symbolic links. 

If you filter the paths returned by `walk` and your filter criterion involves the file attributes stored with a directory, such as size, creation time, or type (file, directory, symbolic link), then use the `find` method instead. Call it with a predicate function that accepts a path and a `BasicFileAttributes` object. The only advantage is efficiency, since the directory is being read anyway, the attributes are readily available. 

### Base64 Encoding

The Base64 encoding encodes a sequence of bytes into a (longer) sequence of printable ASCII characters. Java 8 provides a standard encoder and decoder. 

Normally, an encoded string has no line breaks, but the MIME standard used for email requires a "\r\n" every 76 characters. 

For encoding, request a `Base64.Encoder` with one of the static methods `getEncoder`, `getUrlEncoder`, or `getMimeEncoder` of the `Base64` class. 

That class has methods to encode an array of bytes or a NIO `ByteBuffer`. 
```Java
Base64.Encoder encoder = Base64.getEncoder();
String original = username + ":" + password;
String encoded = encoder.encodeToString(original,getBytes(StandardCharsets.UTF-8));
```

Alternatively, you can "wrap" an output stream, so that all data sent to it is automatically encoded. 
```Java
Path originalPath = ..., encodedPath = ...;
Base64.Encoder encoder = Base64.getMimeEncoder();
try (OutputStream output = Files.newOutputStream(encodedPath)) {
  Files.copy(originalPath, encoder.wrap(output));
}
```

To decode, reverse the operations:
```Java
Path encodedPath = ..., decodedPath = ...;
Base64.Decoder decoder = Base64.getMimeDecoder();
try (InputStream input = Files.newInputStream(encodedPath)) {
  Files.copy(decoder.wrap(input), decodedPath);
}
```

## Annotations

Annotations are tags inserted into the source code that some tools can process. 

Java 8 has two enhancements to annotaion processing: repeated annotations and type use annotations. Moreover, reflection has been enhanced to report method parameter names. This has the potential to simplify annotations on method parameters. 

### Repeated Annotations

When annotations were first created, they were envisioned to mark methods and fields for processing. 
```Java
// Call after construction
@PostConstruct public void fetchData() { ... }

// Inject resource here
@Resource("jdbc:derby:sample")
private Connection conn;
```
In this context, it made no sense to apply same annotation twice. You can't inject a field in two ways. Different annotations on the same element are fine and quite common:
```Java
@Stateless @Path("/service")
public class Service { ... }
```

Soon, more and more uses for annotations emerged, leading to situations where one would have liked to repeat the same annotation. E.g.,
```Java
@Entity
@PrimaryKeyJoinColumn(name="ID")
@PrimaryKeyJoinColumn(name="REGION")
public class Item { ... }
```
Since that wasn't possible, the annotations were packed into a container annotation, like this:
```Java
@Entity
@PrimaryKeyJoinColumns({
  @PrimaryKeyJoinColumn(name="ID")
  @PrimaryKeyJoinColumn(name="REGION")
})
public class Item { ... }
```

This is no longer necessary in Java 8. If your framework provider has enabled repeated annotations, you can just use them. 

For a framework implementor, the `AnnotatedElement` interface has a method that gets the annotation of type T, if present. 
```Java
public <T extends Annotation> T getAnnotation(Class<T> annotationClass)
```

What should the method do if multiple annotations of the same type are present? To solve this problem, the inventor of a repeatable annotation must
1. Annotate the annotation as `@Repeatable`
2. Provide a container annotation 

E.g., for a simple unit testing framework, we might define a repeatable `@TestCase` annotation, to be used like this:
```Java
@TestCase(params="4", expected="24")
@TestCase(params="0", expected="1")
public static long factorial(int n) { ... }
```
Here is how annotation can be defined:
```Java
@Repeatable(TestCases.class)
@interface TestCase {
  String params();
  String expected();
}

@interface TestCases {
  TestCase[] value();
}
```
Whenever the user supplies two or more `@TestCase` annotations, they are automatically wrapped into a `@TestCases` annotation. 

When annotation processing code calls `element.getAnnotation(TestCase.class)` on the element representing the `factorial` method, null is returned. This is becasue the element is actually annotated with the container annotation `TestCases`. 

When implementing an annotation processor for your repeatable annotation, you will find it simpler to use the `getAnnotationsByType` method. The call `element.getAnnotationsByType(TestCase.class)` "looks through" any `TestCases` container and gives you an array of `TestCase` annotations. 

### Type Use Annotations

Prior to Java 8, an annotation was applied to a declaration. A declaration is a part of code the introduces a new name. 
```Java
@Entity public class Person { ... }

@SuppressWarnings("unchecked") List<Person> people = query.getResultList();
```

In Java 8, you can annotate any type use. This can be useful in combination with tools that check for common programming errors. Suppose you annotated variables that you never want to be null as `@NonNull`. A tool can check that the following is correct:
```Java
private @NonNull List<String> names = new ArrayList<>();
...
names.add("Fred"); // No possibility of a NullPointerException
```

The tool should detect any statement that might cause `names` to be null. 
```Java
names = null; // Null checker flags this as an error
names = readNames(); // OK if readNames returns a @NonNull String
```

The null checker in the Checker Framework assumes that any nonlocal variables are implicitly `@NonNull`, but that local variables might be null unless the code shows otherwise. If a method may return a null, it needs to be annotated as `@Nullable`. 

How can one express that the list _elements_ should be non-null? 
```Java
private List<@NonNull String> names;
```
It is this kind of annotation that was not possible before Java 8 but has now become legal. 

Type use annotations can appear in the following places:
* With generic type arguments: `List<@NonNull String>`, `Comparator.<@NonNull String>reverseOrder()`
* In any position of an array: `@NonNull String[][] words`(words[i][j] is not null), `String @NonNull [][] words`(words is not null), `String[] @NonNull [] words`(words[i] is not null)
* With superclasses and implemented interfaces: `class Image implements @Rectangular Shape`
* With constructor invocations: `new @Path String("/usr/bin")`
* With casts and `instanceOf` checks: `(@Path String) input`, `if (input instanceOf @Path String)`. (The annotations are only for use by external tools. They have no effect on the behavior of a cast or an `instanceOf` check.)
* With exception specifications: `public Person read() throws @Localized IOException`
* With wildcards and type bounds: `List<@ReadOnly ? extends Person>`, `List<? extends @ReadOnly> Person`
* With method and constructor references: `@Immutable Person::getName`

There are a few type positions that cannot be annotated:
```Java
@NonNull String.class // cannot annotate class literal
import java.lang.@NonNull String; // cannot annotate import
```

It is also impossible to annotate an annotation. 

More for extended type checking can be found at Checker Framework tutorial. 

### Method Parameter Reflection

The names of parameters are now available through reflection. Consider a typical JAX-RS method:
```Java
Person getEmployee(@PathParam("dept") Long dept, @QueryParam("id") Long id)
```
In almost all cases, the parameter names are the same as the annotation arguments, or they can be made to be the same. If the annotation processor could read the parameter names, then one could simply write
```Java
Person getEmployee(@PathParam Long dept, @QueryParam Long id)
```

This is possible in Java 8, with the new class `java.lang.relect.Parameter`. 

Unfortuantely, for the necessary information to appear in the classfile, the source must be compiled as `javac -parameters SourceFile.java`. 

## Miscellaneous Minor Changes

### Null Checks

The `Objects` class has static predicate methods `isNull` and `nonNull` that can be useful for streams. 
```Java
// checks whether a stream contains a null
stream.anyMatch(Objects::isNull);

// filter null
stream.filter(Objects::nonNull);
```

### Lazy Messages

The `log`, `logp`, `severe`, `warning`, `info`, `config`, `fine`, `finer`, and `finest` methods of `java.util.Logger` class now support lazily constructed messages. 
```Java
logger.finest("x: " + x + ", y: " + y);
```
The message string is formatted even when the logging level is such that it would never be used. Instead, use
```Java
logger.finest(() -> "x: " + x + ", y: " + y);
```
Now the lambda expression is only evaluated at the `FINEST` logging level, when the cost of the lambda invocation is presumably the least of one's problems. 

The `requireNonNull` of the `Objects` class also has a version that computes the message string lazily. 
```Java
this.directions = Objects.requireNonNull(directions, () -> "directions for " + this.goal + " must not be null");
```
In the common case that `directions` is not null, `this.directions` is simply set to `directions`. If `directions` is null, the lambda is invoked, and a `NullPointerException` is thrown whose message is the returned string. 

### Regular Expressions

Java 7 introduced named capturing groups.
```Java
(?<city>[\p{L} ]+),\s*(?<state>[A-Z]{2})
```

In Java 8, you can use the names in the `start`, `end`, and `group` methods of `Matcher`:
```Java
Matcher matcher = pattern.matcher(input);
if (matcher.matches()) {
  String city = matcher.group("city");
  ...
}
```

The `Pattern` class has a `splitAsStream` method that splits a `CharSequence` along a regular expression. 
```Java
String contents = new String(Files.readAllBytes(path), StandardCharsets.UTF_8);
Stream<String> words = Pattern.compile("[\\P{L}]+").splitAsStream(contents);
```

The method `asPredicate` can be used to filter strings that match a regular expression:
```Java
Stream<String> acronyms = words.filter(Pattern.compile("[A-Z]{2,}").asPredicate());
```

### Locales

A locale specifies everything you need to know to present information to a user with local preferences concerning language, date formats, and so on. 

A locale is composed of up to five components:
1. A language, specified by two or three lowercase letters
2. A script, specified by four letters with an intial uppercase
3. A country, specified by two uppercase letters or three digits
4. Optionally, a variant 
5. Optionally, an extension. Extensions describe local preferences for calendars, numbers, and so on

Since Java 7 you can simply call `Locale.forLanguageTag("en-US")`. Java 8 adds methods for finding locales that match user needs. 

A _language range_ is a string that denotes the locale characteristics that a user desires, with * for wildcards. One can optionally specify a weight between 0 and 1 when constructing a `Locale.LanguageRange`. 

Given a list of weighted language ranges and a collection of locales, the `filter` method produces a list of matching locales, in descending order of match quality. 
```Java
// A list containing the Locale.LanguageRange objects for the given strings
List<Locale.LanguageRange> ranges = Stream.of("de", "*-CH")
  .map(Locale.LanguageRange::new)
  .collect(Collectors.toList());
  
List<Locale> matches = Locale.filter(ranges, Arrays.asList(Locale.getAvailableLocales()));
// matching de, de-CH, de-AT, de-LU, de-DE, de-GR, fr-CH, it_CH
```

`lookup`: finds the best locale. 
```Java
Locale bestMatch = Locale.lookup(ranges, locales);
// matching de
```

### JDBC

In Java 8, JDBC has been updated to version 4.2.

The `Date`, `Time`, and `Timestamp` classes in the `java.sql` package have methods to convert from and to their `java.time` analogs `LocalDate`, `LocalTime`, and `LocalDateTime`. 

The `Statement` class has a method `executeLargeUpdate` for executing an update whose row count exceeds Integer.MAX_VALUE. 

JDBC 4.1 specified a generic method `getObject(column, type)` for `Statement` and `ResultSet`, where `type` is a `Class` instance. E.g., `URL url = result.getObject("link", URL.class)` retrieves a `DATALINK` as a `URL`. Now the corresponding `setObject` method is provided as well. 
