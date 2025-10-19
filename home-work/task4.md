Запускаем docker-compose вместе с шардированным кластером

```bash
user@fedora:~/WorkNew/mongodb$ docker-compose up -d
WARN[0000] /home/user/WorkNew/mongodb/docker-compose.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 18/18
 ✔ shard2_1 Pulled                                                                                                                                                                                                                                                                                                     37.8s 
 ✔ mongos Pulled                                                                                                                                                                                                                                                                                                       37.8s 
 ✔ cfg2 Pulled                                                                                                                                                                                                                                                                                                         37.8s 
 ✔ cfg3 Pulled                                                                                                                                                                                                                                                                                                         37.7s 
   ✔ 4b3ffd8ccb52 Pull complete                                                                                                                                                                                                                                                                                         5.4s 
   ✔ 5f211690369e Pull complete                                                                                                                                                                                                                                                                                         5.4s 
   ✔ e20d020ae6f3 Pull complete                                                                                                                                                                                                                                                                                         5.5s 
   ✔ c61448294a5e Pull complete                                                                                                                                                                                                                                                                                         5.5s 
   ✔ 31b397d90832 Pull complete                                                                                                                                                                                                                                                                                         5.6s 
   ✔ b92a15b39d95 Pull complete                                                                                                                                                                                                                                                                                         5.6s 
   ✔ 3c9ec5e75886 Pull complete                                                                                                                                                                                                                                                                                        36.0s 
   ✔ b92ad19150f3 Pull complete                                                                                                                                                                                                                                                                                        36.1s 
 ✔ shard2_3 Pulled                                                                                                                                                                                                                                                                                                     37.7s 
 ✔ shard1_3 Pulled                                                                                                                                                                                                                                                                                                     37.7s 
 ✔ cfg1 Pulled                                                                                                                                                                                                                                                                                                         37.8s 
 ✔ shard1_2 Pulled                                                                                                                                                                                                                                                                                                     37.8s 
 ✔ shard2_2 Pulled                                                                                                                                                                                                                                                                                                     37.8s 
 ✔ shard1_1 Pulled                                                                                                                                                                                                                                                                                                     37.8s 
[+] Running 19/20
 ✔ Network mongodb_mongo-cluster   Created                                                                                                                                                                                                                                                                              0.3s 
 ✔ Volume "mongodb_shard2_2_data"  Created                                                                                                                                                                                                                                                                              0.1s 
 ✔ Volume "mongodb_shard2_3_data"  Created                                                                                                                                                                                                                                                                              0.1s 
 ✔ Volume "mongodb_cfg2_data"      Created                                                                                                                                                                                                                                                                              0.1s 
 ✔ Volume "mongodb_shard2_1_data"  Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Volume "mongodb_cfg1_data"      Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Volume "mongodb_cfg3_data"      Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Volume "mongodb_shard1_3_data"  Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Volume "mongodb_shard1_1_data"  Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Volume "mongodb_shard1_2_data"  Created                                                                                                                                                                                                                                                                              0.0s 
 ✔ Container mongodb-shard2_1-1    Started                                                                                                                                                                                                                                                                              1.3s 
 ✔ Container mongodb-shard1_2-1    Started                                                                                                                                                                                                                                                                              1.4s 
 ✔ Container mongodb-shard1_1-1    Started                                                                                                                                                                                                                                                                              1.3s 
 ✔ Container mongodb-cfg1-1        Started                                                                                                                                                                                                                                                                              1.3s 
 ✔ Container mongodb-shard2_2-1    Started                                                                                                                                                                                                                                                                              1.7s 
 ✔ Container mongodb-shard2_3-1    Started                                                                                                                                                                                                                                                                              1.5s 
 ✔ Container mongodb-cfg3-1        Started                                                                                                                                                                                                                                                                              1.2s 
 ✔ Container mongodb-shard1_3-1    Started                                                                                                                                                                                                                                                                              1.2s 
 ✔ Container mongodb-cfg2-1        Started                                                                                                                                                                                                                                                                              1.5s 
 ⠼ Container mongodb-mongos-1      Starting                                                                                                                                                                                                                                                                             1.5s 
Error response from daemon: driver failed programming external connectivity on endpoint mongodb-mongos-1 (fe319ea4fd1f404a752c9283c06365e131f977c83233c2e3b9018c6cadecca0b): Bind for 0.0.0.0:27017 failed: port is already allocated
```
Обнаружил что порт 27017 занят, а это наш сервер из прошлой работы :D

