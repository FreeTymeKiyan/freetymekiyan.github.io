title: Reroute Unassigned Shards
date: 2016-03-06 13:24:55
categories:
  - Coding
tags:
  - Elasticsearch
---
  
## Red Cluster!  
  
There are 3 cluster statuses:   
  
>1. green: Everything is good
>2. yellow: Something may be wrong, but the cluster is still running
>3. red: Cluster is dead  
  
Dead means you can do nothing until it's recovered, which is very bad indeed. I will share with you how to deal with one common situation: **when cluster is red due to unassigned shards**.  
  
## Steps  
  
The general idea is pretty simple: find those shards which are unassigned, manually assign them to a node with reroute API. Let's see how we can do that step by step. Then we can combine them into a configurable simple script. 
  
### Step 1: Check Unassigned Shards  
  
To get cluster information, we usually use cat APIs. There is a `GET /_cat/shards` endpoint to show a detailed view of what nodes contain which shards<sup>[[1]](#References)</sup>. 
  
```bash Cat shards
# cat shards verbose
curl "http://your.elasticsearch.host.com:9200/_cat/shards?v"

# cat shards index
curl "http://your.elasticsearch.host.com:9200/_cat/shards/wiki2"
# example return
# wiki2 0 p STARTED 197 3.2mb 192.168.56.10 Stiletto
# wiki2 1 p STARTED 205 5.9mb 192.168.56.30 Frankie Raye
# wiki2 2 p STARTED 275 7.8mb 192.168.56.20 Commander Kraken
```
  
By piping cat shards to fgrep, we can get all unassigned shards. 
```bash Get unassigned shards
# cat shards with fgrep
curl "http://your.elasticsearch.host.com:9200/_cat/shards" | fgrep UNASSIGNED
# example return
# wiki1 0 r UNASSIGNED ALLOCATION_FAILED
# wiki1 1 r UNASSIGNED ALLOCATION_FAILED
# wiki1 2 r UNASSIGNED ALLOCATION_FAILED
```

If you don't want to deal with shell script, you can also find these unassigned shards using another endpoint `POST /_flush/synced`<sup>[[2]](#References)</sup>. This endpoint is actually not just some information. It allows an administrator to initiate a synced flush manually. This can be particularly useful for a planned (rolling) cluster restart where you can stop indexing and don’t want to wait the default 5 minutes for idle indices to be sync-flushed automatically. It returns with a json response.  

```bash _flush/synced
curl -XPOST "http://your.elasticsearch.host.com:9200/twitter/_flush/synced"
```

If there are failed shards in the response, we can iterate through a failures array to get all unassigned ones. 
```json Example response with failed shards
{
   "_shards": {
      "total": 4,
      "successful": 1,
      "failed": 1
   },
   "twitter": {
      "total": 4,
      "successful": 3,
      "failed": 1,
      "failures": [
         {
            "shard": 1,
            "reason": "unexpected error",
            "routing": {
               "state": "STARTED",
               "primary": false,
               "node": "SZNr2J_ORxKTLUCydGX4zA",
               "relocating_node": null,
               "shard": 1,
               "index": "twitter"
            }
         }
      ]
   }
}
```
  
### Step 2: Reroute 
  
The reroute command allows to explicitly execute a cluster reroute allocation command including specific commands<sup>[[3]](#References)</sup>  . An unassigned shard can be explicitly allocated on a specific node.

```bash Reroute example
curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
    "commands" : [ {
        "move" :
            {
              "index" : "test", "shard" : 0,
              "from_node" : "node1", "to_node" : "node2"
            }
        },
        {
          "allocate" : {
              "index" : "test", "shard" : 1, "node" : "node3"
          }
        }
    ]
}'
```

There are 3 kinds of commands you can use: 
>move: Move a started shard from one node to another node. Accepts index and shard for index name and shard number, from_node for the node to move the shard from, and to_node for the node to move the shard to.

>cancel: Cancel allocation of a shard (or recovery). Accepts index and shard for index name and shard number, and node for the node to cancel the shard allocation on. It also accepts allow_primary flag to explicitly specify that it is allowed to cancel allocation for a primary shard. This can be used to force resynchronization of existing replicas from the primary shard by cancelling them and allowing them to be reinitialized through the standard reallocation process.

>**allocate**: Allocate an unassigned shard to a node. Accepts the index and shard for index name and shard number, and node to allocate the shard to. It also accepts allow_primary flag to explicitly specify that it is allowed to explicitly allocate a primary shard (might result in data loss).  
  
Combining step 2 with the unassigned shards from Step 1, we can reroute all unassigned shards 1 by 1, thus getting faster cluster recovery from red status.  
  
## Example Solutions

### Python  
  
Below is a python script I wrote using `POST /_flush/synced` and `POST /reroute`  
  
{% gist bc83e96bb818c6ac311a reroute.py %}
  
### Shell Script  
  
Below is a shell script I found elsewhere in a blog post<sup>[[4]](#References)</sup>  
  
```bash 
for shard in $(curl -XGET http://localhost:9200/_cat/shards | grep UNASSIGNED | awk '{print $2}'); do
    curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
        "commands" : [ {
              "allocate" : {
                  "index" : "t37", # index name
                  "shard" : $shard,
                  "node" : "datanode15", # node name
                  "allow_primary" : true
              }
            }
        ]
    }'
    sleep 5
done
```
  
## Possible Unassigned Shard Reasons  
  
FYI, these are the possible reasons for a shard be in a unassigned state<sup>[[1]](#References)</sup>:  

</br>

|Name|Comment|
|:--:|:------|
|INDEX_CREATED|Unassigned as a result of an API creation of an index|
|CLUSTER_RECOVERED|Unassigned as a result of a full cluster recovery|
|INDEX_REOPENED|Unassigned as a result of opening a closed index|
|DANGLING_INDEX_IMPORTED|Unassigned as a result of importing a dangling index|
|NEW_INDEX_RESTORED|Unassigned as a result of restoring into a new index|
|EXISTING_INDEX_RESTORED|Unassigned as a result of restoring into a closed index|
|REPLICA_ADDED|Unassigned as a result of explicit addition of a replica|
|**ALLOCATION_FAILED**|Unassigned as a result of a failed allocation of the shard|
|NODE_LEFT|Unassigned as a result of the node hosting it leaving the cluster|
|REROUTE_CANCELLED|Unassigned as a result of explicit cancel reroute command|
|REINITIALIZED|When a shard moves from started back to initializing, for example, with shadow replicas|
|REALLOCATED_REPLICA|A better replica location is identified and causes the existing replica allocation to be cancelled|
  
## References  
  
1. [ElasticSearch Document Cat Shards](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-shards.html#cat-shards)
2. [ElasticSearch Document Synced Flush](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-synced-flush.html)  
3. [ElasticSearch Document Cluster Reroute](https://www.elastic.co/guide/en/elasticsearch/reference/2.2/cluster-reroute.html)
4. [How to fix your elasticsearch cluster stuck in initializing shards mode?](https://t37.net/how-to-fix-your-elasticsearch-cluster-stuck-in-initializing-shards-mode.html)  
