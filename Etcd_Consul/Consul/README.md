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


```
docker exec -- consul-server1 consul operator raft list-peers
Node            ID                                    Address          State     Voter  RaftProtocol  Commit Index  Trails Leader By
consul-server1  5d7cb1b9-e05e-0c66-51dd-fbe675e4c4d9  172.19.0.7:8300  follower  true   3             712           0 commits
consul-server2  75e22d5b-e9ad-d320-dfa8-2b888ce6e923  172.19.0.4:8300  follower  true   3             712           0 commits
consul-server3  058b95e9-bd1f-7a3c-7702-587252014804  172.19.0.2:8300  leader    true   3             712           -
```

