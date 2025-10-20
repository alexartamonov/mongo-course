# Настройка кластера

Запускаем docker-compose вместе с шардированным кластером

```yaml
version: '3.9'

services:
  # ========================
  # Config Servers
  # ========================
  cfg1:
    image: mongo:8.0
    command: ["mongod", "--configsvr", "--replSet", "cfgRepl", "--port", "27019", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - cfg1_data:/data/db
    networks:
      - mongo-cluster

  cfg2:
    image: mongo:8.0
    command: ["mongod", "--configsvr", "--replSet", "cfgRepl", "--port", "27020", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - cfg2_data:/data/db
    networks:
      - mongo-cluster

  cfg3:
    image: mongo:8.0
    command: ["mongod", "--configsvr", "--replSet", "cfgRepl", "--port", "27021", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - cfg3_data:/data/db
    networks:
      - mongo-cluster

  # ========================
  # Shard 1
  # ========================
  shard1_1:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard1", "--port", "27031", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard1_1_data:/data/db
    networks:
      - mongo-cluster

  shard1_2:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard1", "--port", "27032", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard1_2_data:/data/db
    networks:
      - mongo-cluster

  shard1_3:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard1", "--port", "27033", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard1_3_data:/data/db
    networks:
      - mongo-cluster

  # ========================
  # Shard 2
  # ========================
  shard2_1:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard2", "--port", "27041", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard2_1_data:/data/db
    networks:
      - mongo-cluster

  shard2_2:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard2", "--port", "27042", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard2_2_data:/data/db
    networks:
      - mongo-cluster

  shard2_3:
    image: mongo:8.0
    command: ["mongod", "--shardsvr", "--replSet", "shard2", "--port", "27043", "--bind_ip_all", "--dbpath", "/data/db"]
    volumes:
      - shard2_3_data:/data/db
    networks:
      - mongo-cluster

  # ========================
  # Mongos (query router)
  # ========================
  mongos:
    image: mongo:8.0
    command: >
      mongos
      --configdb cfgRepl/cfg1:27019,cfg2:27020,cfg3:27021
      --bind_ip_all
      --port 27017
    depends_on:
      - cfg1
      - cfg2
      - cfg3
      - shard1_1
      - shard1_2
      - shard1_3
      - shard2_1
      - shard2_2
      - shard2_3
    ports:
      - "27017:27017"
    networks:
      - mongo-cluster

volumes:
  cfg1_data:
  cfg2_data:
  cfg3_data:
  shard1_1_data:
  shard1_2_data:
  shard1_3_data:
  shard2_1_data:
  shard2_2_data:
  shard2_3_data:

networks:
  mongo-cluster:
    driver: bridge

```

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
Подключаемся к роутеру mongos - он объединяет все шарды в единую точку входа.
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

# Заполнение базы данных товаров

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

sh.status() показал что всё хорошо и наш кластер настроен


Вставляем данные в нашу коллекцию 

