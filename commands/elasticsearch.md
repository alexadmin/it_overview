```
# SHOW INDEX INFO
curl -XGET localhost:9200/_cat/indices?pretty 

# LIST ALL INDEXES
curl 'localhost:9200/_cat/indices?v'

# SHOW CLUSTER LOAD
curl -XGET localhost:9200/_cat/nodes?v 

# CLUSTER STATUS
curl -XGET localhost:9200/_cluster/health?pretty

# SHOW PROCESSES LIST
curl -XGET 'localhost:9200/_tasks?pretty'

# DRAIN
curl -XPUT -H 'Content-Type: application/json' "http://localhost:9200/_cluster/settings" -d '{
  "transient" :{
      "cluster.routing.allocation.exclude._ip" : "X.X.X.X"
   }
}';echo
# ADD BACK
curl -XPUT -H 'Content-Type: application/json' "http://localhost:9200/_cluster/settings" -d '{
  "transient" :{
      "cluster.routing.allocation.exclude._ip" : null
   }
}'

curl -H 'Content-Type: application/json' -XPUT localhost:9200/_cluster/settings -d '{"transient" :{"cluster.routing.allocation.node_concurrent_recoveries" : 10}}';echo

curl -H 'Content-Type: application/json' -XPUT localhost:9200/_cluster/settings -d '{
"transient" :{
"indices.recovery.max_bytes_per_sec" : "80mb"
}
}';echo

# SHOW PENDING TASKS
curl -XGET 'http://localhost:9200/_cluster/pending_tasks' | jq

# SHOW UNASSIGNED SHARDS
curl -XGET localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason| grep UNASSIGNED

# ALLOCATE SHARDS
curl -X POST http://localhost:9200/_cluster/reroute?retry_failed=true -d '
{
    "commands" : [
        {
          "allocate_replica" : {
                "index" : "test333", "shard" : 2,
                "node" : "alex1"
          }
        }
    ]
}
'

# UPDATE REPLICA FOR ALL
curl -XPUT 'localhost:9200/_settings' -d '
    {
        "index" : {
            "number_of_replicas" : 2
        }
    }'

# UPDATE REPLICA FOR INDEX
curl -XPUT 'localhost:9200/index_name/_settings' -d '
    {
        "index" : {
            "number_of_replicas" : 2
        }
    }'

# DUMP
elasticdump --input=http://localhost:9200/my_index --output=my_index.json --type=data
elasticdump --input=http://localhost:9200/my_index --output=my_index_mapping.json --type=mapping

# COPY REMOTE
elasticdump --input=http://server001:9200/promo_index_v1 --output=http://server002:9200/promo_index_v1 --type=mapping
elasticdump --input=http://server001:9200/promo_index_v1 --output=http://server002:9200/promo_index_v1 --type=data

curl -XPUT 'localhost:9200/*/_settings' -H 'Content-Type: application/json' -d '{
 "index.indexing.slowlog.threshold.index.warn": "20s",
 "index.indexing.slowlog.threshold.index.info": "15s",
 "index.indexing.slowlog.threshold.index.debug": "10s",
 "index.indexing.slowlog.threshold.index.trace": "5s",
 "index.indexing.slowlog.level": "trace",
 "index.indexing.slowlog.source": "1000"
}'

curl -XPUT 'localhost:9200/*/_settings' -H 'Content-Type: application/json' -d '{
"index.search.slowlog.threshold.query.warn": "20s",
"index.search.slowlog.threshold.query.info": "15s",
"index.search.slowlog.threshold.query.debug": "10s",
"index.search.slowlog.threshold.query.trace": "5s",
"index.search.slowlog.threshold.fetch.warn": "20s",
"index.search.slowlog.threshold.fetch.info": "15s",
"index.search.slowlog.threshold.fetch.debug": "10s",
"index.search.slowlog.threshold.fetch.trace": "5s"
}'
```

```
# DELETE DOCUMENTS WITH FIELDS
curl -H 'Content-Type: application/json' -XPUT 'http://localhost:9200/web_dep_general_284/_settings' -d '{
     "index" : {
        "blocks.write" : false
     }
}'
curl -X POST "localhost:9200/web_dep_general_284/_delete_by_query?pretty" -H 'Content-Type: application/json' -d'
 {
   "query": {
       "match": {
         "channel": "basket"
       }
    }
 }
'
curl -H 'Content-Type: application/json' -XPUT 'http://localhost:9200/web_dep_general_284/_settings' -d '{
     "index" : {
        "blocks.write" : true
     }
}'
```
