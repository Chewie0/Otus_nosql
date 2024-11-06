Поднятие 3 узлового Cassandra кластера
```
docker-compose up -d
```
<img width="520" alt="image" src="https://github.com/user-attachments/assets/5495e457-229f-45f3-ac0c-7d9949b082be">

Проверим конфигурацию кластера
```
docker exec -it cassandra1 nodetool status
```

<img width="693" alt="image" src="https://github.com/user-attachments/assets/f1ffbcfa-afe5-478e-9b7e-836e3424d36b">

Подключимся через cqlsh

```
sudo docker exec -it cassandra1 cqlsh
```

Создадим keyspace 

```
CREATE KEYSPACE test_keyspace WITH replication = {'class':'SimpleStrategy', 'replication_factor' : 3};
```

Переключимся на созданный keyspace и создадим таблицы

```
CREATE TABLE books (some_id int primary key, name text, info text);

CREATE TABLE persons (some_id int, some_date date, time timestamp, name text, PRIMARY KEY ((some_id, some_date), time));
```