```bash
user@fedora:~/WorkNew/mongodb$ docker stop mongodb
mongodb
user@fedora:~/WorkNew/mongodb$ docker-compose up -d
WARN[0000] /home/user/WorkNew/mongodb/docker-compose.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 10/10
 ✔ Container mongodb-shard2_3-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-cfg2-1      Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-shard1_1-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-shard1_3-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-shard2_2-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-cfg3-1      Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-cfg1-1      Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-shard1_2-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-shard2_1-1  Running                                                                                                                                                                                                                                                                                0.0s 
 ✔ Container mongodb-mongos-1    Started                                                                                                                                                                                                                                                                                0.2s 
user@fedora:~/WorkNew/mongodb$ 

```
Подключаемся к первому конфигурационному серверу и создаём реплику для хранения метаданных о шардировании.

```JS
user@fedora:~/WorkNew/mongodb$ docker exec -it mongodb-cfg1-1 mongosh --port 27019
Current Mongosh Log ID: 68f53bc7ed9dbb38eece5f46
Connecting to:          mongodb://127.0.0.1:27019/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
Using MongoDB:          8.0.15
Using Mongosh:          2.5.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-10-19T19:24:25.046+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-10-19T19:24:25.048+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.048+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.049+00:00: We suggest setting the contents of sysfsFile to 0.
   2025-10-19T19:24:25.050+00:00: vm.max_map_count is too low
   2025-10-19T19:24:25.050+00:00: We suggest setting swappiness to 0 or 1, as swapping can cause performance problems.
------

test> rs.initiate({
...   _id: "cfgRepl",
...   configsvr: true,
...   members: [
...     {_id: 0, host: "cfg1:27019"},
...     {_id: 1, host: "cfg2:27020"},
...     {_id: 2, host: "cfg3:27021"}
...   ]
... })
{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1760902096, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('AAAAAAAAAAAAAAAAAAAAAAAAAAA=', 0),
      keyId: Long('0')
    }
  },
  operationTime: Timestamp({ t: 1760902096, i: 1 })
}
cfgRepl [direct: secondary] test> exit
```

Подключаемся к первому узлу первого шарда и настраиваем его репликацию:

