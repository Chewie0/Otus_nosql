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
mongosh --port 40011
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
![image](https://github.com/user-attachments/assets/14194df2-9397-4b69-9352-342ebc4932f2)


```
mongosh --port 40021
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

```
mongosh --port 40031
```
```
rs.initiate({
 "_id" : "shard-replica-set-3",
 members : [
 {"_id" : 0, host : "mongo-shard-3-rs-1:40031"},
 {"_id" : 1, host : "mongo-shard-3-rs-2:40032"},
 {"_id" : 2, host : "mongo-shard-3-rs-3:40033" }
 ]
});

```
```
sh.addShard("shard-replica-set-1/mongo-shard-1-rs-1:40011,mongo-shard-1-rs-2:40012,mongo-shard-1-rs-3:40013")
```

```
sh.addShard("shard-replica-set-2/mongo-shard-2-rs-1:40021,mongo-shard-2-rs-2:40022,mongo-shard-2-rs-3:40023")
```

![image](https://github.com/user-attachments/assets/15166f28-07bf-44b7-9d0a-dc4f08a6416e)

