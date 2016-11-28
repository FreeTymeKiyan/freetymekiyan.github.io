title: Export Index Using Elasticsearch Dump
date: 2016-03-11 13:38:21
categories:
  - Coding
tags:
  - Elasticsearch
  - Notes
---

A very common problem we encounter in Elasticsearch cluster management is how to copy an index to another cluster. For example, we need to make a back up copy for all our current data, or we need to copy data to staging cluster to test query performance.   
  
The intuitive way would be manually get all documents under the specific index, create another index using the same settings and mappings, then index all documents to the newly created index. Fortunately, we do not need to do that. There are already several good solutions. One of them is using a third-parth tool named Elasticsearch Dump.   
  
## Elasticsearch Dump  
  
ElasticSearch Dump is a node package for moving and saving indices. To use this handy tool, you need to have node installed before using it.   
  
### Installation<sup>[[1]](#References)</sup>  
  
Assume that you already have node, to install it:   
  
```bash Install elasticdump
npm install elasticdump -g
```
  
You probaly need to add `sudo` before the above command if there is any permission problem.   
  
### Usages<sup>[[1]](#References)</sup>  
  
Then `elasticdump` command should be available from your command line. The command works by specifying an input and an output. Both can be either an elasticsearch url or a file, or even standard io streams from terminal.   
  
To fully copy an index from production to staging, we provide the command with production hostname, port and index name as input, staging hostname, port and index name as output. We should move the analyzer first, the mapping second, and finally the data. 
  
```bash Copy an index from production to staging with analyzer and mapping
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=analyzer
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=mapping
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=data
```
  
I encountered an error when try to dump data. It's related to how many connections can be set up to target host at one time. If you also have limit on your host, please refer to `limit` and `maxSockets` options in the Useful Options table. 
  
We may also specify output as a local file instead of a url.   
  
```bash Backup index data to a file
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=/data/my_index_mapping.json \
  --type=mapping
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=/data/my_index.json \
  --type=data
```
  
We can pipe the outcome with gzip using stdout.   
  
```bash Backup and index to a gzip using stdout
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=$ \
  | gzip > /data/my_index.json.gz
```
  
### Useful Options  
  
There are many options for the elasticdump command. I list a few most useful options with comments in the table below.   
<br>

|Options|Explanation|Default|Comment|
|:-----:|:----------|:-----:|:------|
|limit|How many objects to move in bulk per operation limit is approximate for file streams|100|Can avoid too much traffic generate at one time|
|debug|Display the elasticsearch commands being used|false|Useful when error happens|
|type|What are we exporting? Can be data, mapping, analyzer|data|Can export mapping or analyzer instead of data only|
|delete|Delete documents one-by-one from the input as they are moved. Will not delete the source index|false|If you want to remove old data in the meantime|
|searchBody|Preform a partial extract based on search results|{"query": { "match_all": {} } }|Provide query to customize target data|
|all|Load/store documents from ALL indexes|false||
|bulk|Leverage elasticsearch Bulk API when writing documents|false|Should use bulk as a default to be more efficient|
|ignore-errors|Will continue the read/write loop on write error|false||
|scrollTime|Time the nodes will hold the requested search in order.|10m||
|maxSockets|How many simultaneous HTTP requests can we process make?|5 [node <= v0.10.x]<br>Infinity [node >= v0.11.x] ||
|help|This page|-|Check help if you forget some options|
  
## Summary  
  
Elasticsearch Dump satisfies the need adequately. 
  
There are also some other options. The native solution would be the Snapshot & Restore<sup>[[2]](#References)</sup>. Or you can use plugins like Elasticsearch InOut<sup>[[3]](#References)</sup> and Elasticsearch knapsack<sup>[[4]](#References)</sup>. In addition to that, you can use other third-party tools like ElasticSearch Exporter<sup>[[5]](#References)</sup>. 
  
## References  
  
1. [Elasticsearch-dump Github Page](https://github.com/taskrabbit/elasticsearch-dump)  
2. [Snapshot and Restore, Elasticsearch documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html)  
3. [Elasticsearch Inout Plugin, Github Page](https://github.com/crate/elasticsearch-inout-plugin)
4. [Knapsack plugin for Elasticsearch, Github Page](https://github.com/jprante/elasticsearch-knapsack)
5. [Elasticsearch Exporter, Github Page](https://github.com/mallocator/Elasticsearch-Exporter)  
  
