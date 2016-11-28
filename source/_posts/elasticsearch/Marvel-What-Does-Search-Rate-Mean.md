title: Marvel What Does Search Rate Mean?
date: 2016-04-15 16:38:38
categories:
  - Coding
tags:
  - Elasticsearch
  - Notes
---

## Problem

Elasticsearch provides a handy monitoring tool named Marvel. It is pretty easy to setup. But when it comes to real monitoring, you will realize that those metrics in dashboard are so confusing. They are not consistent with the actual QPS you sent to the cluster. You don't really know what they refer to. 

## Solution

I googled it. But unfortunately there was no answer at that time. The only thing I got is [a post on Marvel forum](https://discuss.elastic.co/t/marvel-what-does-the-search-rate-mean/35260) where somebody was as confused as me. 

Then I tried to find the source code of Marvel. But I couldn't find anything either because it is not opensourced. I really think Marvel has so many more improvements needed. They really should opensource it. 

![Average Search Latency](/images/search_latency_after.png)
The answer came to me when I was struggling with the search performance. I reindexed all the data to try whether it would affect performance. To my surprise, after I changed to a single shard, the average search latency dropped from around 30ms to 7ms. And the search rate also drops to around 10k/s, which is exactly the estimation value of requests I sent to Elasticsearch cluster. All the mysteries disappeared and everything became crystal clear. 

![Marvel Dashboard](/images/latency_before.png)
The first one is "Search Rate". When the index had 10 shards, it was about 10 times the actual requests. When the index has only 1 shard, it was almost the same as the actual request. 

Then "Search Latency". When the index had 10 shards, it was around 0.9ms, but the end to end latency was around 30ms. When single shard, it goes up to 1.88ms, while the end to end latency is merely 7ms. That's probably because the amount of data of each shard increases because of fewer shards, hence the longer search in each shard. But there is no need to merge result anymore. Here, we can see how badly merging result affects search performance.

"Index Rate" and "Indexing Latency" are the same. 

## Summary

The meanings are listed below:

* Search Rate: for a single index, it's number of lookups per second * number of shards. For multiple indices, it's the sum of the search rate of each index. 
* Search Latency: Average latency within each shard. 
* Indexing Rate: for a single index, it's number of indexing per second * number of shards. For multiple indices, it's the sum of the indexing rate of each index. 
* Indexing Latency: Average latency within each shard. 
  
You can verify the conclusions above by changing the number of shards of an index and see how it changes afterwards. Feel free to let me know if there is anything wrong. 
