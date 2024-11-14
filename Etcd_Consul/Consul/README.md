# Consul
Создаем образ nginx с consul template
```
docker build --tag consul-nginx --platform linux/arm64/v8 .
```

Запустим кластер консул с созданным образом
```
docker compose up -d
```

Для обновления сертификатов
```
mkdir certs && cd certs
consul tls cert create -server
consul tls cert create -client
```

Поднятый кластер

<img width="998" alt="image" src="https://github.com/user-attachments/assets/8a8b8f15-72fe-4924-8323-8d374a851136">


Создадим тестовые значения

<img width="659" alt="image" src="https://github.com/user-attachments/assets/611c3634-86d9-4a09-bc15-556644c5159d">

Уроним ноду consul-server-2

<img width="1012" alt="image" src="https://github.com/user-attachments/assets/cc093d48-7de8-4bd8-bc65-898e5aafbca0">

Введенные значения сохранились 

<img width="670" alt="image" src="https://github.com/user-attachments/assets/cb3bdb53-48b8-4fc5-8c92-587be88b3025">
