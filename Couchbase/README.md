# Запускаем кластер Couchbase
```
alla@MBP-Alla Desktop % docker run -d --name db1 couchbase
Unable to find image 'couchbase:latest' locally
latest: Pulling from library/couchbase
4be1db8bbbeb: Pull complete 
0178f47af7f5: Pull complete 
26b0c58c39b9: Pull complete 
d5e9b370f594: Pull complete 
ed78ca14ad1d: Pull complete 
5671e02dad6c: Pull complete 
3873c10fbd13: Pull complete 
18cf2d31d997: Pull complete 
265ed722984a: Pull complete 
e5dfc3f907d0: Pull complete 
9c5189f0e223: Pull complete 
Digest: sha256:78446fa1cf1c9cea6c563114b7882b5c3f84d1663207b15dd966bf8f10b427c0
Status: Downloaded newer image for couchbase:latest
551b600b9630d60df28118bd6ed59660f62f1242e6abe42a4576f8f3eacda13d
alla@MBP-Alla Desktop % docker run -d --name db2 couchbase
184ac619fcbf49781566034b026306705e29bb89f37c73a63c6cf2b05f95181b
alla@MBP-Alla Desktop % docker run -d --name db3 -p 8091-8096:8091-8096 -p 11210-11211:11210-11211 couchbase
0fc593de64325dfd41997bd47475e5a0e82d7028cfc11a6c24cfef6f68f07f07
```

Переходим на http://localhost:8091/ui/index.html

<img width="570" alt="image" src="https://github.com/user-attachments/assets/af9d0d41-0166-4a5b-afbf-fa2dc90f3af7">


<img width="604" alt="image" src="https://github.com/user-attachments/assets/d72a4bae-be5b-49ee-b9fe-643d4ddfce82">

Смотрим адреса других серверов 
```
alla@MBP-Alla Desktop % docker inspect --format '{{ .NetworkSettings.IPAddress }}' db2                  
172.17.0.3
alla@MBP-Alla Desktop % docker inspect --format '{{ .NetworkSettings.IPAddress }}' db1                  
172.17.0.2
```

Добавляем сервера через веб-морду и нажимаем rebalance

<img width="1189" alt="image" src="https://github.com/user-attachments/assets/554ff32c-e646-40e8-8926-ee754a310cca">

Наполняем тестовыми данными

<img width="994" alt="image" src="https://github.com/user-attachments/assets/8db230ba-b92e-4052-b78a-5a89277a7e41">

<img width="1223" alt="image" src="https://github.com/user-attachments/assets/a6250d21-e167-4109-afb3-6f9f4d0b895d">

Установим фактор репликации 2 и сделаем перебалансировку

<img width="514" alt="image" src="https://github.com/user-attachments/assets/076baa3f-074c-456c-b1be-cdcce3807df6">


Остановим одну ноду и сделаем перебалансировку
```
alla@MBP-Alla Desktop % docker stop 184ac619fcbf
```

Убедимся в сохранности данных через консоль
```
cbq> SELECT a.name FROM `beer-sample`.`_default`.`_default` a LIMIT 5;
{
    "requestID": "1ec60541-bf52-499d-b61a-b2e927ce0c33",
    "signature": {
        "name": "json"
    },
    "results": [
    {
        "name": "21st Amendment Brewery Cafe"
    },
    {
        "name": "21A IPA"
    },
    {
        "name": "563 Stout"
    },
    {
        "name": "Amendment Pale Ale"
    },
    {
        "name": "Bitter American"
    }
    ],
    "status": "success",
    "metrics": {
        "elapsedTime": "5.045292ms",
        "executionTime": "4.78925ms",
        "resultCount": 5,
        "resultSize": 206,
        "serviceLoad": 2
    }
}
```

Уроним еще одну ноду и сделаем перебалансировку

<img width="816" alt="image" src="https://github.com/user-attachments/assets/0f4e8b24-8df9-48ab-8d19-bb1fa0c6c6a4">

убедимся, что данные доступны
```
cbq> SELECT a.type FROM `beer-sample`.`_default`.`_default` a LIMIT 5;
{
    "requestID": "8f138d65-2e83-4eeb-b997-ec9508dac861",
    "signature": {
        "type": "json"
    },
    "results": [
    {
        "type": "beer"
    },
    {
        "type": "beer"
    },
    {
        "type": "beer"
    },
    {
        "type": "beer"
    },
    {
        "type": "beer"
    }
    ],
    "status": "success",
    "metrics": {
        "elapsedTime": "6.617958ms",
        "executionTime": "6.361875ms",
        "resultCount": 5,
        "resultSize": 150,
        "serviceLoad": 2
    }
}
```




