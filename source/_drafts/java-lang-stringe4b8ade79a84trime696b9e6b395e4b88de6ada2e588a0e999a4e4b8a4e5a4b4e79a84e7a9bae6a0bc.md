title: java.lang.String中的trim()方法不止删除两头的空格
tags:
  - Debug
  - FACK-Android
  - Java
id: 321
categories:
  - Android
  - 技术实践
date: 2013-01-05 16:48:38
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">昨天极品QA提交了一个bug给我，说是一条状态删除到逗号的时候会FC。关逗号什么事儿呢？逗号还不是字符串！你不能歧视它！本着为逗号正名的正义之心，我果断打开了源码找问题，终于聚焦到了trim()这个函数。trim()一直被我用来去掉字符串两边可能存在的空格，莫非有鬼？</span></span>

## 
	<span style="color:#000000;">探索</span>

	<span style="color:#000000;"><span style="font-size:14px;">首先看了下trim方法的文档：</span></span>

<pre class="brush:java;">
public String trim () 
Since: API Level 1 
Copies this string removing white space characters from the beginning and end of the string.

Returns
a new string with characters &lt;= \\u0020 removed from the beginning and the end. </pre>

	<span style="color:#000000;"><span style="font-size:14px;">人家还真没说是只删掉两边空格的。返回值是删掉了开头和结尾存在的&ldquo;&lt; = \\u0020&rdquo;的字符串。这里面也没说关逗号啥事儿啊&hellip;google一把java string trim 会删除的内容找到一篇研究的文章如下：</span></span>

### 
	<span style="color:#000000;">java.lang.String中的trim()方法的详细说明</span>

	<span style="color:#000000;"><span style="font-size:14px;">String.Trim()方法到底为我们做了什么，仅仅是去除字符串两端的空格吗？</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; line-height: 1.6em;">一直以为Trim()方法就是把字符串两端的空格字符给删去，其实我错了，而且错的比较离谱。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">首先我直接反编译String类，找到Trim()方法：</span></span>

