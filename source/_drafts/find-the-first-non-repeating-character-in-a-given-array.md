title: 'Find the first non-repeating character in a given array'
tags:
  - Algorithms
  - Java
  - 代码
  - 技术
id: 1010
categories:
  - Java
  - 技术实践
date: 2014-02-01 05:30:41
---

## 
	<span style="color:#000000;">Given: </span>

	<span style="font-size:14px;"><span style="color:#000000;">A stream of characters.</span></span>

## 
	<span style="color:#000000;">Find: </span>

	<span style="font-size:14px;"><span style="color:#000000;">The first non<span class="s1">-</span>repeating character in the stream.&nbsp;<span style="line-height: 1.6em;">You can go through this stream only once.</span></span></span>

## 
	<span style="color:#000000;"><span style="line-height: 1.6em;">Solution:</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">We have to understand why it limits it to only once first. Basically, the easiest way everyone could come up with is to focus&nbsp;on the current character and go through the array to find duplication. If found, then go to next character. If not, then we get the result. It&#39;s easy to solve in this way, while we go through the array many times.</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">Now we only have one chance. So, we must get some necessary&nbsp;information which can further help us find the result. One information which may be of help is to count the number of times each character shows up. It&#39;s easy to implement this with a hashmap, with character as the key and integer count as the value. Then we simply iterate over the hashmap to get the character with only one count. </span></span>

	<span style="font-size:14px;"><span style="color:#000000;">But there is still one problem, for the iteration of hashmap&nbsp;operates randomly, we can&#39;t guarantee the final result is the first non-repeating character. To avoid it, we may choose another data structure called LinkedHashMap, which has a predictable iteration order.&nbsp;</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">The implementation goes like this:</span></span>

<pre class="brush:java;">
private static void lindkedHashMap(String input) {
	LinkedHashMap&lt;Character, Integer&gt; hashMap = new LinkedHashMap&lt;Character, Integer&gt;();

	for (int i = 0; i &lt; input.length(); i++) {
		char curChar = input.charAt(i);
		if (hashMap.containsKey(input.charAt(i))) {
			hashMap.put(curChar, hashMap.get(curChar) + 1);
		} else {
			hashMap.put(curChar, 1);
		}
	}

	for (Entry&lt;Character, Integer&gt; obj : hashMap.entrySet()) {
		if (obj.getValue() == 1) {
			System.out.print(&quot;First non-repeating character in the stream : &quot;+ obj.getKey());
			break;
		}
	}
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">It&#39;s still a bit hard to understand because we are using senior data structures, not a good way of thinking to solve this question. More generally, what we are trying to build is a relation between the index, the character, and the character count. As we all known, only 26 characters are there in English, and they&#39;ve been already well coded in ASCII. So, if we get a character from the input, we may substract it by &#39;a&#39; to get an offset to represent which character it is. These offsets can be used as indices of an array of integers to represent count at the same time. Moreover, we need to insert these characters in order to an array to maintain the sequence. This would help us to find the first non-repeating character easily.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Here is the implementation:</span></span>

<pre class="brush:php;">
public static final int TOTAL_ALPHABET = 26;

private static char selfBuildMap(String input) {
	char result = &#39;1&#39;;
	char[] charIndex = new char[TOTAL_ALPHABET]; // remember order
	int[] charCount = new int[TOTAL_ALPHABET]; // remember number of show up
	int charToInt;
	int charPos = -1; // we don&#39;t need to go through 26 characters most of the times

	String lowerCase = input.toLowerCase();

	for (int i = 0; i &lt; lowerCase.length(); i++) {
		charToInt = lowerCase.charAt(i) - &#39;a&#39;; // it&#39;s like an offset
		if (++charCount[charToInt] == 1) { // first time
			charIndex[++charPos] = lowerCase.charAt(i); // insert lower case, only one time 
		}
	}

	for (int i = 0; i &lt;= charPos; i++) {
		if (charCount[charIndex[i] - 'a'] == 1)
			return charIndex[i];
	}
	return result;
}</pre>

	&nbsp;