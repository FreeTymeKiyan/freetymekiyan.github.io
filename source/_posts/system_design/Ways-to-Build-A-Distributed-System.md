title: Ways to Build A Distributed System
date: 2016-09-13 13:58:24
categories:
 - Coding
 - Reading Note
tags:
 - System Design
---

Every engineer should learn about distributed system design, since it is not only the path to next career level, but a necessary ability to build a large-scale application according to use cases and specifications as well. Without a proper design, it would be impossible to implement and manage such a system afterwards. 

Today I read through "The Twitter Problem"<sup>[[1]](#References)</sup> on hiredintech.com and found it truly useful and easy to understand. This post is a summary on the classic problems and solutions when building a scalable system. These problems are generally met in most of the large applications and are well solved by those extraordinary predecessors. 

## Techniques

These techniques are divided into two parts: 1) Handling user requests 2) Storing the data. 

### Handling user requests

Suppose we already know what kind of system, how many requests approximately, and how complex the requests are. What are the ways to handle these requests?

#### Comparing the technologies

One aspect can be the technologies used to implement the application. Some technologies are specially designed for high concurrency situation with less memory usage. This can enhance the load handled by a single machine. With some research on these technologies, we can know the number of requests that can be handled by a single machine for each of them. Then pick the one that performs the best. 

One possible problem for this pre-research is time consuming. But it is worth it since the comparison result is reusable for the next design. Another downside can be that the best technology chosen may not be familiar to the team, or even not easier to pick up at all. All these factors should be considered before making the final decision. 

#### Scaling Up

Scailing up means improving a single machine. Think about the aspects you can improve, such as the CPU, the memory, the disk and etc. Examples are like replace CPU with more cores, increase the size of memory, and upgrade to SSD drive. 

This approach is useful in some cases, but after a given point, the hardware of one machine just is not capable of handling all the requests. It would also be a single point of failure if the machine goes down. 

#### Scaling Out

Scaling out basically means adding more nodes and building a cluster. The load is distributed to a number of machines instead of one. 

The approach is better generally, as it avoids single point failure and is easy to scale. When the current cluster cannot handle the load well, we can config a new machine and add it to the cluster. And if we know how many requests one node can handle, we can easily estimate how many nodes we need for a cluster as well. 

#### Software Load Balancer

We can run a software load balancer on a machine in front of the application cluster. With proper configuration, the load balancer will forward the request to the actual node behind it in the cluster, thus distributing the load across. Classic software load balancers are nginx and HAProxy. Some cloud providers like AWS also provide traffic load balancing as a service. 

Software load balancer definitely adds resiliency to an application. It is almost a must-have in distributed systems. But it can also be the bottleneck if the number of requests is too high and exceeds the capability of one single load balancer node. 

#### DNS Load Balancer

We can config a DNS server to parse domain to different hosts. This DNS load balancer is in front of software load balancer and can forward requests to different software load balancers. In this way, the system can handle even more requests and further scale out. 

#### Auto Scaling

Auto scaling is the ability to adjust the number of nodes in a cluster according current traffic level. Services like AWS and Heroku offer this kind of support. 

This technique is specifically useful when it comes to unusual high traffic, where caching is not that efficient. The system will respond to the traffic and add more nodes if the traffic is too high, then reclaim those nodes when the traffic goes down to normal. 

### Storing the data

#### Estimation on data sizes

Assume that we know the relations between our data objects, we can assess the approximate size of the data to be stored. For example, in a simple twitter problem, we have 10 million users and each of them post 1 tweet per day. Each tweet contains 140 characters. If we assume 2 bytes per character, it will be 2 * 140 * 10bln = 2.6 TB for a day. 

After a quick analysis on all possible data to be stored, there can be some data that takes up majority of the storage space. It is important to have a rough idea about the size of the data that the system will need to handle. Appropriate design decisions base on this. 

#### Adding Indexes

In relational DB, adding indexes to a specific field enables executing quick queries joining tables. If we know some fields are used frequently in filtering queries, we definitely should build an index over the field to optimize the times for such queries.

After creating the indexes, write queries will become slightly slower. Considering the time we can save for heavy read operations it is still worth it. 

#### Replication

With all the read requests coming to DB, we can have several replications that hold a copy of the data each. 

This will help only if the write operations does not increase dramatically at the same time. Otherwise, it will be complicated to sync data between primary and replications. 

#### Data partitioning (Sharding)

Divide the data in some way to several databases. After that, each DB will hold only part of the data, and also, share only part of the requests. 

If we have several databases and each of them only has part of the data, the read and write will speed up since the load is smaller. But a way should be pre-defined before partitioning. Otherwise we won't be able to know which DB a specific request should go to. And also, because the data size is smaller for each DB, it makes administration tasks like backups faster. 

#### In-memory cache

In order to handle the incoming read requests, we can add an in-memory cache in front of the relational DB. The reason why this works is that DB reads directly from disk, and it is much slower than from memory. Even if the DB has its own caching mechanisms, a separate cache layer enables us to gain more control over what to be cached and how. 

An in-memory cache will save us a lot of direct reads to DB. And since the results of most frequent requests is cached, the system is more resilient to usual traffic peaks. 

## Summary

These techniques from "The Twitter Problem" are described and analyzed at a very high level, so that we can have an general idea on each of them. This example also already showed what it is actually like to design a distributed system (or answer such problems in an interview). The next steps may be learning the actually theories behind each step mentioned, figuring out the reasons, and diving deep into each of those technologies. 

## References

1. [The Twitter Problem, hiredintech.com](http://www.hiredintech.com/data/uploads/hiredintech_system_design_the_twitter_problem_beta.pdf)