<pre class="brush:java;">
public string Trim()
{
    return this.TrimHelper(WhitespaceChars, 2);
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">TrimHelper方法有两个参数，第一个参数名WhitespaceChars，首字母尽然是大写的，肯定有文章，真不出我所料：</span></span>

<pre class="brush:java;">
internal static readonly char[] WhitespaceChars;</pre>

	<span style="color:#000000;"><span style="font-size: 14px; line-height: 1.6em;">这里只是定义它，没有赋值，而且是静态的，我们看看构造函数去，果然找到：</span></span>

<pre class="brush:java;">
static String()
{ Empty = &quot; &quot;; WhitespaceChars = new char[] { &#39;/t&#39;, &#39;/n&#39;, &#39;/v&#39;, &#39;/f&#39;, &#39;/r&#39;, &#39; &#39;, &#39;/x0085&#39;, &#39;/x00a0&#39;, &#39;?&#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39;?&#39;, &#39;?&#39;, &#39;?&#39;, &#39;?&#39;, &#39;?&#39;, &#39; &#39;, &#39;?&#39;, &#39;?&#39;, &#39;/u2028&#39;, &#39;/u2029&#39;, &#39; &#39;, &#39;?&#39; }; }  </pre>

	<span style="color:#000000;"><span style="font-size:14px;">Trim方法就是把字符串两端的这些字符给删去？我很坚定的猜想到。

	继续我们的探索，直接反编译TrimHelper，哇，也许这个才是我想要的，私有的TrimHelper方法：</span></span>

<pre class="brush:java;">
private string TrimHelper(char[] trimChars, int trimType)
{
    int num = this.Length - 1;
    int startIndex = 0;
    if (trimType != 1)
    {
        startIndex = 0;
        while (startIndex &lt; this.Length)
        {
            int index = 0;
            char ch = this[startIndex];
            index = 0;
            while (index &lt; trimChars.Length)
            {
                if (trimChars[index] == ch)
                {
                    break;
                }
                index++;
            }
            if (index == trimChars.Length)
            {
                break;
            }
            startIndex++;
        }
    }
    if (trimType != 0)
    {
        num = this.Length - 1;
        while (num &gt;= startIndex)
        {
            int num4 = 0;
            char ch2 = this[num];
            num4 = 0;
            while (num4 &lt; trimChars.Length)
            {
                if (trimChars[num4] == ch2)
                {
                    break;
                }
                num4++;
            }
            if (num4 == trimChars.Length)
            {
                break;
            }
            num--;
        }
    }
    int length = (num - startIndex) + 1;
    if (length == this.Length)
    {
        return this;
    }
    if (length == 0)
    {
        return Empty;
    }
    return this.InternalSubString(startIndex, length, false);
}</pre>

	<span style="color:#000000;"><span style="font-size: 14px; line-height: 1.6em;">经过分析和运行，基本上知道了这个方法是干什么的了。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">TrimHelper方法有两个参数：</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">第一个参数trimChars，是要从字符串两端删除掉的字符的数组；</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">第二个参数trimType，是标识Trim的类型。就目前发现，trimType的取值有3个。当传入0时，去除字符串头部的空白字符，传入1时去除字符串尾部的空白字符，传入其他数值(比如2) 去除字符串两端的空白字符。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">最后再看看真正执行字符串截取的方法:</span></span>

<pre class="brush:java;">
private unsafe string InternalSubString(int startIndex, int length, bool fAlwaysCopy)
{
    if (((startIndex == 0) &amp;&amp; (length == this.Length)) &amp;&amp; !fAlwaysCopy)
    {
        return this;
    }
    string str = FastAllocateString(length);
    fixed (char* chRef = &amp;str.m_firstChar)
    {
        fixed (char* chRef2 = &amp;this.m_firstChar)
        {
            wstrcpy(chRef, chRef2 + startIndex, length);
        }
    }
    return str;
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">&nbsp;原来也用指针的？第一次看到，效率应该比较高吧。&nbsp;</span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">最后总结一下：&nbsp;</span></span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">String.Trim()方法会去除字符串两端，不仅仅是空格字符，它总共能去除25种字符:&nbsp;(&#39;/t&#39;, &#39;/n&#39;, &#39;/v&#39;, &#39;/f&#39;, &#39;/r&#39;, &#39; &#39;, &#39;/x0085&#39;, &#39;/x00a0&#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39; &#39;, &#39;?&#39;, &#39;/u2028&#39;, &#39;/u2029&#39;, &#39; &#39;, &#39;?&#39;)</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;"><span style="font-size:14px;">如果你想保留其中的一个或多个(例如/t制表符，/n换行符，/r回车符等)，请慎用Trim方法。</span></span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;">请注意，Trim删除的过程为从外到内，直到碰到一个非空白的字符为止，所以不管前后有多少个连续的空白字符都会被删除掉。</span></span></span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;">最后附上两个相关的方法（也是String类直接提供的），分别去除字符串头部空白字符的TrimStart方法和去除字符串尾部空白字符的 TrimEnd方法。</span></span></span></span></span></span></span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;"><span style="font-size:14px;">如果想去除字符串两端其他任意字符，可以考虑Trim他的重载兄弟：String.Trim(Char[])，传入你想要去除的哪些字符的数组。</span></span></span></span></span></span></span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">源码奉上：</span></span>

<pre class="brush:java;">
public string Trim(params char[] trimChars)
{
    if ((trimChars == null) || (trimChars.Length == 0))
    {
        trimChars = WhitespaceChars;
    }
    return this.TrimHelper(trimChars, 2);
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">空格 != 空白字符，删除空格请使用: Trim(&#39; &#39;);</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">转载自：http://blog.csdn.net/muyu114/article/details/5734295</span></span>