title: 'Customers who bought X also bought A,B,C items'
tags:
  - Design
  - SQL
  - 技术
id: 1021
categories:
  - 技术实践
  - 数据库
date: 2014-02-04 03:59:51
---

## 
	<span style="color:#000000;">Given:</span>

	<span style="color:#000000;"><span style="font-size:14px;">Assume: if a customer buys A,B,C items in his history then they are all related and each one should appear in others</span></span>

## 
	<span style="color:#000000;">Find:</span>

	<span style="color:#000000;"><span style="font-size:14px;">Design the system for Amazon website where if you purchase an item X , it shows &quot;Customers who bought X also bought A,B,C items&quot;.&nbsp;</span></span>

## 
	<span style="color:#000000;">Solution:</span>

	<span style="font-size:14px;">We probably need to carry out a database design to solve this problem. The general idea is to use this item to find which customers bought it. Then search with each user to find out what they bought other than this item. To improve the correlation between X, A, B and C, these items probably should be in one category.&nbsp;</span>

	<span style="font-size:14px;">Consider 3 database tables: &quot;users&quot;, &quot;items&quot;, &quot;categories&quot; and 1 association table, &quot;deals&quot;, which contains 2 columns: user_id and item_id.&nbsp;</span>

	<span style="font-size:14px;">Through these 4 tables, we can have the followings associations:&nbsp;</span>

1.  <span style="font-size:14px;"><span style="line-height: 1.6em;">An user has bought many items.</span></span>
2.  <span style="font-size:14px;"><span style="line-height: 1.6em;">An item belongs to one category.</span></span>

	<span style="font-size:14px;">So &#39;Users who bought item X also bought items&#39; can be accomplished by the following steps:&nbsp;</span>

1.  <span style="font-size:14px;">Get users from deals table where &#39;item_id&#39; = &#39;X&#39;.</span>
2.  <span style="font-size:14px;">For each user, find all the items he bought that belongs to the same category as &#39;X&#39;.</span>
3.  <span style="font-size:14px;">Merge all user&#39;s item arrays.</span>