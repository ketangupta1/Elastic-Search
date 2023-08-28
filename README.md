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
