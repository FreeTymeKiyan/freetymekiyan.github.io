title: 'Sort String Array to make Anagrams next to each other'
tags:
  - Algorithms
  - Java
  - 代码
  - 技术
id: 1014
categories:
  - Java
  - 技术实践
date: 2014-02-02 03:43:51
---

## 
	<span style="color:#000000;">Given:</span>

	<span style="color:#000000;"><span style="font-size:14px;">An array of strings.</span></span>

## 
	<span style="color:#000000;">Find:</span>

	<span style="color:#000000;"><span style="font-size:14px;">Sort the array so that all the anagrams are next to each other.</span></span>

## 
	<span style="color:#000000;">Solution:</span>

	<span style="color:#000000;"><span style="font-size:14px;">What is anagram? Anagrams are like &quot;cinema&quot; and &quot;iceman&quot;, words with same characters but different order. Now that we have an array of strings, these strings are all differnet words, some of them are anagrams. What we have to do is to sort the array to put anagrams next to each other. It&#39;s like group the strings and no specific ordering of the words is required.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">One way to do this is to just apply any standard sortring algorithms, like merge sort or quick sort, and modify the comparator. This comparator will be used to indicate that two strings which are anagrams of each other are equivalent. To accomplish this, we may need to sort any string we get, then use compareTo method to get the result. Two words which are anagrams will look the same once they&#39;re sorted.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">The codes below implements the comparator:</span></span>

<pre class="brush:java;">
public class AnagramComparator implements Comparator&lt;String&gt;{

    @Override
    public int compare(String o1, String o2) {
        return sortChars(o1).compareTo(sortChars(o2));
    }

    private String sortChars(String s) {
	char[] content = s.toCharArray();
	Arrays.sort(content);
	return new String(content);
    }
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">Then we only need to use the code below to sort the array.</span></span>

<pre class="brush:java;">
Arrays.sort(array, new AnagramComparator());</pre>

	<span style="color:#000000;"><span style="font-size:14px;">This algorithm will take O(nlog(n)) time.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">This might be a little bit overkilled because we only need to group the arrays. We can do this by using a hash table which maps from the sorted version of a word to a list of its anagrams. Once we&#39;ve grouped all the words into these lists by anagram, we can then put them back into the array.</span></span>

<pre class="brush:java;">
public void sort(String[] array) {
	Hashtable&lt;String, LinkedList&lt;String&gt;&gt; hash = 
			new Hashtable&lt;String, LinkedList&lt;String&gt;&gt;();
	for (String s : array) {
		String key = sortChars(s);
		if (!hash.containsKey(key)) {
			hash.put(key, new LinkedList&lt;String&gt;());
		}
		LinkedList&lt;String&gt; anagrams = hash.get(key);
		anagrams.push(s);
	}

	int index = 0;
	for (String key : hash.keySet()) {
		LinkedList&lt;String&gt; list = hash.get(key);
		for (String t : list) {
			array[index] = t;
			index++;
		}
	}
}</pre>

	<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">&nbsp;</span>

	&nbsp;