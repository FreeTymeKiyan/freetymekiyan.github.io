title: Update Elasticsearch License
tags:
  - Elasticsearch
  - Notes
categories:
  - Coding
date: 2016-02-17
---
  
Marvel is the official Elasticsearch monitoring plugin. Although it's free, it requires subscription for a basic license.  
  
The initial trial license lasts for only one month. After the first month, marvel will stop showing dashboard and give you a grace period of 7 days to renew the license. If you do not renew it, not only marvel will not work anymore, but all marvel data will be deleted as well.  
  
Before researching for another handy monitoring plugin, I decide to renew the license first.  
  
## Steps to Renew License  
  
According to [the document](#References), the steps are pretty simple.  
  
1. Register for a new license on official site  
2. Update license through API  
  
### Register for a new license  
  
I will not dive into it too much. Just go to the register page and fill out a form. Then you will receive an email with download link of the license. Download and save the license file.  
  
### Update license through API  
  
Elasticsearch has a special license API.  
  
``` sh
curl -XPUT -u admin 'http://<host>:<port>/_license' -d @license.json
```
  
1. &lt;host&gt;: the hostname of the Elasticsearch node (localhost if executing locally)
2. &lt;port&gt;: the http port
3. license.json: the name of license JSON file
  
The `-u admin` is for Shield users.  
  
Note that for the file you need to specify an absolute path, unless it is under the current path you are running the terminal.  
  
The response will show you some information about the license. For example, if may tell you that the license is not enough to all previous services. If it is enough, you can call the API again with a query parameter `acknowledge=true`.  
  
``` sh
curl -XPUT -u admin 'http://<host>:<port>/_license?acknowledge=true' -d @license.json
```
  
## View License
  
``` sh
curl -XGET -u admin:password 'http://<host>:<port>/_license'
```
  
This API will get you the license status. Example result is shown below:  
  
``` js
// get license example response
{
  "license" : {
    "status" : "active",
    "uid" : "0a98411f-73f4-4c67-954c-724874ed5488",
    "type" : "trial",
    "issue_date" : "2015-10-13T18:18:20.709Z",
    "issue_date_in_millis" : 1444760300709,
    "expiry_date" : "2015-11-12T18:18:20.709Z",
    "expiry_date_in_millis" : 1447352300709,
    "max_nodes" : 1000,
    "issued_to" : "elasticsearch",
    "issuer" : "elasticsearch"
  }
}
```

## Problem  
  
What if the annoying expiry page still shows? I met this problem after everything in the document is done. So, it is actually not enough to just update license. To fix this problem, I tried to update marvel at the kibana end. 


### Update Marvel  
  
Update Marvel is fairly simple, you just remove the old one and install a new one.   
  
```sh
# remove marvel from kibana
bin/kibana plugin --remove marvel
```
  
Then you can install it.  
  
```sh
# install marvel to kibana, with latest version
bin/kibana plugin --install elasticsearch/marvel/latest
```

Sometimes it will not work because the version. Marvel version should be aligned with Kibana version. For example, if your Kibana is still 4.3.0, you cannot use latest Marvel, which is 2.2.0\. You will need to get Marvel 2.1.0 instead.

```sh
# install marvel to kibana
bin/kibana plugin --install elasticsearch/marvel/2.1.0
```
  
If the page still shows, you may restart the cluster.  
  
## References  
  
1. [Managing Your License](https://www.elastic.co/guide/en/marvel/current/license-management.html)
2. [Installing Marvel](https://www.elastic.co/guide/en/marvel/current/installing-marvel.html#installing-marvel)
  