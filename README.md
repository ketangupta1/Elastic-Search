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
- Each piece is called a shard.
- Sharding is done at the index level.
- Sharding improves performance by parallelization of the query. By default, every node has only one shard and each shard has the capacity of 2 billion documents only. If there are more than 2 billion documents then sharding will be performed.
- e.g: Let's assume we have a document and there are two indices students and teachers with a combined space of 900 GB, but our node has space of 500 GB only Then we will take 2     nodes of 500 GB each and will do sharding of indices. we will do sharding like putting students in the first node and teachers in 2nd node but there will be a problem if students index alone size is 800 GB and teachers index size will be only 100 GB. So we are doing sharding at index level shard both teachers as well as students indices.
- split and shrink API is there to perform sharding.


## CRUD Operations
- Check cluster health.

      GET _cluster/health
- Check nodes.

      GET _cat/nodes?v
- Check shards.
  
      GET _cat/shards?v
- Create index:

      PUT index_name
    Creating index by giving parameter
  
      PUT index_name
      {
      "settings": {
        "number_of_shards": 2,
        "number_of_replicas": 2
        }
      }
- Delete index
  
      DELETE index_name
- Add document to the index
  
      POST index_name/_doc
      {
        request body
        "name": "Rahul",
        "age": 35
      }
  If you want to provide id by yourself:
  
      POST index_name/_doc/id
      {
        request body
        "name": "Mohit",
        "age": 24
      }
- Delete document from the index
  
      DELETE index_name/_doc/id
- Fetch document in the index
  
      GET index_name/_doc/id
- Search for document
  
      GET index_name/_search
      {
        "query": {
          "match_all": {}
        }
      }
- Update document

      POST index_name/_update/id
      {
        "doc": {
          add_new_field or update_existing_field
          "age": 5,
          "last_name": "Sharma"
        }
      }
    When we are updating in elasticsearch it is not updating the document it replaces the document, because replacing is more efficient.

- Scripted Update
    We can write our own custom logic.

      POST index_name/_update/id
      {
        "script": {
          "source": "script"
          "params": {
            write your parameter
          }
        }
      }

    e.g:
      With script only:
  
      POST student/_update/765785452
      {
        "script": {
          "source": "ctx._source.age = 20"
        }
      }
  ctx will provide the whole document for the provided id, there is _source(data that we are providing stored in this) key and and inside source age si present so update the age to 20

  With script and params both:
  
      POST index_name/_update/765785452
      {
        "script": {
          "source": "ctx._source.age = params.new_age"
          "params": {
            "new_age": 5
          }
        }
      }
      In the 1st example condition is hard coded but here it is in the params.

- Multiline Script

      POST index_name/_update/id
      {
        "script": {
          "source": """
          if(ctx._source.age<=0){
            ctx.op = 'noop';
          }
          else{
            ctx._source.age--;
          }"""
        }
      }
    Multiline script is written under two double quotes(""), above it is three bcz one is for source. if age is less than or equal to 0 then ctx.op='noop' means no operations. else decrease the age by 1. If we write ctx.op='delete' then in this condition if age is less than or equal to 0 delete that document.
- Update by using _update_by_query:

      POST my-index-000001/_update_by_query
      {
        "script": {
          "source": "ctx._source.count++",
          "lang": "painless"
        },
        "query": {
          "term": {
            "user.id": "kimchy"
          }
        }
      }


- Update field with null value using _update_by_query: 

      index_name/_update_by_query
      {
        "script": {
          "source": "ctx._source.grade = params ",
          "params": {
      			"grade_description": "Grade_13",
      			"grade_code": "13"
      	},
          "lang": "painless"
        },
        "query": {
          "bool": {
              "must_not": {
                  "exists": {
                      "field": "grade.grade_code"
                  }
              }
          }
      }
      }

- Upsert

  If there is no document for the id provided and we try to update that document then there will be error. So upsert is used to handle that if no document is there then create a new one provided in the upsert.
  
      POST index_name/_update/id
      {
        "script":{
          "source": "ctx._source.age++"
        },
        "upsert": {
          "name": "Rahul",
          "age": 30
        }
      }
