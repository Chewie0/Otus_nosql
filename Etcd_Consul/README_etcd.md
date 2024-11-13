# Etcd

Поднимем кластер
```
docker compose up -d
```

Состояние кластера
```
docker exec -it desktop-etcd-02-1  etcdctl endpoint status -w table --cluster=true 
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|      ENDPOINT       |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| http://etcd-02:2379 | 9772fa935aee2fcb |   3.5.0 |   20 kB |     false |      false |         2 |          8 |                  8 |        |
| http://etcd-01:2379 | dc6856a29b285c1c |   3.5.0 |   25 kB |     false |      false |         2 |          8 |                  8 |        |
| http://etcd-00:2379 | f9bb7dc32e4429fd |   3.5.0 |   20 kB |      true |      false |         2 |          8 |                  8 |        |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
```

Добавим значения
```
docker exec -it desktop-etcd-02-1  etcdctl put key value                          
OK
docker exec -it desktop-etcd-02-1  etcdctl get key      
key
value
docker exec -it desktop-etcd-01-1  etcdctl get key 
key
value
```
Остановим контейнер
```
docker stop desktop-etcd-02-1
```

Состояние кластера и добавленных ключей
```
docker exec -it desktop-etcd-01-1  etcdctl endpoint status -w table --cluster=true
{"level":"warn","ts":"2024-11-13T15:10:26.096Z","logger":"etcd-client","caller":"v3/retry_interceptor.go:62","msg":"retrying of unary invoker failed","target":"etcd-endpoints://0x400015e700/#initially=[127.0.0.1:2379]","attempt":0,"error":"rpc error: code = DeadlineExceeded desc = latest balancer error: last connection error: connection error: desc = \"transport: Error while dialing dial tcp: lookup etcd-02 on 127.0.0.11:53: no such host\""}
Failed to get the status of endpoint http://etcd-02:2379 (context deadline exceeded)
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|      ENDPOINT       |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| http://etcd-01:2379 | dc6856a29b285c1c |   3.5.0 |   25 kB |     false |      false |         2 |          9 |                  9 |        |
| http://etcd-00:2379 | f9bb7dc32e4429fd |   3.5.0 |   20 kB |      true |      false |         2 |          9 |                  9 |        |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
docker exec -it desktop-etcd-01-1  etcdctl get key                               
key
value
```
Остановим мастер-ноду

```
docker stop desktop-etcd-00-1                                                     
```
```
docker exec -it desktop-etcd-01-1  etcdctl endpoint status -w table --cluster=true
{"level":"warn","ts":"2024-11-13T15:13:08.800Z","logger":"etcd-client","caller":"v3/retry_interceptor.go:62","msg":"retrying of unary invoker failed","target":"etcd-endpoints://0x4000154a80/#initially=[127.0.0.1:2379]","attempt":0,"error":"rpc error: code = DeadlineExceeded desc = context deadline exceeded"}
Error: failed to fetch endpoints from etcd cluster member list: context deadline exceeded

docker exec -it desktop-etcd-01-1  etcdctl get key                                
{"level":"warn","ts":"2024-11-13T15:13:20.925Z","logger":"etcd-client","caller":"v3/retry_interceptor.go:62","msg":"retrying of unary invoker failed","target":"etcd-endpoints://0x40003d8700/#initially=[127.0.0.1:2379]","attempt":0,"error":"rpc error: code = DeadlineExceeded desc = context deadline exceeded"}
Error: context deadline exceeded
```

После поднятия остановленных нод данные сохранились, лидер перешел к ноде etcd-01
```
docker exec -it desktop-etcd-01-1  etcdctl get key 
key
value
docker exec -it desktop-etcd-01-1  etcdctl endpoint status -w table --cluster=true
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|      ENDPOINT       |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| http://etcd-02:2379 | 9772fa935aee2fcb |   3.5.0 |   20 kB |     false |      false |         4 |         13 |                 13 |        |
| http://etcd-01:2379 | dc6856a29b285c1c |   3.5.0 |   25 kB |      true |      false |         4 |         13 |                 13 |        |
| http://etcd-00:2379 | f9bb7dc32e4429fd |   3.5.0 |   25 kB |     false |      false |         4 |         13 |                 13 |        |
+---------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
```