```JS

db.products.insertMany([  
   { _id: 1,  name: "Игровой ноутбук",       price: 20000, description: "Игровой ноутбук - последняя новинка от известного бренда" },  
   { _id: 2,  name: "Компьютерная мышка",    price: 500,   description: "Надежное устройство для повседневной работы" },  
   { _id: 3,  name: "Механическая клавиатура", price: 3500, description: "Клавиатура с подсветкой и тактильной отдачей" },  
   { _id: 4,  name: "Монитор 27\"",          price: 12000, description: "Монитор высокого разрешения для работы и игр" },  
   { _id: 5,  name: "Наушники с микрофоном", price: 2500,  description: "Подходят для работы и онлайн-общения" },  
   { _id: 6,  name: "Веб-камера FullHD",     price: 1800,  description: "Камера для видеозвонков и стриминга" },  
   { _id: 7,  name: "МФУ принтер",           price: 7000,  description: "Лазерный принтер с функцией сканирования" },  
   { _id: 8,  name: "Игровое кресло",        price: 15000, description: "Эргономичное кресло для геймеров" },  
   { _id: 9,  name: "Жесткий диск 2TB",      price: 4500,  description: "Надежное хранилище данных" },  
   { _id: 10, name: "SSD 1TB",               price: 8500,  description: "Быстрый твердотельный накопитель" },  
   { _id: 11, name: "Смарт-часы",            price: 9000,  description: "Фитнес-браслет с расширенными функциями" },  
   { _id: 12, name: "Планшет 10\"",          price: 18000, description: "Планшет с высоким разрешением экрана" },  
   { _id: 13, name: "Смартфон",              price: 30000, description: "Флагманский смартфон последнего поколения" },  
   { _id: 14, name: "Роутер Wi-Fi 6",        price: 3500,  description: "Высокоскоростной маршрутизатор для дома" },  
   { _id: 15, name: "Беспроводные наушники", price: 6000,  description: "Компактные и удобные наушники" },  
   { _id: 16, name: "Колонка Bluetooth",     price: 4000,  description: "Портативная колонка с отличным звуком" },  
   { _id: 17, name: "Внешний аккумулятор",   price: 2500,  description: "Powerbank на 20000 мАч" },  
   { _id: 18, name: "Флешка 128GB",          price: 1200,  description: "USB накопитель для быстрого обмена данными" },  
   { _id: 19, name: "Видеокарта",            price: 45000, description: "Игровая видеокарта последнего поколения" },  
   { _id: 20, name: "Материнская плата",     price: 15000, description: "Материнская плата с поддержкой новейших процессоров" },  
 ])  

```

Проверяем статус шардирования данных 

```JS

[direct: mongos] stocks> db.products.getShardDistribution()
... 
MongoshInvalidInputError: [SHAPI-10001] Collection products is not sharded

```

Данные лежат на одной шарде - это сделано специально. Я размышлял, как лучше шардировать базу данных, и для меня наилучшим решением, исходя из второго задания, было шардировать именно остатки по складам.
Наименований товаров может быть, например, около 10 000, и важно разложить остатки по шардам, чтобы избежать проблем с поиском. Например, если пользователь захочет посмотреть товары дороже 5 000 рублей, и эти товары будут на разных шардах, системе придётся обходить их все, что замедлит работу.
Поэтому я решил сделать так: коллекцию products оставить как основную на одном шарде, а уже остатки по складам разложить по шардам.


А теперь заполним коллекцию inventory - добавим туда city_id и заполним записями:

