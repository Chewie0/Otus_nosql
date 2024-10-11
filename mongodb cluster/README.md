# Кластерные возможности Mongodb

Развернем кластер
```
docker compose up -d
```
Прокатаем конфиги по нодам

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
Добавим шарды к роутеру
```
sh.addShard("shard-replica-set-1/mongo-shard-1-rs-1:40011,mongo-shard-1-rs-2:40012,mongo-shard-1-rs-3:40013")
```

```
sh.addShard("shard-replica-set-2/mongo-shard-2-rs-1:40021,mongo-shard-2-rs-2:40022,mongo-shard-2-rs-3:40023")
```

<img width="719" alt="image" src="https://github.com/user-attachments/assets/67b0f44c-d95f-4c6d-b14c-a69417861d89">


Создадим коллекцию и наполним тестовыми данными
```
use test_db
db.createCollection('person')
sh.shardCollection('person')
db.person.createIndex({_id: 1})
```

```
use admin
db.runCommand({shardCollection: "test_db.person", key: {_id: 1}})
sh.status()
use test_db
db.person.stats().storageSize / (1024 * 1024) + " MB"
use config
db.settings.updateOne(
 { _id: "chunksize" },
 { $set: { _id: "chunksize", value: 1 } },
 { upsert: true }
)
```

<img width="356" alt="image" src="https://github.com/user-attachments/assets/57606a01-948c-4b1e-90a2-a0826aec75b5">


коллекция разбилась по шардам

<img width="712" alt="image" src="https://github.com/user-attachments/assets/99e1d419-a5fd-4bb9-9001-2a64055e58bb">


```
sh.balancerCollectionStatus("test_db.person")
db.person.getShardDistribution()
```

<img width="427" alt="image" src="https://github.com/user-attachments/assets/05633c2b-2b24-4896-a918-4ece9a7b7e7a">



Попробуем поронять инстансы
Уроним primary сервер mongo-shard-1-rs-1

```
docker stop mongo-shard-1-rs-1
```
```
mongosh --port 40012
rs.status()
```
mongo-shard-1-rs-2 стал PRIMARY сервером, данные сохранились

<img width="686" alt="image" src="https://github.com/user-attachments/assets/ea9c45f0-f572-4147-901a-247f024aee31">


После поднятия mongo-shard-1-rs-1 - сервер стал в роль SECONDARY

<img width="588" alt="image" src="https://github.com/user-attachments/assets/73ec9bd8-06f4-4d84-9626-d50a25581e90">
