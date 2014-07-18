title: 6 Steps of Learning Node
tags:
  - Node.js
categories:
  - Coding
date: 2014-07-12 15:00:00
---
Recently I felt really puzzled by a problem: how can I be as cool as those senior engineers I met? They always impress me with their rich fund of knowledge and mature skills. I felt like that I'm able to build something, grasp a language from nothing, but still far from an expert.

I ran into a post[(1)](#Reference) on [cnodejs.org](http://www.cnodejs.org) earlier today and looked into the comments under it as well. It mentioned 6 steps in learning Node.js.

##Steps
###Step 1
The first step is about the fundamentals of Node.js. What we need to know are the following things:

1. The grammar and features of JavaScript.
2. What is Node.js? What are the differences between Node.js and JavaScript?
3. The advantages and disadvantages of Node.js.
4. The proper scenarios of Node.js.
5. Basic grammar of Node.js. The features of Node.js:
  * event-driven
  * async non-blocking I/O model
6. What is npm and its common usages.
7. REPL
8. ...

These topics are introduces in most books about Node.js. After we gain a general knowledge of it, we may write some "hello world" programs:

* Build an HTTP server with http module which returns hello world.
* Read in a txt file and output the content to command line.
* ...

###Step 2
The second step is learning some third-party modules. We may feel that too little are offered by the core modules to do something. No worries, there are more the 80,000 modules on npm right now, and more than 300,000,000 downloads for the last month. So let's build some interesting stuff with these modules:

* Build a twitter like website.
* Build a blog website.
* Build an online chat room.
* Build a simple web crawler.
* Build something fun with provided APIs.
* ...

###Step 3
Coming back to core modules ranks the third. To be blunt, go through the official API docs carefully.

###Step 4
What about the forth step? Of course we need more practices. No matter using core modules or third-party ones, try to solve some problem or replace the code we wrote before with Node.js. Moreover, we'd better read some source code. The source code here doesn't mean the source code of Node.js itself, or those large frameworks. At this time, pick some source code of utility modules which implements some certain function. This kind of modules usally have several hundreds of lines, not too difficult while we may learn a lot. For example:

* underscore
* ...

###Step 5
The fifth step is to continue with the above one and then try to create your own module. If you can't find any module that is satisfactory, then it's your chance. Also, it would be great if you can modify or update some modules. Go ahead and submit some pull requests to that github repository.

###Step 6
Last but not least, read the source code of Node.js and famous frameworks. This is necessary for improvement. Well, we also have many coding rock stars on github. Follow them, see what they are doing, get in touch with the community, we will finally be great.

##To Add
Personally, I think these steps works for all programming languages or even any skills that can be gained. Some things I want to emphasis are the books and community. Books about Node.js, certain modules or JavaScript are always there. Read those classic ones and learn from them. For the community part, we have plenty of resources both online and offline. I want to note some but there are too many out there. See the lists below from nodejs.org[(2)](#Reference) for more details.
###Online
1. Subscribe [Node.js Weekly](http://nodeweekly.com/).
2. Keep an eye on Stack Overflow's Node.js tag. You may also subscribe this one.
3. [Planet Node](http://www.planetnodejs.com/) -- an aggregator of Node developer blogs.
4. [Noder's on Twitter](https://github.com/joyent/node/wiki/Node-Users).
5. [CNodeJS.org](http://www.cnodejs.org) -- Node.js Chinese community.

###Offline
1. Meet ups on [Node Meatspace](http://nodemeatspace.com/).
2. [NodeConf](http://nodeconf.com/) -- conferences are the main event in the United States.
3. [Node Summit](http://nodesummit.com/) -- a conference in San Francisco focusing on the adoption of Node in larger companies.

##Reference
1. [学习Node.js的六个步骤](http://cnodejs.org/topic/535376501969a7b22aca6d24)
2. [Node.js.org, Community Page](http://nodejs.org/community/)