```JS


db.inventory.insertMany(
 [{ product_id: 1, warehouse_id: 1, city_id: 1, quantity: 5 },
  { product_id: 2, warehouse_id: 1, city_id: 1, quantity: 0 },
  { product_id: 3, warehouse_id: 2, city_id: 1, quantity: 8 },
  { product_id: 4, warehouse_id: 2, city_id: 1, quantity: 7 },
  { product_id: 5, warehouse_id: 3, city_id: 2, quantity: 0 },
  { product_id: 6, warehouse_id: 3, city_id: 2, quantity: 3 },
  { product_id: 7, warehouse_id: 4, city_id: 2, quantity: 6 },
  { product_id: 8, warehouse_id: 4, city_id: 2, quantity: 1 },
  { product_id: 9, warehouse_id: 5, city_id: 3, quantity: 8 },
  { product_id: 10, warehouse_id: 5, city_id: 3, quantity: 4 },
  { product_id: 11, warehouse_id: 6, city_id: 3, quantity: 9 },
  { product_id: 12, warehouse_id: 6, city_id: 3, quantity: 0 },
  { product_id: 13, warehouse_id: 7, city_id: 4, quantity: 15 },
  { product_id: 14, warehouse_id: 7, city_id: 4, quantity: 7 },
  { product_id: 15, warehouse_id: 8, city_id: 4, quantity: 0 },
  { product_id: 16, warehouse_id: 8, city_id: 4, quantity: 2 },
  { product_id: 17, warehouse_id: 9, city_id: 5, quantity: 6 },
  { product_id: 18, warehouse_id: 9, city_id: 5, quantity: 0 },
  { product_id: 19, warehouse_id: 10, city_id: 5, quantity: 11 },
  { product_id: 20, warehouse_id: 10, city_id: 5, quantity: 5 }])
  
  {
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('68f55f89461df1eaf5ce5f47'),
    '1': ObjectId('68f55f89461df1eaf5ce5f48'),
    '2': ObjectId('68f55f89461df1eaf5ce5f49'),
    '3': ObjectId('68f55f89461df1eaf5ce5f4a'),
    '4': ObjectId('68f55f89461df1eaf5ce5f4b'),
    '5': ObjectId('68f55f89461df1eaf5ce5f4c'),
    '6': ObjectId('68f55f89461df1eaf5ce5f4d'),
    '7': ObjectId('68f55f89461df1eaf5ce5f4e'),
    '8': ObjectId('68f55f89461df1eaf5ce5f4f'),
    '9': ObjectId('68f55f89461df1eaf5ce5f50'),
    '10': ObjectId('68f55f89461df1eaf5ce5f51'),
    '11': ObjectId('68f55f89461df1eaf5ce5f52'),
    '12': ObjectId('68f55f89461df1eaf5ce5f53'),
    '13': ObjectId('68f55f89461df1eaf5ce5f54'),
    '14': ObjectId('68f55f89461df1eaf5ce5f55'),
    '15': ObjectId('68f55f89461df1eaf5ce5f56'),
    '16': ObjectId('68f55f89461df1eaf5ce5f57'),
    '17': ObjectId('68f55f89461df1eaf5ce5f58'),
    '18': ObjectId('68f55f89461df1eaf5ce5f59'),
    '19': ObjectId('68f55f89461df1eaf5ce5f5a')
  }
}
```

# Создание и заполнение шардированной коллекции документов

Используем hash-based sharding, по причине - мы стремимся равномерно распределить по шардам данные о наличии товаров на складе. 

```JS
[direct: mongos] stocks> db.inventory.createIndex({ city_id: "hashed" })
city_id_hashed
[direct: mongos] stocks> sh.shardCollection("stocks.inventory", { city_id: "hashed" })
{
  collectionsharded: 'stocks.inventory',
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1760911430, i: 41 }),
    signature: {
      hash: Binary.createFromBase64('AAAAAAAAAAAAAAAAAAAAAAAAAAA=', 0),
      keyId: Long('0')
    }
  },
  operationTime: Timestamp({ t: 1760911430, i: 41 })
}
```

Сначала я увидел что данные лежат все в одной шарде 

```JS
[direct: mongos] stocks> db.inventory.getShardDistribution()
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '1KiB',
  docs: 20,
  chunks: 1,
  'estimated data per chunk': '1KiB',
  'estimated docs per chunk': 20
}
---
Totals
{
  data: '1KiB',
  docs: 20,
  chunks: 1,
  'Shard shard2': [
    '100 % data',
    '100 % docs in cluster',
    '83B avg obj size on shard'
  ]
}
```

Но я подумал, может быть данных мало для шардирования и решил через python на генерировать данных 

```JS
[direct: mongos] stocks> db.inventory.getShardDistribution()
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '396.09MiB',
  docs: 5004000,
  chunks: 1,
  'estimated data per chunk': '396.09MiB',
  'estimated docs per chunk': 5004000
}
---
Totals
{
  data: '396.09MiB',
  docs: 5004000,
  chunks: 1,
  'Shard shard2': [
    '100 % data',
    '100 % docs in cluster',
    '83B avg obj size on shard'
  ]
}

```

Но потом через некоторое время я увидел, я правда пока эксперементировал - удалил первоначальную коллекцию и включил шардирование во время вставки данных (10 млн записей), решил подождать, так ещё интересенее

