title: Back of the Envelope Calculation
date: 2016-09-21 19:48:48
categories:
 - Coding
 - Reading Note
tags:
 - System Design
---

So, I continued the journey on learning about System Design and found another very good resource. It is a talk by Jeff Dean at Stanford named *Software Engineering Advice from Building Large-Scale Distributed Systems*<sup>[[1]](#References)</sup>. In this talk, Jeff Dean shared the lessons and tips learned from his various kinds of experience.  
  
One important skill he pointed out in the talk is the ability to estimate performance of a system design, in terms of time cost. Why is it important? Because this skill actually allows you to choose the best solution without building it.  
  
Some fundamental knowledge is required to grasp this method:  
  
1. Time costs for some basic steps
2. Decomposition of a design
  
## Time Cost  
  
Jeff provided a brief table for the costs. This table is lucid overall but some rows in it might be unclear. I will show the table first and try to explain those rows.  
  
### Time Cost Table  
  
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
  
### Explanation  
  
We keep speaking and hearing all the time that L1 cache is the fastest, then the L2 cache, then the main memory, and finally the disk. We always want to cache data in memory instead of disk. Here in the table we can find data support for this point. In addition to these numbers, we should also concern the time costs of L3 cache(12.9 ns), Solid-state drive(50-150 μs), and network(can vary based on different network environment)<sup>[[2]](#References)</sup>.  
  
As main memory becomes larger and larger nowadays, a clear trend in system design is to put performance significant data in memory rather than on disk. For example, in Elasticsearch, I use in-memory aggregation to boost the query process and avoid disk seeks. As a result, the system can return 1000 hits in 10 to 20ms, which is almost 100x faster than a normal query(around 1 sec). Another example would be the widely use of in-memory cache, such as Memcached and Redis, which can save us enormous amount of disk hits from read requests send to DB.  
  
What is Branch mispredict? Here is the explanation from Wikipedia<sup>[[3]](#References)</sup>: 
  
    Branch misprediction occurs when a central processing unit (CPU) mispredicts the next instruction to process in branch prediction, which is aimed at speeding up execution.   
    
    During the execution of certain programs there are places where the program execution flow can continue in several ways. These are called branches, or conditional jumps. The CPU also uses a pipeline which allows several instructions to be processed at the same time. When the code for a conditional jump is read we do not yet know the next instruction to execute and insert into the execution pipeline. This is where branch prediction comes in. Branch prediction guesses the next instruction to execute and inserts the next assumed instruction to the pipeline. Guessing wrong is called branch misprediction. The partially processed instructions in the pipeline after the branch have to be discarded and the pipeline has to start over at the correct branch when a branch misprediction is detected. This slows down the program execution.  
  
We will show an example in the next section that relates to branch mispredict.  
  
What is Mutex lock/unlock? I found some words from Webopedia<sup>[[4]](#References)</sup> that explains it well:  

    Mutex is short for mutual exclusion object. In computer programming, a mutex is a program object that allows multiple program threads to share the same resource, such as file access, but not simultaneously. When a program is started, a mutex is created with a unique name. After this stage, any thread that needs the resource must lock the mutex from other threads while it is using the resource. The mutex is set to unlock when the data is no longer needed or the routine is finished.   
  
Wikipedia also throws light on mutex's significance<sup>[[5]](#References)</sup>:   

    Mutual exclusion is a property of concurrency control, which is instituted for the purpose of preventing race conditions; it is the requirement that one thread of execution never enter its critical section at the same time that another, concurrent thread of execution enters its own critical section.  
  
So knowing how long it takes to do mutex lock/unlock, we are able to estimate the time cost for concurrent designs.  
  
What is Zippy? Zippy, or Snappy, is actually a fast compressor/decompressor open sourced by Google. Here is what I found on Snappy Github page<sup>[[6]](#References)</sup>: 
  
    It aims for very high speeds and reasonable compression. Compared to the fastest mode of zlib, Snappy is an order of magnitude faster for most inputs, but the resulting compressed files are anywhere from 20% to 100% bigger.
  
In situations where our design consists of data compression, we can use the number here to help.  
  
"Send 2K bytes over 1Gbps network" is easy. Just do the math. 2 * 10^3 * 8 bit / (1 * 10^9) bit/s = 16 * 10^(-6) s ≈ 2 * 10^4 ns.  
  
Read 1 MB sequentially from memory/network/dist. Obviously, with the time costs we know that reading from memory is the fastest. When calculate time cost, I think it is more useful to know how many bytes per second instead, which are 4000 MB/s, 100 MB/s, and 30 MB/s, respectively.  
  
Round trip within same datacenter. It is good to know that even the same data center has time cost. Latency in the data center matters when it comes to performance critical systems. That means even our machines in the same cluster access each other can take time. For instance, if we’re using a distributed in-memory key/value store, we need to fetch data from another machine in the cluster. That would be a few 500,000ns (or 0.5ms) round-trips in the same datacenter<sup>[[7]](#References)</sup>.  
  
Send packet CA->Netherlands->CA, 150ms. That is a round trip between the west of North America and the west of Europe. We should take this into consideration if we need to build some global distributed services. Network is not "free". Network takes time.  
  
As I found out during writing this post, this talk is given 7 years ago at 2009. All the numbers are based on a mid-range PC at that time<sup>[[8]](#References)</sup>. So the numbers can be smaller now and there are also some more things to cover. I think it would be better if we have somewhere to keep all these figures in relation to design updated. Anyway, the exact numbers here are not as important as the differences in magnitude as you move up and down the table.  
  
## Decomposition a Design  
  
I think it is rough to just summarize how to decompose a design since it is based on the knowledge of operation systems and the actual experience on building a performance significant system. For those who do not have them, this topic would be better explained in examples.  
  
### Example 1
  
For example, suppose we are going to generate an image result page that has 30 thumbnails in it. We definitely have various of available solutions. The very basic one can be reading the thumbnails serially from disk and show them. How can we decompose it? Well, for each thumbnail, we need to seek it on disk, and then read. With the average size of a thumbnail given, which is 256k, we can estimate the time cost:  
  
`30 seeks * 10 ms/seek + 30 * 256 k / 30 MB/s = 560 ms.`  
  
560ms is definitely too long since whatever latency larger than 100ms is noticeable to user. How can we improve that? Obviously, by taking another look at the first design, we shall notice that there were no order requirments for those thumbnails, so that the thumbnails other than the current one do not need to wait. We can read them all at the same time. So this process can be boosted by reading thumbnails with one thread for each in parallel instead. How long does that take?  
  
`10 ms/seek + 256 k read / 30 MB/s = 18 ms`  
  
Of course, 256 k is just an average size. The estimation above ignores the variance. The full generation time depends on the largest thumbnail which takes longer to read. So we can imagine the real time cost might be much higher 
  
Besides these two solutions, there is still potential to make the design even faster. If we take another look at the second design, we know that the bottleneck is that everything is on disk. What if we cache the thumbnails in memory? As long as the image size is not that big, we can put all of them in memory with `256 k * 30 = 6 ~ 7 MB` space. Comparing to the large amount of memory we have on modern machines, it is totally acceptable. The time cost here would be:  
  
`100 ns/main memory reference + 256 k read / 4000 MB/s = 64.1 us`  
  
### Example 2  
  
The problem is "How long does it take to quicksort 1 GB of 4 byte numbers". This is a pure time cost estimation question, as we already know the implementation of quicksort. So how can we decompose the steps?  
  
First we need to figure out how many numbers we have, which is the input length `n`. 10^9 byte is roughly 2^30. 
  
`n = 2^30 / 4 = 2^28`  
  
Then we know the time complexity of quicksort it O(nlogn) and the basic step of quicksort is comparison. So the number of comparisons is around:  

`2^28 * log(2^28) ≈ 2^33` (Here 2^4 < log(2^28) = 28 < 2^5 and we take the larger one).  
  
Around half of the comparisons will mispredict. So  

`2^32 mispredicts * 5 ns/mispredict = 21 seconds`   
  
Another thing to consider is the memory bandwidth. Suppose the quicksort should be sequential, and the machine we run this sorting have 4GB memory. So  
  
`2^30 bytes * log(2^28) passes = 28 GB. 28 GB / 4 GB/s = 7 seconds`  
  
As a result, it should take around 30 secs to sort 1 GB on one CPU machine with 4 GB memory.  
  
## Summary  
  
In this post I shared a method by Jeff Dean to estimate time cost of a design, named "Back of the Envelope Calculation". The estimation consists of mainly two steps: 1) Knowing the time cost of basic steps 2) Decomposing a design into those basic steps. Then we will be able to get an intuitive assessment on our possible designs and choose the best one.  
  
The "Back of the Envelope Calculation" here focuses on time cost. Another interesting topic is memory usage estimation, which is also widely used before actual implementation. I remembered seeing my former tech lead doing this on a tiny piece of post-it paper and was totally fascinated. I told myself I would also be that kind of engineer one day. So stay tuned on the next topic: Memory Usage Estimation.  
  
## References  
  
1. [Software Engineering Advice from Building Large-Scale Distributed Systems, Google Research PPT](http://static.googleusercontent.com/media/research.google.com/en//people/jeff/stanford-295-talk.pdf)
2. [Alex Martelli's answer to "How much time does it take to fetch one word from memory?", Stackoverflow](http://stackoverflow.com/a/3142870/3642996)
3. [Branch misprediction, Wikipedia](https://en.wikipedia.org/wiki/Branch_misprediction)  
4. [Mutex, Webopedia](http://www.webopedia.com/TERM/M/mutex.html)  
5. [Mutual exclusion, Wikipedia](https://en.wikipedia.org/wiki/Mutual_exclusion)  
6. [Snappy Readme, Github](https://github.com/google/snappy)  
7. [Data By The Numbers, Linux Magazine](http://www.linux-mag.com/id/7589/)  
8. [The Infinite Space Between Words, codinghorror.com](https://blog.codinghorror.com/the-infinite-space-between-words/)  
  