- Replace
  It will replace the old document with the new one provided.
  
      PUT index_name/_doc/id
      {
        "name": "Piyush",
        "age": 28
      }



## Learn Query
- Searching

      GET learn/_search
      {
        "_source": ["title", "duration"],        // It stands for what field you want the elasticsearch to return. Here the title and duration will be returned.
        "size": 20,                              // How many documents do you want to return in the search. Maxm size is 10k, if you want more than that you need to do pagination
        "min_score": 0.5,                        // Whenever we write any query elasticsearch gives a score by default. Here it will not return any document whose score is less than 0.5
        "query": {  
          "bool": {                              
            "must": [],                          // Must stands for 'AND' operator
            "filter": [],                        // Filter stands for filtering
            "should": [],                        // should stands for 'OR' operator
            "must_not": []                       // must_not stands for 'NOT' operator
          }
        }
      }
- Match and match Phrase

      PUT foo/_doc/1
      {"text": "i love elk its great"}
      
      PUT foo/_doc/2
      {"text": "i ELK search"}
      
      PUT foo/_doc/3
      {"text": "Elk elastic search"}
  Created index foo and added 3 documents with id 1,2 and 3 respectively.
  Now I'm going to search in these documents.

      GET foo/_search
      {
        "query": {
          "match": {
            "text": "elk"
          }
        }
      }
  The above search will return all the documents.

      GET foo/_search
      {
        "query": {
          "match": {
            "text": "elk search"
          }
        }
      }
  The above query will also give 3 results. This is because the elasticsearch will search for elk and search separately. To get an exact match MATCH PHRASE is used.

      GET foo/_search
      {
        "query": {
          "match_phrase": {
            "text": "elk search"
          }
        }
      }
  there is only one result for the above query because elk search is present in only one document.


- searching with must and match

      GET learn/_search
      {
        "_source": ["title"],
        "size": 20,
        "min_score": 0.5,
        "query": {
          "bool": {
            "must": [
              {
                "match": {
                  "title": "Blood"
                }
              }
              ,
              {
                "match": {
                  "title": "Father"
                }
              }
              ],
            "filter": [],
            "should": [],
            "must_not": []
          }
        }
      }
  The above query will search the words "Blood" and "Father" both words in the text of each document.

      GET learn/_search
      {
        "_source": ["title"],
        "size": 20,
        "min_score": 0.5,
        "query": {
          "bool": {
            "must": [],
            "filter": [],
            "should": [
              {
                "match": {
                  "title": "Blood"
                }
              }
              ,
              {
                "match": {
                  "title": "Father"
                }
              }
              ],
            "must_not": []
          }
        }
      }
  The above query will search for the word "Blood" or "Father" If any word is present in the text that document will be the part of result.

- Nested bool
  Write a query: Title contains "Norm" OR "King" but NOT contain the word "Sized" OR "Shaman".

      GET learn/_search
      {
        "_source": ["title"],
        "size": 20,
        "min_score": 0.5,
        "query": {
          "bool": {
            "must": [],
            "filter": [],
            "should": [
              {
                "match": {
                  "title": "King"
                }
              }
              ,
              {
                "match": {
                  "title": "Norm"
                }
              }
              ],
            "must_not": [
              {
                "bool": {
                  "should": [
                    {
                      "match": {
                        "title": "Sized"
                      }
                    },
                    {
                      "match": {
                        "title": "Shaman"
                      }
                    }
                  ]
                }
              }
              ]
          }
        }
      }
  
- Creating new index with same mapping and settings( Copying index ):
  First copy the mappings and settings from the old index.

      GET old_index_name/_mappings
      GET old_index_name/_settings
  Now create the new index:

      PUT /new_index_name
      {
        "settings": {
          "number_of_shards": 1
        },
        "mappings": {
          "properties": {
            "field1": { "type": "text" }
          }
        }
      }
  For copying data from the old index to the new use reindex API:

      POST _reindex
      {
        "source": {
          "index": "my-index-000001"
        },
        "dest": {
          "index": "my-new-index-000001"
        }
      }
- Add new or update existing mapping in index:
  
      PUT /index_name/_mapping
      {
        "properties": {
          "manager": {
            "properties": {
              "manager_id": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              },
              "manager_name": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              }
            }
          }
        }
      }
