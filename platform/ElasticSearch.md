
#### Elastic Search

##### Define Elasticsearch
* distributed
* JSON-based search
* analytics engine

##### Features
* Fast: BKD trees, column store
* Scalability: 1 -> 300 nodes
* Highly Available: detects failures
* Elasticsearch Plus Hadoop

##### Concepts
* Near RealTime: Add doc, Index first, Then search
* Cluster: a collection of one or more nodes
* Node: UUID is assigned to the node
* Index
* Type
* Documentation
* Shards: distributed，parallelize operations
* Replication: high availability

##### Health
* Health: yellow-one node, so not high availability

##### Index
```
curl -XPOST "http://localhost:9200/_search" -d'
{
    "query": {
        "query_string": {
            "query": "kill"
        }
    }
}'
```

##### Search
* POST request
* Query DSL
```
curl -XPOST "http://localhost:9200/_search" -d'
{
    "query": {
        "filtered": {
            "query": {
                "query_string": {
                    "query": "drama"
                }
            },
            "filter": {
                "term": { "year": 1962 }
            }
        }
    }
}
```

##### Sense
* A chrome plugin

##### DEMO
* http://joelabrahamsson.com/elasticsearch-101/

