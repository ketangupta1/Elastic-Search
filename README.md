# Elasticsearch Tutorial

## Elasticsearch
- Open source analytics and full-text search engine.
- Built-in Java, easy to use, highly scalable.
- Used to provide search functionality to an application with features like auto-completing, correcting typos, handling synonyms, etc.

## Kibana
- Dashboard for analyzing and visualizing data coming from Elasticsearch.

## Install and run Elasticsearch
- Download elasticsearch from: https://www.elastic.co/downloads/elasticsearch
- Unzip it and run elasticsearch using the command:
-     ./bin/elasticsearch
- If you are running it for the first time then password and configurations will also be generated so save it safely.

## Install and run Kibana
- Download kibana from: https://www.elastic.co/downloads/kibana
- Unzip and run kibana using the command:
-     ./bin/kibana
- Now a link will be generated open the link and configure it with your elasticsearch enrollment token.
- Enter your username and password. By default, your username is: elastic, and paste your password from the elasticsearch terminal.
- Click on Explore on my own
  
## Elasticsearch architecture
- Node: An instance of elasticsearch, when I write the command: ./bin/elasticsearch it means one node has been started.
- Cluster: Collections of nodes for a particular task.
- Every node has its cluster defined in its configurations if not then a cluster will be created and the currently running node will be part of that cluster.
- How data is stored:
    - Data is stored in JSON like:
      {
        "name": "Ram",
        "id": 1
      }
    - All keys starting with "_"  
- Every node contains metadata like index, type, id, source, etc.
      {
        "_index": " ",
        "_type": " ",
        "_id": " ",
        ......
        ......
        "_source":
      }

- "_source" contains the actual data. So every document is: metadata+JSON

- Some comparison with elasticsearch and RDBMS for understanding purposes only:
    - Document <-> rows in SQL
    - Fields <-> columns in SQL
    - Index <-> Table in SQL

- Documents are grouped together by indices.
 
## Elasticsearch REST-API
- There are two methods of getting data from elasticsearch. Using query and by using REST-API
- When we want data from elasticsearch we are running a query but that query is converted to the REST-API.
- To write API on kibana UI go to management->Dev Tools. Here we will write API.
- The structure of API contains: Method+API-name(started with _)+Command
    - GET _cluster/health
    - GET _cat/nodes?v: "_cat" data should come in a human-readable format. "?v" header should also come
    - GET _cat/indices?v&expand_wildcards=all

## Access it from query
- Open your terminal.
- For sending HTTP requests from the terminal curl is used.
- write command:
-      curl -X GET -k -u username:password https://localhost:9200
-  -k is used for bypassing the security
-      curl -X GET -k -u username:password https://localhost:9200/_cluster/health

## Sharding
- Sharding is a way to divide indices into small pieces.
- Each piece is called shard.
- Sharding is done at the index level.
- Sharding improves performance by parallelization of the query. By default, every node has only one shard and each shard has the capacity of 2 billion documents only. If there are more than 2 billion documents then sharding will be performed.
- e.g: Let's assume we have a document and there are two indices students and teachers with a combined space of 900 GB, but our node has space of 500 GB only Then we will take 2     nodes of 500 GB each and will do sharding of indices. we will do sharding like putting students in the first node and teachers in 2nd node but there will be a problem what if students index alone size is 800 GB and teachers index size will be only 100 GB. So we are doing sharding at index level shard both teachers as well as students indices.
- split and shrink API is there to perform sharding.


## CRUD Operations
- Check cluster health.
-     GET _cluster/health
- Check nodes.
-      GET _cat/nodes?v
- Check shards.
-     GET _cat/shards?v
- Create index:
  -     PUT index_name
  -     PUT index_name
        {
          "settings": {
            "number_of_shards": 2,
            "number_of_replicas": 2
          }
        }
- Delete index
-     DELETE index_name
- Add document to the index
  -     POST index_name/_doc
        {
          request body
        }
  If you want to provide id by yourself:
  -     POST index_name/_doc/8765490534
        {
          request body
        }
