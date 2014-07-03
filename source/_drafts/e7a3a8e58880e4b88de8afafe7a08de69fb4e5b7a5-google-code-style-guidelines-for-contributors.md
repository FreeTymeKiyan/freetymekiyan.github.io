title: '磨刀不误砍柴工——Google Code Style Guidelines for Contributors'
tags:
  - FACK-Android
  - Java
  - 代码
  - 高质量
id: 465
categories:
  - Android
  - 代码管理
  - 技术实践
date: 2013-02-06 20:46:16
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">Google Code Style Guidelines for Contributors是谷歌对代码投稿人的建议，是一种代码风格的推荐，也是代码规范的一种补充。这份个、g<span style="line-height: 1.6em;">uideline里面有很多实用的建议。我尽力把它们都翻译出来了，以便加深理解。</span></span></span>

<div>

## 
		Code Style Guidelines for Contributors

### 
		<span style="color:#000000;">一、Java语言规则</span>

		<span style="color:#000000;"><span style="font-size:14px;">我们遵循标准的Java编码惯例。我们加了一些规则：</span></span>

#### 
		<span style="color:#000000;"><span style="line-height: 1.6em;">不要忽略异常</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">写代码的时候像这样完全忽略异常很有诱惑力</span></span>

	<div>
		<pre class="brush:java;">
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}</pre>

			<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">你千万别这样做。当你可能认为你的代码绝不会遇到这个错误的情况或处理它不重要的时候，像上面这样忽略异常会在你的代码里面埋下地雷，某天某个人就会踩到&hellip;你必须用某种原则处理代码里的每个异常。特定的处理方式根据情况有所不同。</span></span></span>

			&nbsp;

	</div>

	> <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">任何时候某个人写了一个空的catch语句心里都该有惶恐的感觉。肯定会有这样做是对的的情况，但是你至少得先想想。在Java里你不能逃避这种惶恐的感觉。 - James Gosling</span></span></span>

		&nbsp;

		<span style="color:#000000;"><span style="font-size:14px;">可以接受的替代方案（按倾向排序）有：</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">1.在方法调用处抛出异常</span></span>

	<div>
		<pre class="brush:java;">
void setServerPort(String value) throws NumberFormatException {
    serverPort = Integer.parseInt(value);
}</pre>

			<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">2.根据你的抽象级别抛出一个新的异常</span></span></span>

			&nbsp;

	</div>

	<div>
		<pre class="brush:java;">
void setServerPort(String value) throws ConfigurationException {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        throw new ConfigurationException(&quot;Port &quot; + value + &quot; is not valid.&quot;);
    }
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">3.优雅地处理错误的情况并在catch区块里用一个合适的值替代</span></span>

	<div>
		<pre class="brush:java;">
/** Set port. If value is not a valid number, 80 is substituted. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        serverPort = 80;  // default port for server 
    }
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">4.捕捉异常再抛出一个RuntimeException。这种做法很危险，当且仅当你肯定这个错误发生的时候，程序崩溃是最合适的做法。</span></span>

	<div>
		<pre class="brush:java;">
/** Set port. If value is not a valid number, die. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        throw new RuntimeException(&quot;port &quot; + value &quot; is invalid, &quot;, e);
    }
}</pre>

			&nbsp;

	</div>

		<span style="font-size:14px;"><span style="color:#000000;">注意：原本的异常被传给了RuntimeException的构造器。如果你的代码要在Java 1.3版本下编译，你需要忽略作为原因的异常。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">5.最后一个手段：如果你很自信地认为其实忽略这个异常才是恰当的，那么你可以忽略它，但是你也必须在注释中写个好理由。</span></span>

	<div>
		<pre class="brush:java;">
/** If value is not a valid number, original port number is used. */
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        // Method is documented to just ignore invalid user input.
        // serverPort will just be unchanged.
    }
}</pre>

#### 
			<span style="color:#000000;"><span style="line-height: 1.6em;">不要捕捉通用的异常</span></span>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">有时候犯下懒这样捕捉异常很诱惑人</span></span>

	<div>
		<pre class="brush:java;">