```JS
docker exec -it mongodb-shard1_1-1 mongosh --port 27031
Current Mongosh Log ID: 68f53befab792c3b44ce5f46
Connecting to:          mongodb://127.0.0.1:27031/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
Using MongoDB:          8.0.15
Using Mongosh:          2.5.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-10-19T19:24:25.088+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-10-19T19:24:25.090+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.090+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.090+00:00: We suggest setting the contents of sysfsFile to 0.
   2025-10-19T19:24:25.091+00:00: vm.max_map_count is too low
   2025-10-19T19:24:25.092+00:00: We suggest setting swappiness to 0 or 1, as swapping can cause performance problems.
------

test> rs.initiate({
...   _id: "shard1",
...   members: [
...     {_id: 0, host: "shard1_1:27031"},
...     {_id: 1, host: "shard1_2:27032"},
...     {_id: 2, host: "shard1_3:27033"}
...   ]
... })
{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1760902134, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('AAAAAAAAAAAAAAAAAAAAAAAAAAA=', 0),
      keyId: Long('0')
    }
  },
  operationTime: Timestamp({ t: 1760902134, i: 1 })
}
shard1 [direct: secondary] test> exit

```
Подключаемся к первому узлу второго шарда и настраиваем его репликацию:
```JS
user@fedora:~/WorkNew/mongodb$ docker exec -it mongodb-shard2_1-1 mongosh --port 27031
Current Mongosh Log ID: 68f53c80564ecfda56ce5f46
Connecting to:          mongodb://127.0.0.1:27031/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
MongoNetworkError: connect ECONNREFUSED 127.0.0.1:27031
user@fedora:~/WorkNew/mongodb$ docker exec -it mongodb-shard2_1-1 mongosh --port 27041
Current Mongosh Log ID: 68f53c820bc83afee9ce5f46
Connecting to:          mongodb://127.0.0.1:27041/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
Using MongoDB:          8.0.15
Using Mongosh:          2.5.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-10-19T19:24:25.035+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-10-19T19:24:25.036+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.036+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-10-19T19:24:25.036+00:00: We suggest setting the contents of sysfsFile to 0.
   2025-10-19T19:24:25.036+00:00: vm.max_map_count is too low
   2025-10-19T19:24:25.036+00:00: We suggest setting swappiness to 0 or 1, as swapping can cause performance problems.
------

test> rs.initiate({
...   _id: "shard2",
...   members: [
...     {_id: 0, host: "shard2_1:27041"},
...     {_id: 1, host: "shard2_2:27042"},
...     {_id: 2, host: "shard2_3:27043"}
...   ]
... })
{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1760902293, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('AAAAAAAAAAAAAAAAAAAAAAAAAAA=', 0),
      keyId: Long('0')
    }
  },
  operationTime: Timestamp({ t: 1760902293, i: 1 })
}
shard2 [direct: other] test> exit

```
Подключаемся к роутеру mongos — он объединяет все шарды в единую точку входа.
```JS

user@fedora:~/WorkNew/mongodb$ docker exec -it mongodb-mongos-1 mongosh --port 27017
Current Mongosh Log ID: 68f53cd0461df1eaf5ce5f46
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
Using MongoDB:          8.0.15
Using Mongosh:          2.5.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-10-19T19:24:36.486+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
------

[direct: mongos] test> sh.addShard("shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033")
... sh.addShard("shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043")
... sh.status()
shardingVersion
{ _id: 1, clusterId: ObjectId('68f53bdb70855672e905c33f') }
---
shards
[
  {
    _id: 'shard1',
    host: 'shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033',
    state: 1,
    topologyTime: Timestamp({ t: 1760902364, i: 4 }),
    replSetConfigVersion: Long('-1')
  },
  {
    _id: 'shard2',
    host: 'shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043',
    state: 1,
    topologyTime: Timestamp({ t: 1760902364, i: 24 }),
    replSetConfigVersion: Long('-1')
  }
]
---
active mongoses
[ { '8.0.15': 1 } ]
---
autosplit
{ 'Currently enabled': 'yes' }
---
balancer
{
  'Currently enabled': 'yes',
  'Failed balancer rounds in last 5 attempts': 0,
  'Currently running': 'no',
  'Migration Results for the last 24 hours': 'No recent migrations'
}
---
shardedDataDistribution
[]
---
databases
[
  {
    database: { _id: 'config', primary: 'config', partitioned: true },
    collections: {}
  }
]

```

Теперь создаём базу stocks и включаем для неё шардирование:

```JS


[direct: mongos] test> sh.enableSharding("stocks")
... sh.shardCollection("stocks.values", { stock_symbol: 1 })
... 
{
  collectionsharded: 'stocks.values',
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1760902374, i: 48 }),
    signature: {
      hash: Binary.createFromBase64('AAAAAAAAAAAAAAAAAAAAAAAAAAA=', 0),
      keyId: Long('0')
    }
  },
  operationTime: Timestamp({ t: 1760902374, i: 47 })
}
[direct: mongos] test> 

```
