title: 'Java Garbage Collection'
tags:
  - Java
  - 技术
id: 1023
categories:
  - Java
  - 技术实践
date: 2014-02-04 10:51:31
---

## 
	<span style="color:#000000;">Basics:</span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Few important points about garbage collection in java:</span></span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">**Objects are created on heap** in Java&nbsp;irrespective of there scope e.g. local or member variable. While it&#39;s worth noting that class variables or static members are created in method area of Java memory space and both heap and method area is shared between different thread.</span></span>

2.  <span style="color:#000000;"><span style="font-size:14px;">Garbage collection is a mechanism provided by Java Virtual Machine to **reclaim heap space** from objects which are eligible for Garbage collection.</span></span>

3.  <span style="color:#000000;"><span style="font-size:14px;">Garbage collection **relieves** java programmer from **memory management** which is essential part of C++ programming and gives more time to focus on business logic.</span></span>

4.  <span style="color:#000000;"><span style="font-size:14px;">Garbage collection in Java is carried by a **daemon thread** called **Garbage collector**.</span></span>

5.  <span style="color:#000000;"><span style="font-size:14px;">Before removing an object from memory,&nbsp;Garbage collection thread **invokes finalize () method **of that object and gives an opportunity to perform any sort of cleanup required.</span></span>

6.  <span style="color:#000000;"><span style="font-size:14px;">You as Java programmer **can not force Garbage collection** in Java; it will only **trigger** if JVM thinks it **needs** a garbage collection based on Java **heap size**.</span></span>

7.  <span style="color:#000000;"><span style="font-size:14px;">There are methods like **System.gc()** and **Runtime.gc()** which is used to **send request** of Garbage collection to JVM but it&rsquo;s **not guaranteed** that garbage collection will happen.</span></span>

8.  <span style="color:#000000;"><span style="font-size:14px;">If there is no memory space for creating new object in Heap Java Virtual Machine throws **OutOfMemoryError **or **java.lang.OutOfMemoryError heap space**.</span></span>

## 
	<span style="color: rgb(0, 0, 0); line-height: 1.6em;">​Questions:</span>

	<span style="color:#000000;"><span style="font-size:14px;">Q1:&nbsp;<span style="line-height: 1.6em;">When an Object becomes Eligible for Garbage Collection?</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">A:&nbsp;An Object becomes eligible for Garbage collection or GC if its** not reachable from any live threads or any static references**. In other words,&nbsp;</span>if i**ts all references are null**.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">**Cyclic dependencies** are not counted as reference.&nbsp;So if object A has reference of object B and object B has reference of object A, and they don&#39;t have any other live reference then both objects A and B will be eligible for Garbage collection.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Some cases:</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">All references of that object **explicitly set to null** e.g. object = null.</span></span>

2.  <span style="color:#000000;"><span style="font-size:14px;">Object is created inside a block and reference **goes out scope** once control exit that block.</span></span>

3.  <span style="color:#000000;"><span style="font-size:14px;">**Parent object set to null**. If an object holds reference of another object and when you set container object&#39;s reference null, child or contained object automatically becomes eligible for garbage collection.</span></span>

4.  <span style="color:#000000;"><span style="font-size:14px;">If an object has only live references via **WeakHashMap** it will be eligible for garbage collection.(Strong &amp; Weak reference)</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q2: How do you identify minor and major garbage collection in Java?</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A: Minor collection prints &ldquo;**GC**&rdquo; if garbage collection logging is enable using&nbsp;-**verbose:gc** or **-XX:PrintGCDetails**, while Major collection prints &ldquo;**Full** **GC**&rdquo;.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q3:&nbsp;<span style="line-height: 1.6em;">What is difference between ParNew and DefNew Young Generation Garbage collector?</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A: ParNew and DefNew are two young generation garbage collector. ParNew is a **multi-threaded GC** used along with **concurrent Mark Sweep** while DefNew is **single threaded GC** used along with** Serial Garbage Collector**.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q4:&nbsp;<span style="line-height: 1.6em;">How do you find GC resulted due to calling System.gc()?</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;<span style="line-height: 1.6em;">There will be a word &ldquo;**System**&rdquo; included in Garbage collection **output**.</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q5:&nbsp;<span style="line-height: 1.6em;">What is finalize method in Java? When does Garbage collector calls finalize method in Java ?</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;<span style="line-height: 1.6em;">Finalize method in Java, also called **finalizer,** is a method defined in java.lang.Object and **called by Garbage collector **before collecting any object which is eligible for GC. Finalize() method provides last chance to object to** do cleanup** and **free any remaining resource**.</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Q6:&nbsp;</span><span style="line-height: 1.6em;">Does Garbage collection occur in permanent generation space in JVM?</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">A:&nbsp;</span><span style="line-height: 1.6em;">Garbage Collection does occur **in PermGen space** and if PermGen space is full or cross a threshold, it can trigger Full GC. If you look at output of GC you will find that PermGen space is also garbage collected. This is why correct sizing of PermGen space is important to avoid frequent full GC. You can control size of PermGen space by JVM options **-XX:PermGenSize** and **-XX:MaxPermGenSize**.</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Q7:&nbsp;</span>&nbsp;How to monitor garbage collection activities?</span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">A:&nbsp;</span><span style="line-height: 1.6em;">Enable GC options like **-XX:PrintGCDetails** **-X:verboseGC** and **-XX:PrintGCTimeStamps** as it doesn&#39;t impact application performance much but provide useful states for performance monitoring.</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q8:&nbsp;Garbage Collector is controlled by whom?</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;The **JVM** controls the Garbage Collector. It decides when to run the Garbage Collector. JVM runs the Garbage Collector when it realizes that the memory is running low, but this behavior of jvm can not be guaranteed.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q9:&nbsp;What is the purpose of overriding finalize() method?</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;The finalize() method should be overridden for an object to** include the clean up code **or to **dispose of the system resources** that should to be done before the object is garbage collected.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q10:&nbsp;What happens if an uncaught exception is thrown from during the execution of the finalize() method of an object?&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;The **exception** will be **ignored** and the **garbage** **collection** (finalization) of that object **terminates**.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Q11:&nbsp;How to enable/disable call of finalize() method of exit of the application?</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">A:&nbsp;**Runtime.getRuntime().runFinalizersOnExit(boolean value)**. Passing the boolean value will either disable or enable the finalize() call.</span></span>