try {
    someComplicatedIOFunction();        // may throw IOException 
    someComplicatedParsingFunction();   // may throw ParsingException 
    someComplicatedSecurityFunction();  // may throw SecurityException 
    // phew, made it all the way 
} catch (Exception e) {                 // I&#39;ll just catch all exceptions 
    handleError();                      // with one generic handler!
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">你不该这么做。捕捉通用的异常和异常类在几乎所有情况下都不合适，异常类更不适合，因为它里面也包含了错误的异常。这种做法非常危险。这代表你意料不到的异常（包括像ClassCastException（类转换异常）一样的运行异常）会在程序级的错误处理中被捕捉。这让你代码的故障处理属性变得难以理解。也就是说当有人在你调用的代码中添加了一种新的异常，编译器不会帮你意识到你需要用不同地方式处理那个错误。不管怎样，在大多数情况下，你不该用同种方式处理不同类型的异常。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">这条规则只有少数几个例外的情况：在你想要捕捉所有类型的错误的特定测试代码和顶层代码里（为防止它们在UI中冒出来，或保持批处理作业的运行）。这种情况下，你可以捕捉通用的异常（或异常类）并适当地处理错误。不过，在这么做之前你可得想清楚了，并在注释中解释为什么在这里这么写是安全的。</span></span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">捕捉通用异常的替代方案：</span></span></span>

	<div>

*   <span style="color:#000000;"><span style="font-size:14px;">捕捉单个try之后写多个catch单独捕捉每个异常。这么做有些笨拙，但还是比捕捉所有异常的方法更好。注意不要在catch语句中重复太多的代码。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">用多重try语句重构你的代码让错误处理更加精细。把输入输出操作和解析分开，在各自情况下处理错误。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">重新抛出异常。很多情况下你并不需要在这个层级捕捉异常，让方法去抛出它吧。</span></span>
	</div>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">请记住：异常是人类的好朋友！当编译器抱怨你没在捕捉某个异常的时候，别皱眉头，笑一笑，编译器只是让你捕捉代码中运行期的问题更简单了~</span>

		<span style="color:#000000;"><span style="line-height: 1.2em;">不要使用Finailizer（终结器）</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">终结器是一种当对象被垃圾回收时执行一大块代码的方式。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">好处：做清理很方便，特别是外部资源</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">坏处：终结器什么时候被调用没有保证，甚至会不会被调用都难说</span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">结论：不使用终结器。在多数情况下， 你可以借终结器做你想做的事，很好地处理异常。如果你确实需用它，定义个close()方法（或类似的）并在方法需要被调用的时候准确的写文档。比如说InputStream。这种情况下是合适的、但强求在终结器里面打印一个简短的log信息，只要它不会涌出一大片的log。</span></span></span>

#### 
		<span style="color: rgb(0, 0, 0); line-height: 1.2em;">完整的限制导入语句</span>

		<span style="color:#000000;"><span style="font-size:14px;">当你想用foo包里面的Bar这个类，有两种可能的方法导入它：</span></span>

	<pre class="brush:java;">
import foo.*;</pre>

		<span style="color:#000000;"><span style="font-size:14px;">优点：有可能降低导入语句的数量</span></span>

	<pre class="brush:java;">
import foo.Bar;</pre>

		<span style="color:#000000;"><span style="font-size:14px;">优点：让实际上用了哪个类很清楚。让代码对维护人员开说更易读。</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">结论：导入所有的Android代码都用第二种方式。Java的标准库（java.util.*, java.io.*, 等等）和单元测试代码（junit.framework.*）的导入可以例外。</span>

### 
		<span style="color: rgb(0, 0, 0); line-height: 1.6em;">二、Java库规则</span>

		<span style="color:#000000;"><span style="font-size:14px;">使用Android的Java库和工具有惯例。在某些情况下，惯例在某些重要的方面有改变，老代码可能使用弃用了的样式或库。当处理这些代码时，可以继续使用已有的样式（请看&nbsp;[一致性](#一致性)）。当创建新组件的时候不要使用弃用的库。</span></span>

### 
		<span style="color:#000000;">三、Java风格规范</span>

		<span style="color:#000000;"><span style="font-size:14px;">1.版权声明</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">每个文件顶部都要有一个版权声明。接着是文件在哪个包声明和导入哪些包的声明。每个部分用一个空行分开。然后才是类和接口的声明。在Javadoc中描述这个类或者接口是做什么的。</span></span>

	<pre class="brush:java;">
/*

* Copyright (C) 2010 The Android Open Source Project 
*
* Licensed under the Apache License, Version 2.0 (the &quot;License&quot;);
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at 
*
*      http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software 
* distributed under the License is distributed on an &quot;AS IS&quot; BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and 
* limitations under the License.
*/

package com.android.internal.foo;

import android.os.Blah;
import android.view.Yada;

import java.sql.ResultSet;
import java.sql.SQLException;

/**
* Does X and Y and provides an abstraction for Z.
*/

public class Foo {
    ...
}</pre>

		<span style="font-size:14px;"><span style="color:#000000;">你写的每个类和重要的public方法必须有至少一句描述类或方法做什么的Javadoc注释。最好以第三人称叙述性的动词开头。</span></span>

	<div>
		<pre class="brush:java;">
/** Returns the correctly rounded positive square root of a double value. */
static double sqrt(double a) {
    ...
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">或是</span></span>

	<div>
		<pre class="brush:java;">
/**
* Constructs a new String by converting the specified array of 
* bytes using the platform&#39;s default character encoding.
*/
public String(byte[] bytes) {
    ...
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">如果你给一个很小的get或set方法。比如setFoo()写的Javadoc注释就是sets foo的话，就不必写了。如果该方法还做了其他更复杂的事（比如强加了一个限制或有重要的副作用），那么你必须要写下来。还有，如果属性，这里是&quot;Foo&quot;，的意思不清楚，你也应该写下来。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">你写的每个方法，不论是public的或是其他的，都会因为写了Javadoc受益。public方法是API的一部分，所以要求写Javadoc。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">Android现在没有强制一种写Javadoc的明确风格。参考Sun公司的Javadoc惯例。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">2.方法写短一点</span></span></span>

		<span style="color:#000000;"><span style="font-size:14px;">只要是可行的，应该保证方法尽量小尽量专注个别功能。有些时候长方法也是合适的，所以对方法的长度没有强制限制。如果一个方法写了40多行，你就得考虑在不损害程序结构的情况下进行拆分了。</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">3.在标准的地方定义Field变量</span>

		<span style="color:#000000;"><span style="font-size:14px;">Field变量的标准定义地方有两个：程序的顶部，或是在使用它们的方法的正上方。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">4.限制变量权限</span></span></span>

		<span style="color:#000000;"><span style="font-size:14px;">本地变量的权限应该最小（Effective Java 第29条）。这样做可以增加代码的可读性和可维护性，并降低出错的可能。每个变量都该在使用它并包括它的所有使用场景的最里层代码区块定义。</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">本地变量应该第一次使用的地方声明。几乎所有的本地变量声明都得初始化。如果你明确初始化一个本地变量的信息还不够，你应该把声明放在你觉得够了的地方。</span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">这个规范有个例外的情况，就是try-catch。如果一个变量用某个会抛出检查过的exception的方法的返回值进行初始化，它应该放在try里面初始化。如果这个值必须要在try外面使用，它必须在try之前就声明，尽管这里它还不能被明确的初始化。</span></span></span>

	<div>
		<pre class="brush:java;">
// Instantiate class cl, which represents some sort of Set 
Set s = null;
try {
    s = (Set) cl.newInstance();
} catch(IllegalAccessException e) {
    throw new IllegalArgumentException(cl + &quot; not accessible&quot;);
} catch(InstantiationException e) {
    throw new IllegalArgumentException(cl + &quot; not instantiable&quot;);
}

// Exercise the set 
s.addAll(Arrays.asList(args));</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">但即使是这种情况也可以用封装try-catch到一个方法里面来避免</span></span>

	<div>
		<pre class="brush:java;">
Set createSet(Class cl) {
    // Instantiate class cl, which represents some sort of Set 
    try {
        return (Set) cl.newInstance();
    } catch(IllegalAccessException e) {
        throw new IllegalArgumentException(cl + &quot; not accessible&quot;);
    } catch(InstantiationException e) {
        throw new IllegalArgumentException(cl + &quot; not instantiable&quot;);
    }
}

...

// Exercise the set 
Set s = createSet(cl);
s.addAll(Arrays.asList(args));</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">循环使用的变量应该在循环里面声明，除非有其他的编译上的原因：</span></span>

	<div>
		<pre class="brush:java;">
for (int i = 0; i n; i++) {
    doSomething(i);
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">和</span></span>

	<div>
		<pre class="brush:java;">
for (Iterator i = c.iterator(); i.hasNext(); ) {
    doSomethingElse(i.next());
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">5.给导包的语句排序</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">导包语句的顺序是：</span></span>

1.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">导入android相关的包</span>
2.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">导入第三方的包</span><span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">导入</span>
3.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">Java和Javax的包</span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">​</span><span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">为了准确符合IDE的设置，这些导包语句应该：</span>

1.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">在每个组里面按字母顺序排列，大写字母在小写字母之前</span>
2.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">在每个组之间有空行（android, com, junit, net, org, java, javax）</span>

	<div>

			<span style="color:#000000;"><span style="font-size:14px;">本来在排序上是没有风格的要求的。这意味着要么IDE总是在改变顺序，要么IDE开发者不得不禁止自动导包管理的特性并手动维护这些导入语句。这就太不好了。</span></span>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">定下这个风格的原因是：</span></span>

	<div>

*   <span style="color:#000000;"><span style="font-size:14px;">最想看的包放在最上面</span></span>
*   <span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;">不怎么想看的包放在最下面</span></span></span></span>
*   <span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;">人们使用这个风格很简便</span></span></span></span></span></span>
*   <span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;">​<span style="color:#000000;"><span style="font-size:14px;">IDE能使用这个风格</span></span></span></span></span></span></span></span>

			<span style="color:#000000;"><span style="font-size:14px;">静态导入的使用和位置稍微有点争议。一些人可能喜欢静态导入语句散布在其他的导入语句之中，另一些可能更喜欢都放在其他导入语句的最上面或最下面。我们暂时还没有找到让所有IDE使用同样顺序的方法。</span></span>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">因为大部分人都觉得这个问题不那么重要，用你喜欢的方式并保持一致吧。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">6.缩进使用空格</span></span></span>

		<span style="color:#000000;"><span style="font-size: 14px; line-height: 1.6em;">代码区块使用4个空格。不使用tab。如果有疑问的话，和你周围的代码保持一致。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">自动换行用8个空格，包括函数的调用和赋值。比如说，下面这种是对的</span></span>

	<div>
		<pre class="brush:java;">
Instrument i =
        someLongExpression(that, wouldNotFit, on, one, line);</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">下面这种不对</span></span>

	<div>
		<pre class="brush:java;">
Instrument i =
    someLongExpression(that, wouldNotFit, on, one, line);</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">7.遵循Filed命名惯例</span></span></span>

	<div>

*   <span style="color:#000000;"><span style="font-size:14px;">非public，非静态field变量，命名以m开头（m是member的缩写）</span></span>
*   <span style="color:#000000;"><span style="font-size:14px;">静态field变量以s开头（static）</span></span>
*   <span style="color:#000000;"><span style="font-size:14px;">其他field变量以小写字母开头</span></span>
*   <span style="color:#000000;"><span style="font-size:14px;">public static final 变量（常量）命令全部大写+下划线</span></span>

			<span style="color:#000000;"><span style="font-size:14px;">例如</span></span>

			&nbsp;

	</div>

	<div>
		<pre class="brush:java;">
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">8.使用标准的花括号风格</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">花括号不要单独一行。它和前面的代码同一行</span></span>

	<div>
		<pre class="brush:java;">
class MyClass {
    int func() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">我们要求在判断语句中，花括号在代码语句的周围。除了整<span style="line-height: 1.6em;">个判断和判断语句一行就够了的情况以外。</span></span></span>

		<span style="color:#000000;"><span style="font-size:14px;">正确</span></span>

	<div>
		<pre class="brush:java;">
if (condition) {
    body(); 
}</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">正确</span></span>

	<div>
		<pre class="brush:java;">
if (condition) body();</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">错误</span></span>

	<div>
		<pre class="brush:java;">
if (condition)
    body();  // bad!</pre>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">9.限制每行的长度</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">代码的每行最多100个字符</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">例外：注释里面包含示例命令或URL，为了方便剪切和粘贴可以超过100个字符</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">例外：导包语句可以超过100个字符</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">10.使用标准的Java标注</span>

		<span style="color:#000000;"><span style="font-size:14px;">同一个语言元素中，标注应在修饰符的前面。简单的标记性的标注（比如@Override）可以和语言元素同行。如果有多个标注，或是参数化的标注，每个单独一行并按照字母顺序排序。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">Android中三种Java标注的标准用法是</span></span>

	<div>

*   <span style="color:#000000;"><span style="font-size:14px;">@Deprecated：每当标注了的元素不被推荐的时候都得使用@Deprecated标注。如果你使用@Deprecated标注，你必须同时使用@deprecated的Javadoc标签，并在里面指出一种替代的实现方法。另外得记住，一个又@Deprecated标注的方法应该还能用。如果你你看到有@deprecated Javadoc标签的老代码，请添加一个@Deprecated的标注。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">@Override：每当方法复写了父类的声明或实现方法时都得使用@Override标注。例如，如果你使用@inheritdocs的Javadoc标签，并从一个类（不是接口）里面派生出来，你也得用@Overrides标注这个方法复写了父类的方法。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">@SuppressWarnings：这个标注只应该在不可能去掉某个警告的情况下使用。如果某个警告符合这种情况，@SuppressWarnings必须被使用，以便保证所有的警告都能反映代码中的真实问题。当需要@SuppressWarnings标注时，必须在前面加上一个TODO的注释解释&quot;不可能去掉&quot;的状况。这样一般会找出一个接口很龊的讨厌的类。比如</span></span>

		<pre class="brush:java;">
// TODO: The third-party class com.third.useful.Utility.rotate() needs generics 

@SuppressWarnings(&quot;generic-cast&quot;)

List&lt;String&gt; blix = Utility.rotate(blax);</pre>

*   <span style="color:#000000;"><span style="font-size:14px;"><span style="font-family: sans-serif, Arial, Verdana, 'Trebuchet MS'; line-height: 1.6em;">当需要@SuppressWarnings标注时，代码应该被重构以便隔离这个标注适用的软件部分。</span></span></span>
	</div>

		<span style="color:#000000;"><span style="font-size:14px;">11.缩略词当做一般单词</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">在变量、方法和类命名的时候把缩略词和缩写当一般的单词对待</span></span>

	<div>
		<table>
			<thead>
				<tr>
					<th>

							Good

					</th>
					<th>

							Bad

					</th>
				</tr>
			</thead>
			<tbody>
				<tr>
					<td>

							XmlHttpRequest

					</td>
					<td>

							XMLHTTPRequest

					</td>
				</tr>
				<tr>
					<td>

							getCustomerId

					</td>
					<td>

							getCustomerID

					</td>
				</tr>
				<tr>
					<td>

							class Html

					</td>
					<td>

							class HTML

					</td>
				</tr>
				<tr>
					<td>

							String url

					</td>
					<td>

							String URL

					</td>
				</tr>
				<tr>
					<td>

							long id

					</td>
					<td>

							long ID

					</td>
				</tr>
			</tbody>
		</table>

			<span style="color:#000000;"><span style="font-size:14px;">12.使用TODO注释</span></span>

			&nbsp;

	</div>

		<span style="color:#000000;"><span style="font-size:14px;">如果代码是暂时的、短期的解决方案或够好但不完美的，使用TODO注释</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">TODO注释包括TODO字符串和一个冒号</span></span>

	<div>
		<pre class="brush:java;">
// TODO: Remove this code after the UrlTable2 has been checked in.</pre>

			<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">和</span></span></span>

			&nbsp;

	</div>

	<div>
		<pre class="brush:java;">
// TODO: Change this to use a flag instead of a constant.</pre>

			<span style="color:#000000;"><span style="font-size:14px;">明确一个时间。</span></span>

			&nbsp;

	</div>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">13.少量的输出Log</span>

		<span style="color:#000000;"><span style="font-size:14px;">尽管输出log很有必要，但它对性能影响很大，并且如果它不能保持适当的简洁的话，它就没有任何用处了。相关工具提供了5种不同级别的输出log方法。下面是这五种不同的级别和它们该在什么时候怎么用：</span></span>

	<div>

*   <span style="font-size:14px;"><span style="color:#000000;">ERROR：这个级别的日志在某些致命的情况下才用，换句话说，会有用户可见的后果并除非是外部删掉某些数据，或卸载程序，或清除数据分区，或重新刷机（或更糟糕的）等等无法恢复的情况。这个级别总会被记录日志。在ERROR级别纠正一些日志的问题被报告给数据收集的服务器是非常好的候选方案。</span></span>

*   <span style="font-size:14px;"><span style="color:#000000;">WARNING：这个级别的日志在有严重情况或意外情况的时候才用到，换句话说，某些有用户可见结果，没有因外部操作带来数据损失也能恢复的情况，包括等待或重启某个应用一直到重新下一个程序的新版本或重启设备。这个级别总会被记录日志。纠正WARNING级别输出的日志也可以考虑报告给数据收集服务器。</span></span>

*   <span style="font-size:14px;"><span style="color:#000000;">INFORMATIVE：这个级别的日志被用来提醒对大多数人来说有趣的事，比如说，当发现了一种会有大范围影响的情况，尽管不必是一个错误。这种情况应该只被区域里面适度被认为是最权威的模块记录日志（来避免非权威的组件重复记录日志）。这个级别总会被记录日志。</span></span>

*   <span style="font-size:14px;"><span style="color:#000000;">DEBUG：这个级别的日志应该被用来进一步地提醒设备正在发生的和调查、调试相关的事。你应该只记录必要的部分来收集关于你组件里正发生什么的足够信息的日志。如果你的调试日志占了你日志的很大一块，那么你很可能应该使用VERBOSE了。</span></span>

        *   <span style="font-size:14px;"><span style="color:#000000;">这个级别会被记录日志，即便是release版本，并且要求被if (LOCAL_LOG)或者if (LOCAL_LOGD)区块包围，在这里LOCAL_LOG[D]被定义为你的类或者子组件，以便到时候能一下把所有的日志全部禁用。所以在(LOCAL_LOG)区块中不能有有用的逻辑语句。所有的字符串构建工作也得在区块里面完成。如果字符串构建的工作会因为被重构到外面的方法中跑到区块的外面，输出日志的调用就不该这么做了。</span></span>

    *   <span style="color:#000000;"><span style="font-size:14px;">还是有些代码会写if (localLOGV)，这是可以接受的，虽然名字不是标准的。</span></span>
*   <span style="color:#000000;"><span style="font-size:14px;">VERBOSE：这个级别的日志被用来处理剩下的所有事情。该级别只会在debug版本被输出，并且应该被&nbsp;if (LOCAL_LOGV) 区块（或等价的方式）围起来以便它们能在默认情况能被编译出来。构建日志字符串的工作会在release版本中被移除，需要在if (LOCAL_LOGV) 区块中完成。</span></span>

			<span style="color:#000000;"><span style="font-size:14px;">注意：</span></span>

			&nbsp;

	</div>

	<div>

*   <span style="color:#000000;"><span style="font-size:14px;">在提供的模块之内，除了VERBOSE级别，错误如果可能的话应该只被提交一次：在模块里一条单一的函数调用链中，只该在最内层的函数里返回错误，同一模块的调用函数只该在日志能极大地帮助隔离问题的情况下添加一些日志。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">在一组模块当中，除了VERBOSE级别，当低级别的模块发现了从稍高级别传来的不合法的数据，低级别的模块只该用DEBUG日志输出该情况，并仅当日志里会提供调用函数不这么做就得不到的信息。明确点说，当异常被抛出的时候不需要输出日志（异常中应包含所有相关信息），或是被输出日志的信息都被错误代码包含了。这点在框架和程序的交互中尤为重要，还有被框架妥善处理了的第三方程序引发的状况不该出发高于DEBUG级别的日志。当且仅当某个模块或程序发现了来源于自己层级或更低层级的错误的时候才能使用INFORMATIVE以上的日志。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">如果某种触发输出日志的情况会发生很多次，实现一些限制比率的机制避免输出很多重复或相似的日志是个很好的方法。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">失去网络连接是很平常也完全可以预料的情况，这种情况不该被大量的输出日志。丢失网络连接对应用产生影响的话，应该在DEBUG或VERBOSE级别被输出（这取决于这个影响是否足够严重、足够不可预料来在release版本中输出）。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">一个可以使用的或代表第三方程序的完整的文件系统不该输出高于INFORMATIVE级别的日志</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">来自不可信来源的不合法数据（包括在共享存储中的任何文件或来自任何网络连接短时间之后的数据）被看做在预料之中的，不该在发现不合法（甚至这种情况输出日志也该尽量少）后触发任何高于DEBUG级别的日志。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">记住&rdquo;+&ldquo;运算符，在用在字符串里时，隐形地用默认缓冲大小（16字符）和其他潜在的一些临时的String对象创建了一个StringBuilder，换句话说，显性地新建StringBuilder不会被默认的&rdquo;+&ldquo;运算符耗费更多（事实上效率还高得多）。同时记住调用Log.v()的代码再release版本被编译和执行，包括创建字符串，即使这些日志没有被读到。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">任意给别人读的或在release版本中的日志应该尽量简洁而不是模糊，还得适当地可以理解。这点包括所有直到DEBUG级别的日志。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">可能的时候，日志如果讲得通的话能尽量保持一行。行长度在80到100个字符之间完全可以接受，而比130到160个字符还要长的话（包括tag的长度）可能的话就得尽量避免了。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">输出成功的日志级别绝对不要高于VERBOSE。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">用于诊断难以重现的问题的临时日志应该被保留在DEBUG或VERBOSE级别，还应该放在if区块当中以便在编译时完整的被禁用。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">小心在使用日志时泄露安全性的问题。私有的信息应该被避免。被保护内容的信息必须完全避免。这在写框架层代码时尤其重要，因为很难提前知道哪些会哪些不会是私密信息或被保护的内容。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">System.out.println()（或native代码中的printf()）千万别用。System.out和System.err被重定向到/dev/null，所以你的输出语句不会有任何显示的效果。然而，所有和这些调用相关的字符串创建工作还是被执行了。</span></span>

*   <span style="color:#000000;"><span style="font-size:14px;">输出日志的指导原则是：你的日志最好不在不必要的情况下把其他日志推出缓冲区，就像其他日志不会把你的推出去一样。</span></span>
	</div>

		<span style="font-size:14px;"><a id="一致性" name="一致性"><span style="color:#000000;">14.注重一致性</span></a></span>

		<span style="color:#000000;"><span style="font-size:14px;">我们的一些结束语：注重一致性。如果你在编辑代码，花点时间看看周围的代码再确定它的风格。如果它们在if语句周围使用空格，你也应该这么做。如果他们的注释周围加了些星号，你的注释也该加星号。</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">做这个风格指南是为了有一个代码的常用术语集，以便人们能专注于你说的内容，而不是你怎么说的。我们把总体的风格规范展现在此便于人们了解这些术语。但是本地的风格也很重要。如果你向文件中添加的代码和周围已经存在的代码格格不入，它会在别人读代码时破坏读者的节奏。试着避免这种情况吧。</span>

## 
		<font color="#000000"><span style="line-height: 22.390625px;">后记</span></font>

		<span style="font-size:14px;"><font color="#000000"><span style="line-height: 22.390625px;">翻译这篇Guideline是个非常痛苦的过程。毕竟自己的编程经验不够丰富，对里面的一些建议理解不够深刻；我的翻译水平也不足以让我很好地把原文的意思表达清晰。不过我还是坚持下来了。对于有需求的人来说，死记硬背是无效的。先记住一些最常规的，再在实践中不断武装剩下的才是最好的方法。</span></font></span>

		&nbsp;

</div>