```JS
[direct: mongos] stocks> db.inventory.getShardDistribution()
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '325.76MiB',
  docs: 5132876,
  chunks: 1,
  'estimated data per chunk': '325.76MiB',
  'estimated docs per chunk': 5132876
}
---
Shard shard1 at shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033
{
  data: '81.43MiB',
  docs: 1028859,
  chunks: 1,
  'estimated data per chunk': '81.43MiB',
  'estimated docs per chunk': 1028859
}
---
Totals
{
  data: '407.2MiB',
  docs: 6161735,
  chunks: 2,
  'Shard shard2': [
    '80 % data',
    '83.3 % docs in cluster',
    '83B avg obj size on shard'
  ],
  'Shard shard1': [
    '19.99 % data',
    '16.69 % docs in cluster',
    '83B avg obj size on shard'
  ]
}

```

Попробуем посмотреть какая ситуация у нас с городами, я планировал, что будет распределение равномерно.

```JS

[direct: mongos] stocks> 
... db.inventory.aggregate([
...   {
...     $group: {
...       _id: "$city_id",        
...       count: { $sum: 1 }      
...     }
...   },
...   { 
...     $sort: { _id: 1 }        
...   }
... ])

[
  { _id: 1, count: 1041063 },
  { _id: 2, count: 1041786 },
  { _id: 3, count: 1041097 },
  { _id: 4, count: 1040491 },
  { _id: 5, count: 1042434 }
]


```

Пока наблюдаю картинку 20% на 80% примерно, я устал ждать пока вставка 10 млн документов закончится, оставим на 6282364

```JS
[direct: mongos] stocks> db.inventory.getShardDistribution()
Shard shard1 at shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033
{
  data: '83.32MiB',
  docs: 1052736,
  chunks: 1,
  'estimated data per chunk': '83.32MiB',
  'estimated docs per chunk': 1052736
}
---
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '333.42MiB',
  docs: 5229628,
  chunks: 1,
  'estimated data per chunk': '333.42MiB',
  'estimated docs per chunk': 5229628
}
---
Totals
{
  data: '416.75MiB',
  docs: 6282364,
  chunks: 2,
  'Shard shard1': [
    '19.99 % data',
    '16.75 % docs in cluster',
    '83B avg obj size on shard'
  ],
  'Shard shard2': [
    '80 % data',
    '83.24 % docs in cluster',
    '83B avg obj size on shard'
  ]
}
```

Сделал новую коллекцию inventory2 и записал туда ещё несколько десятков тысяч записей.
Индекс и шардирование включил сразу.
```JS
[direct: mongos] stocks> db.inventory2.getShardDistribution()
Shard shard1 at shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033
{
  data: '2.52MiB',
  docs: 31876,
  chunks: 1,
  'estimated data per chunk': '2.52MiB',
  'estimated docs per chunk': 31876
}
---
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '628KiB',
  docs: 7755,
  chunks: 1,
  'estimated data per chunk': '628KiB',
  'estimated docs per chunk': 7755
}
---
Totals
{
  data: '3.13MiB',
  docs: 39631,
  chunks: 2,
  'Shard shard1': [
    '80.43 % data',
    '80.43 % docs in cluster',
    '83B avg obj size on shard'
  ],
  'Shard shard2': [
    '19.56 % data',
    '19.56 % docs in cluster',
    '83B avg obj size on shard'
  ]
}
```

 Возможно проблема в ключе city_id попробую сейчас - увеличить количество city_id до 50
 
 
```JS

[direct: mongos] stocks> db.inventory2.getShardDistribution()
Shard shard1 at shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033
{
  data: '3.16MiB',
  docs: 39960,
  chunks: 1,
  'estimated data per chunk': '3.16MiB',
  'estimated docs per chunk': 39960
}
---
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '949KiB',
  docs: 11713,
  chunks: 1,
  'estimated data per chunk': '949KiB',
  'estimated docs per chunk': 11713
}
---
Totals
{
  data: '4.08MiB',
  docs: 51673,
  chunks: 2,
  'Shard shard1': [
    '77.33 % data',
    '77.33 % docs in cluster',
    '83B avg obj size on shard'
  ],
  'Shard shard2': [
    '22.66 % data',
    '22.66 % docs in cluster',
    '83B avg obj size on shard'
  ]
}

```

