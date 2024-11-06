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

Заполнение таблиц данными

```
insert into books (some_id, name, info) values (1, 'Долгая прогулка', 'interesting book');
insert into books (some_id, name, info) values (2, 'Ночная смена', 'interesting book');
insert into books (some_id, name, info) values (3, 'Игра Джералда', 'interesting book');
insert into books (some_id, name, info) values (4, 'Четыре сезона', 'not interesting book');

insert into persons (some_id, some_date, time, name) values (1, '2024-11-06', '2024-11-06 00:00', 'Андрей');
insert into persons (some_id, some_date, time, name) values (2, '2024-11-05', '2024-11-05 00:00', 'Василий');
insert into persons (some_id, some_date, time, name) values (3, '2024-11-04', '2024-11-04 00:00', 'Мария');
insert into persons (some_id, some_date, time, name) values (4, '2024-11-06', '2024-11-06 00:00', 'Александра');
insert into persons (some_id, some_date, time, name) values (1, '2024-11-06', '2024-11-06 12:00', 'Елизавета');
insert into persons (some_id, some_date, time, name) values (1, '2024-11-06', '2024-11-06 14:00', 'Яков');

```

Создание вторичного индекса 

```
create index name_index on books (name);
```

Запросы к таблицам

```
cqlsh:test_keyspace> select * from books where name = 'Ночная смена';

 some_id | info             | name
---------+------------------+--------------
       2 | interesting book | Ночная смена

(1 rows)

```

```
cqlsh:test_keyspace> select * from persons where some_id = 1 and some_date = '2024-11-06' order by time;

 some_id | some_date  | time                            | name
---------+------------+---------------------------------+-----------
       1 | 2024-11-06 | 2024-11-06 00:00:00.000000+0000 |    Андрей
       1 | 2024-11-06 | 2024-11-06 12:00:00.000000+0000 | Елизавета
       1 | 2024-11-06 | 2024-11-06 14:00:00.000000+0000 |      Яков

(3 rows)

```

```
cqlsh:test_keyspace> select * from books where info = 'interesting book' ALLOW FILTERING ;

 some_id | info             | name
---------+------------------+-----------------
       1 | interesting book | Долгая прогулка
       2 | interesting book |    Ночная смена
       3 | interesting book |   Игра Джералда

(3 rows)
```

