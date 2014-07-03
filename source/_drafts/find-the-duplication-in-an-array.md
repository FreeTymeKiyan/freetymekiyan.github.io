title: 'Find the duplication in an array'
tags:
  - Algorithms
  - Java
  - 代码
  - 技术
id: 1005
categories:
  - Java
  - 技术实践
date: 2014-01-23 06:03:13
---

## 
	<span style="color:#000000;">Given:</span>

	<span style="color:#000000;"><span style="font-size:14px;">An integer array with 100 numbers ranging&nbsp;from 1 to 99 in it. There is exactly one number that is duplicate.</span></span>

## 
	<span style="color:#000000;">Find:</span>

	<span style="color:#000000;"><span style="font-size:14px;">The duplicate number.</span></span>

## 
	<span style="color:#000000;">Solution:</span>

	<span style="color:#000000;"><span style="font-size:14px;">We may come up with a solution this way. Basically the sum of 99 different numbers and the sum of the array should be different because of the duplication. What&#39;s the relation between them? If we do a minus between the two sums, all the distinctive numbers will be removed, the result is the duplication.</span></span>

<pre class="brush:java;">
int find_duplicate_num(int array[]) { 
    int i=0; 
    int sum=0; 
    int sum2=(1+99)*99/2; 
    for(i=0;i&lt;100;++i) { 
        sum+=array[i]; 
    } 
    return sum-sum2; 
}</pre>