Наблюдаю увеличение количества записей на 2-ой шарде. Вывод что при малом количестве city_id - скорее всего нужно не через hashed, а указывать на какой shard нужно записывать через range.

Начинаем процесс заново

```JS
db.inventory.drop()
db.inventory2.drop()
```

Распределение документов после увеличения до 50 количества городов

```JS
[direct: mongos] stocks>
... let count = db.inventory.countDocuments({ city_id: { $in: [1, 10, 15] } });
... print("Query 1:", count);
... count = db.inventory.countDocuments({ city_id: { $gte: 20, $lte: 29 } });
... print("Query 2:", count);
... count = db.inventory.countDocuments({ city_id: 10 });
... print("Query 3:", count);
... count = db.inventory.countDocuments({ city_id: 1 });
... print("Query 4:", count);
... count = db.inventory.countDocuments({ city_id: 15 });
... print("Query 5:", count);
... count = db.inventory.countDocuments({ city_id: { $gte: 20, $lte: 29 } });
... print("Query 6:", count);
... count = db.inventory.countDocuments({ city_id: { $gte: 20, $lte: 29 } });
... print("Query 7:", count);
Query 1: 6070
Query 2: 19946
Query 3: 2087
Query 4: 2009
Query 5: 1974
Query 6: 19946
Query 7: 19946

```

Видно что они распределились равномерно, это идеальная ситуация

Но по шардам они распределились не так однородно.

```JS
[direct: mongos] stocks> db.inventory.getShardDistribution()
Shard shard1 at shard1/shard1_1:27031,shard1_2:27032,shard1_3:27033
{
  data: '4.92MiB',
  docs: 62208,
  chunks: 1,
  'estimated data per chunk': '4.92MiB',
  'estimated docs per chunk': 62208
}Вывод: для hash-sharding важно иметь достаточно разнообразный ключ, чтобы балансировщик мог равномерно распределять чанки. При небольших данных лучше использовать range-based sharding, чтобы явно указать, какие документы на каком шарде.
---
Shard shard2 at shard2/shard2_1:27041,shard2_2:27042,shard2_3:27043
{
  data: '2.99MiB',
  docs: 37792,
  chunks: 1,
  'estimated data per chunk': '2.99MiB',
  'estimated docs per chunk': 37792
}
---
Totals
{
  data: '7.91MiB',
  docs: 100000,
  chunks: 2,
  'Shard shard1': [
    '62.2 % data',
    '62.2 % docs in cluster',
    '83B avg obj size on shard'
  ],
  'Shard shard2': [
    '37.79 % data',
    '37.79 % docs in cluster',
    '83B avg obj size on shard'
  ]
}
```

Вывод: для hash-sharding важно иметь большое разнообразие ключей, если ключи имеют несколько заранее известных значений, лучше использовать range-based sharding и явно указать, какие документы, на каком шарде.


# Cравнение производительности:

Сделаем не шардированную копию коллекции 
```JS
[direct: mongos] stocks> db.inventory.aggregate([
...     { $match: {} }, 
...     { $out: "inventory_copy" } 
... ])

[direct: mongos] stocks> db.inventory_copy.getShardDistribution()
MongoshInvalidInputError: [SHAPI-10001] Collection inventory_copy is not sharded
```

Сравним производительность у обоих коллекций с разными условиями, напишем функцию на основе агрегации из [работы №2](task2.md)

