# Consul
Создаем образ nginx с consul template
```
docker build --tag consul-nginx --platform linux/arm64/v8 .
```

Запустим кластер консул с созданным образом

```
mkdir certs && cd certs
consul tls cert create -server
consul tls cert create -client
```
```
docker compose up -d
```

Поднятый кластер

<img width="1218" alt="image" src="https://github.com/user-attachments/assets/81f48d0e-68db-42f3-b548-a95527aa487a">


Создадим тестовые значения
<img width="659" alt="image" src="https://github.com/user-attachments/assets/611c3634-86d9-4a09-bc15-556644c5159d">

Уроним ноду

