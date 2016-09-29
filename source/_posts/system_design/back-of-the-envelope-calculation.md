title: Back of the Envelope Calculation
date: 2016-09-21 19:48:48
categories:
 - Coding
 - Reading Note
tags:
 - System Design
---

So, I continued the journey on learning about System Design and found another very good resource. It is a talk by Jeff Dean at Stanford named *Software Engineering Advice from Building Large-Scale Distributed Systems*. In this talk, Jeff Dean shared the lessons and tips learned from his various kinds of experience.  
  
One important skill he pointed out in the talk is the ability to estimate performance of a system design. Why is it important? Because this skill actually allows you to choose the "best" solution, according to the problem definition and the meaning of "best", without building it.   
  
Some fundamental knowledge is required to grasp this method:  
  
1. Time costs for some basic steps
2. Decomposition of a design
  
### Time Cost  
  
Jeff provided a brief table for the costs. This table is lucid overall but some rows in it might be unclear. I will show the table first and try to explain those rows.  
  
#### Time Cost Table  
  
Table 1.1 Numbers Everyone Should Know  
  
|Basic Step|Time|
|:---------|:--:|
|L1 cache reference|0.5ns|
|Branch mispredict|5ns|
|L2 cache reference|7ns|
|Mutex lock/unlock|100ns|
|Main memory reference|100ns|
|Compress 1K bytes with Zippy|10,000ns|
|Send 2K bytes over 1Gbps network|20,000ns|
|Read 1 MB sequentially from memory|250,000ns|
|Round trip within same datacenter|500,000ns|
|Disk seek|10,000,000ns|
|Read 1 MB sequentially from network|10,000,000ns|
|Read 1 MB sequentially from disk|30,000,000ns|
|Send packet CA->Netherlands->CA|150,000,000ns|
  
#### Explanation  
  
We say and hear all the time that L1 cache is the fastest, then the L2 cache, then the main memory, and finally the disk. Here in the table we can find data support for this point.  
  
As main memory of a machine becomes larger and larger nowadays, a clear trend in system design is to put performance significant data in memory rather than on disk. For example, in Elasticsearch, I use in-memory aggregation to boost the query process and avoid disk seeks. As a result, the system can return 1000 hits in 10 to 20ms, which is almost 100x faster than a normal query(around 1 sec). Another example would be the widely use of in-memory cache, such as MemCache and Redis, which can save us enormous amount of disk hits from read requests send to DB.  
  
What is Branch mispredict?  
  
What is Mutex lock/unlock? Why important?
  
What is Zippy? Why is it important?  

"Send 2K bytes over 1Gbps network" is easy. Just do the math. 2 * 10^3 / (1 * 10^9) = 2 * 10^(-6)s = 2 * 10^3ns, not 2 * 10^4ns... What is the problem here?  
  
Read 1 MB sequentially from memory.   
  
Round trip within same datacenter.   
  
Read 1 MB sequentially from network  
  
Read 1 MB sequentially from disk  
  
Send packet CA->Netherlands->CA, 150ms. That is a round trip between the west of North America and the west of Europe. Good to know it. We should take this into consideration if we need to build some global distributed services. Network takes time.  
  
### Decomposition a Design  
  
I think it is rough to just summarize how to decompose a design since it is based on the knowledge of operation systems and the actual experience on building a performance significant system. For those who do not have them, this topic would be better explained in examples.  
  
#### Examples  
  
For example,   
  
Another example,

Find more examples resources. 
  
## Summary  
  
In this post I shared a method by Jeff Dean to estimate time cost of a design, named "Back of the Envelope Calculation". The estimation consists of mainly two step: 1) Knowing the time cost of basic steps 2) Decomposing a design into those basic steps. Then we will be able to get a fairly intuitive assessment on our possible designs and choose the best one.  
  
Other interesting topics in this talk: 
Problems of Real Hardware  
  
Google Engineering Environment  
  
Designing Software Systems  
  Principles
  Get Advice Early
  Interfaces
  Protocols
  Efficiency
  
The "Back of the Envelope Calculation" here focuses on time cost. Another interesting topic is memory usage estimation, which is also widely used before actual implementation. I remembered seeing my former tech lead doing this on a tiny piece of post-it paper and was totally fascinated. I told myself I would also be that kind of engineer one day. So stay tuned on the next topic: Memory Usage Estimation. 
  
## References  
  
1. []()