```JS
function aggregateByCity(collectionName, cityFilter) {
    const start = new Date();
    const result = db.getCollection(collectionName).aggregate([
        { $match: { city_id: cityFilter } },
        { $group: { _id: "$product_id", totalQuantity: { $sum: "$quantity" } } },
        { $lookup: { from: "products", localField: "_id", foreignField: "_id", as: "product" } },
        { $unwind: "$product" },
        { $project: { _id: 0, name: "$product.name", totalQuantity: 1 } },
        { $sort: { totalQuantity: -1 } }
    ]).toArray();
}

const testFilters = [
    { filter: { $in: [1, 10, 15] }, label: "Query 1: city_id in [1,10,15]" },
    { filter: { $gte: 20, $lte: 29 }, label: "Query 2: 20 <= city_id <= 29" },
    { filter: 10, label: "Query 3: city_id = 10" },
    { filter: 1, label: "Query 4: city_id = 1" },
    { filter: 15, label: "Query 5: city_id = 15" },
    { filter: { $lte: 25 }, label: "Query 6: city_id <= 25" },
    { filter: { $gt: 25 }, label: "Query 7: city_id > 25" },
    { filter: { $in: [2, 4, 6] }, label: "Query 8: city_id in [2,4,6]" },
    { filter: { $in: [7, 8, 9] }, label: "Query 9: city_id in [7,8,9]" },
    { filter: { $in: [3, 5, 10] }, label: "Query 10: city_id in [3,5,10]" },
    { filter: { $gte: 1 }, label: "Query 11: city_id >= 1" }
];

function runTests(collectionName) {
    print("=== Testing collection:", collectionName, "===");

    testFilters.forEach(tf => {
        let sumTime = 0;
        for (let i = 0; i < 5; i++) { 
            const start = new Date();
            aggregateByCity(collectionName, tf.filter, tf.label); 
            const end = new Date();
            sumTime += (end - start);
        }
        const avgTime = sumTime / 5;
        print(tf.label, "- average time over 5 runs:", avgTime, "ms");
    });
}



runTests("inventory");
runTests("inventory_copy");


=== Testing collection: inventory ===
Query 1: city_id in [1,10,15] - average time over 5 runs: 20.4 ms
Query 2: 20 <= city_id <= 29 - average time over 5 runs: 36.4 ms
Query 3: city_id = 10 - average time over 5 runs: 10.2 ms
Query 4: city_id = 1 - average time over 5 runs: 9.8 ms
Query 5: city_id = 15 - average time over 5 runs: 9.6 ms
Query 6: city_id <= 25 - average time over 5 runs: 91.6 ms
Query 7: city_id > 25 - average time over 5 runs: 63.2 ms
Query 8: city_id in [2,4,6] - average time over 5 runs: 16 ms
Query 9: city_id in [7,8,9] - average time over 5 runs: 16.8 ms
Query 10: city_id in [3,5,10] - average time over 5 runs: 17.4 ms
Query 11: city_id >= 1 - average time over 5 runs: 149 ms
=== Testing collection: inventory_copy ===
Query 1: city_id in [1,10,15] - average time over 5 runs: 35.2 ms
Query 2: 20 <= city_id <= 29 - average time over 5 runs: 36.8 ms
Query 3: city_id = 10 - average time over 5 runs: 33.4 ms
Query 4: city_id = 1 - average time over 5 runs: 32.2 ms
Query 5: city_id = 15 - average time over 5 runs: 31 ms
Query 6: city_id <= 25 - average time over 5 runs: 37.8 ms
Query 7: city_id > 25 - average time over 5 runs: 37.8 ms
Query 8: city_id in [2,4,6] - average time over 5 runs: 34.4 ms
Query 9: city_id in [7,8,9] - average time over 5 runs: 33.8 ms
Query 10: city_id in [3,5,10] - average time over 5 runs: 35.6 ms
Query 11: city_id >= 1 - average time over 5 runs: 43.6 ms

```
# Вывод

Наибольшая просадка наблюдается, когда в запросе используется большое количество city_id. Наиболее выражена она в 11-м запросе, где выборка охватывает все города. Как видно, шардирование ускоряет выборку данных при запросах по одному городу. Однако, если данные затрагивают сразу несколько городов или все города, задержка увеличивается примерно в 5 раз.

В целом, по результатам данной работы можно сделать вывод: шардирование должно применяться осознанно, с учетом бизнес-задач. Например, для интернет-магазина, где пользователь выбирает товары и видит остатки по конкретному городу, шардирование по городу может быть удобным и даже ускорять работу. Но если пользователь ищет данные по географическим критериям (например, «Москва +300 км»), такая организация данных может наоборот замедлить поиск или выдачу информации.
