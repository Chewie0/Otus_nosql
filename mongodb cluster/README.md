# Кластерные возможности Mongodb

```
mongosh --port 40001
```
```
rs.initiate({
 "_id": "config-replica-set",
 members : [
 {"_id": 0, "host": "mongo-configsvr-1:40001"},
 {"_id": 1, "host": "mongo-configsvr-2:40002"},
 {"_id": 2, "host": "mongo-configsvr-3:40003" }
 ]
});
```

```
mongosh --port 40001
```
```
rs.initiate({
 "_id": "config-replica-set",
 members : [
 {"_id": 0, "host": "mongo-configsvr-1:40001"},
 {"_id": 1, "host": "mongo-configsvr-2:40002"},
 {"_id": 2, "host": "mongo-configsvr-3:40003" }
 ]
});
```

```
mongosh --port 40002
```
```
rs.initiate({
 "_id" : "shard-replica-set-1",
 members : [
 {"_id" : 0, host : "mongo-shard-1-rs-1:40011"},
 {"_id" : 1, host : "mongo-shard-1-rs-2:40012"},
 {"_id" : 2, host : "mongo-shard-1-rs-3:40013" }
 ]
});
```

```
mongosh --port 40003
```
```
rs.initiate({
 "_id" : "shard-replica-set-2",
 members : [
 {"_id" : 0, host : "mongo-shard-2-rs-1:40021"},
 {"_id" : 1, host : "mongo-shard-2-rs-2:40022"},
 {"_id" : 2, host : "mongo-shard-2-rs-3:40023" }
 ]
});

```
