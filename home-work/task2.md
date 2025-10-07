## Задание


1. Развернуть ВМ (Linux) с MongoDB (у вас есть ВМ в ВБ, любой другой способ, в т.ч. докер)
2. Создать 2 коллекции (10-20 строк) с товарами и складами их наличием
3. Или написать Map Reduce по подсчёту количества товаров по наименованиям
(просуммировать с разных складов)
4. Или реализовать на aggregation framework
5. Не забываем ВМ остановить/удалить
## Подготовка окружения

Запуск базы данных в докере:

```bash
user@fedora:~$ docker run -d --name mongodb -p 27017:27017 mongo:6  
Unable to find image 'mongo:6' locally  
6: Pulling from library/mongo  
af6eca94c810: Pull complete    
67085ad3d402: Pull complete    
de5d534c2479: Pull complete    
5fe433ce8dd2: Pull complete    
067ec5108522: Pull complete    
5b83cbb4ca6b: Pull complete    
4fad5f8c2622: Pull complete    
e7273bd7e554: Pull complete    
Digest: sha256:3ed29f0e1a0dc571357388f66c63fcb046dbea7beaf3c80015f41357b94156f9  
Status: Downloaded newer image for mongo:6  
7dc500973071965534d97899cd7db22ac11d4694e21c351e3c68a797de3aada5
```

Подключаемся к базе данных через докер:

```sh
user@fedora:~$ docker exec -it mongodb mongosh  
Current Mongosh Log ID: 68e038934fadcd12c0ce5f46  
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8  
Using MongoDB:          6.0.26  
Using Mongosh:          2.5.8  
  
For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/  

test>
```

## Заполнение базы данных информацией

Переключаемся с test на store 

```
test> use store  
switched to db store  
store>
```

Но перед тем как заполнять базу данных, необходимо определиться с тем, что мы в ней будем хранить, в данном задании нам необходимо работать с товарами и складами.

Модель товара (`products`)

| Поле        | Тип данных | Описанипе            |
| ----------- | ---------- | -------------------- |
| id          | числовой   | идентификатор товара |
| name        | текстовый  | наименование товара  |
| price       | числовой   | цена товара          |
| description | текстовый  | описание товара      |

Модель склада (`warehouses`)

| Поле       | Тип данных | Описанипе            |
| ---------- | ---------- | -------------------- |
| name       | текстовый  | наименование товара  |
| address    | текстовый  | адрес склада         |
| product_id | числовой   | идентификатор товара |
Предположим, что у нас магазин электроники и бытовой техники и заполним таблицу товаров -

```js
db.products.insertMany([  
...   { _id: 1,  name: "Игровой ноутбук",       price: 20000, description: "Игровой ноутбук - последняя новинка от известного бренда" },  
...   { _id: 2,  name: "Компьютерная мышка",    price: 500,   description: "Надежное устройство для повседневной работы" },  
...   { _id: 3,  name: "Механическая клавиатура", price: 3500, description: "Клавиатура с подсветкой и тактильной отдачей" },  
...   { _id: 4,  name: "Монитор 27\"",          price: 12000, description: "Монитор высокого разрешения для работы и игр" },  
...   { _id: 5,  name: "Наушники с микрофоном", price: 2500,  description: "Подходят для работы и онлайн-общения" },  
...   { _id: 6,  name: "Веб-камера FullHD",     price: 1800,  description: "Камера для видеозвонков и стриминга" },  
...   { _id: 7,  name: "МФУ принтер",           price: 7000,  description: "Лазерный принтер с функцией сканирования" },  
...   { _id: 8,  name: "Игровое кресло",        price: 15000, description: "Эргономичное кресло для геймеров" },  
...   { _id: 9,  name: "Жесткий диск 2TB",      price: 4500,  description: "Надежное хранилище данных" },  
...   { _id: 10, name: "SSD 1TB",               price: 8500,  description: "Быстрый твердотельный накопитель" },  
...   { _id: 11, name: "Смарт-часы",            price: 9000,  description: "Фитнес-браслет с расширенными функциями" },  
...   { _id: 12, name: "Планшет 10\"",          price: 18000, description: "Планшет с высоким разрешением экрана" },  
...   { _id: 13, name: "Смартфон",              price: 30000, description: "Флагманский смартфон последнего поколения" },  
...   { _id: 14, name: "Роутер Wi-Fi 6",        price: 3500,  description: "Высокоскоростной маршрутизатор для дома" },  
...   { _id: 15, name: "Беспроводные наушники", price: 6000,  description: "Компактные и удобные наушники" },  
...   { _id: 16, name: "Колонка Bluetooth",     price: 4000,  description: "Портативная колонка с отличным звуком" },  
...   { _id: 17, name: "Внешний аккумулятор",   price: 2500,  description: "Powerbank на 20000 мАч" },  
...   { _id: 18, name: "Флешка 128GB",          price: 1200,  description: "USB накопитель для быстрого обмена данными" },  
...   { _id: 19, name: "Видеокарта",            price: 45000, description: "Игровая видеокарта последнего поколения" },  
...   { _id: 20, name: "Материнская плата",     price: 15000, description: "Материнская плата с поддержкой новейших процессоров" }  
... ])  
...    
{  
 acknowledged: true,  
 insertedIds: {  
   '0': 1,  
   '1': 2,  
   '2': 3,  
   '3': 4,  
   '4': 5,  
   '5': 6,  
   '6': 7,  
   '7': 8,  
   '8': 9,  
   '9': 10,  
   '10': 11,  
   '11': 12,  
   '12': 13,  
   '13': 14,  
   '14': 15,  
   '15': 16,  
   '16': 17,  
   '17': 18,  
   '18': 19,  
   '19': 20  
 }  
}
```

Проверим результат вставки через запрос 

```js

db.products.find()  
[  
 {  
   _id: 1,  
   name: 'Игровой ноутбук',  
   price: 20000,  
   description: 'Игровой ноутбук - последняя новинка от известного бренда'  
 },  
 {  
   _id: 2,  
   name: 'Компьютерная мышка',  
   price: 500,  
   description: 'Надежное устройство для повседневной работы'  
 },  
 {  
   _id: 3,  
   name: 'Механическая клавиатура',  
   price: 3500,  
   description: 'Клавиатура с подсветкой и тактильной отдачей'  
 },  
 {  
   _id: 4,  
   name: 'Монитор 27"',  
   price: 12000,  
   description: 'Монитор высокого разрешения для работы и игр'  
 },
/// Другие товары
]
```

Заполним коллекцию со складами - 

```JS
db.warehouses.insertMany([  
...   { _id: 1, name: "Склад Москва",       address: "Москва, ул. Первая, 10" },  
...   { _id: 2, name: "Склад Санкт-Петербург", address: "Санкт-Петербург, ул. Вторая, 20" },  
...   { _id: 3, name: "Склад Казань",       address: "Казань, ул. Третья, 5" },  
...   { _id: 4, name: "Склад Новосибирск",  address: "Новосибирск, ул. Четвертая, 15" },  
...   { _id: 5, name: "Склад Екатеринбург", address: "Екатеринбург, ул. Мира, 50" },  
...   { _id: 6, name: "Склад Ростов-на-Дону", address: "Ростов-на-Дону, ул. Садовая, 12" },  
...   { _id: 7, name: "Склад Самара",       address: "Самара, ул. Московская, 22" },  
...   { _id: 8, name: "Склад Владивосток",  address: "Владивосток, ул. Восточная, 30" },  
...   { _id: 9, name: "Склад Пермь",        address: "Пермь, ул. Пермская, 77" },  
...   { _id: 10,name: "Склад Краснодар",    address: "Краснодар, ул. Березовая, 100" }  
... ]  
...    
... )  
{  
 acknowledged: true,  
 insertedIds: {  
   '0': 1,  
   '1': 2,  
   '2': 3,  
   '3': 4,  
   '4': 5,  
   '5': 6,  
   '6': 7,  
   '7': 8,  
   '8': 9,  
   '9': 10  
 }  
}
```

Для учета количества товаров на складах - введем дополнительную колекцию Inventory`

```js
db.inventory.insertMany([  
...   { warehouse_id: 1, product_id: 1, quantity: 5 },  
...   { warehouse_id: 1, product_id: 2, quantity: 0 },  
...   { warehouse_id: 1, product_id: 3, quantity: 8 },  
...    
...   { warehouse_id: 2, product_id: 1, quantity: 2 },  
...   { warehouse_id: 2, product_id: 4, quantity: 7 },  
...   { warehouse_id: 2, product_id: 5, quantity: 0 },  
...    
...   { warehouse_id: 3, product_id: 2, quantity: 10 },  
...   { warehouse_id: 3, product_id: 6, quantity: 3 },  
...    
...   { warehouse_id: 4, product_id: 7, quantity: 6 },  
...   { warehouse_id: 4, product_id: 8, quantity: 1 },  
...   { warehouse_id: 4, product_id: 9, quantity: 0 },  
...    
...   { warehouse_id: 5, product_id: 10, quantity: 4 },  
...   { warehouse_id: 5, product_id: 11, quantity: 9 },  
...    
...   { warehouse_id: 6, product_id: 12, quantity: 0 },  
...   { warehouse_id: 6, product_id: 13, quantity: 15 },  
...    
...   { warehouse_id: 7, product_id: 14, quantity: 7 },  
...   { warehouse_id: 7, product_id: 15, quantity: 0 },  
...   { warehouse_id: 7, product_id: 16, quantity: 2 },  
...    
...   { warehouse_id: 8, product_id: 17, quantity: 6 },  
...   { warehouse_id: 8, product_id: 18, quantity: 0 },  
...    
...   { warehouse_id: 9, product_id: 19, quantity: 11 },  
...   { warehouse_id: 9, product_id: 20, quantity: 5 },  
...    
...   { warehouse_id: 10, product_id: 1, quantity: 0 },  
...   { warehouse_id: 10, product_id: 5, quantity: 3 },  
...   { warehouse_id: 10, product_id: 9, quantity: 8 }  
... ])  
...    
{  
 acknowledged: true,  
 insertedIds: {  
   '0': ObjectId('68e05bb14fadcd12c0ce5f47'),  
   '1': ObjectId('68e05bb14fadcd12c0ce5f48'),  
   '2': ObjectId('68e05bb14fadcd12c0ce5f49'),  
   '3': ObjectId('68e05bb14fadcd12c0ce5f4a'),  
   '4': ObjectId('68e05bb14fadcd12c0ce5f4b'),  
   '5': ObjectId('68e05bb14fadcd12c0ce5f4c'),  
   '6': ObjectId('68e05bb14fadcd12c0ce5f4d'),  
   '7': ObjectId('68e05bb14fadcd12c0ce5f4e'),  
   '8': ObjectId('68e05bb14fadcd12c0ce5f4f'),  
   '9': ObjectId('68e05bb14fadcd12c0ce5f50'),  
   '10': ObjectId('68e05bb14fadcd12c0ce5f51'),  
   '11': ObjectId('68e05bb14fadcd12c0ce5f52'),  
   '12': ObjectId('68e05bb14fadcd12c0ce5f53'),  
   '13': ObjectId('68e05bb14fadcd12c0ce5f54'),  
   '14': ObjectId('68e05bb14fadcd12c0ce5f55'),  
   '15': ObjectId('68e05bb14fadcd12c0ce5f56'),  
   '16': ObjectId('68e05bb14fadcd12c0ce5f57'),  
   '17': ObjectId('68e05bb14fadcd12c0ce5f58'),  
   '18': ObjectId('68e05bb14fadcd12c0ce5f59'),  
   '19': ObjectId('68e05bb14fadcd12c0ce5f5a'),  
   '20': ObjectId('68e05bb14fadcd12c0ce5f5b'),  
   '21': ObjectId('68e05bb14fadcd12c0ce5f5c'),  
   '22': ObjectId('68e05bb14fadcd12c0ce5f5d'),  
   '23': ObjectId('68e05bb14fadcd12c0ce5f5e'),  
   '24': ObjectId('68e05bb14fadcd12c0ce5f5f')  
 }  
}
```

Получение данных через агрегацию - 

```JS
db.inventory.aggregate([  
...   { $group: {  
...       _id: "$product_id",  
...       totalQuantity: { $sum: "$quantity" }  
...   }},  
...   { $lookup: {  
...       from: "products",  
...       localField: "_id",  
...       foreignField: "_id",  
...       as: "product"  
...   }},  
...   { $unwind: "$product" },  
...   { $project: { _id: 0, name: "$product.name", totalQuantity: 1 } },  
...   { $sort: { totalQuantity: -1 } }  
... ])  
...    
[  
 { totalQuantity: 15, name: 'Смартфон' },  
 { totalQuantity: 11, name: 'Видеокарта' },  
 { totalQuantity: 10, name: 'Компьютерная мышка' },  
 { totalQuantity: 9, name: 'Смарт-часы' },  
 { totalQuantity: 8, name: 'Жесткий диск 2TB' },  
 { totalQuantity: 8, name: 'Механическая клавиатура' },  
 { totalQuantity: 7, name: 'Игровой ноутбук' },  
 { totalQuantity: 7, name: 'Монитор 27"' },  
 { totalQuantity: 7, name: 'Роутер Wi-Fi 6' },  
 { totalQuantity: 6, name: 'МФУ принтер' },  
 { totalQuantity: 6, name: 'Внешний аккумулятор' },  
 { totalQuantity: 5, name: 'Материнская плата' },  
 { totalQuantity: 4, name: 'SSD 1TB' },  
 { totalQuantity: 3, name: 'Веб-камера FullHD' },  
 { totalQuantity: 3, name: 'Наушники с микрофоном' },  
 { totalQuantity: 2, name: 'Колонка Bluetooth' },  
 { totalQuantity: 1, name: 'Игровое кресло' },  
 { totalQuantity: 0, name: 'Планшет 10"' },  
 { totalQuantity: 0, name: 'Беспроводные наушники' },  
 { totalQuantity: 0, name: 'Флешка 128GB' }  
]
```

Получение данных через Map Reduce

```JS
var mapFunction = function() {
  emit(this.product_id, this.quantity);
};
```

```js
var reduceFunction = function(key, values) {
  return Array.sum(values);
};
```

```js
db.inventory.mapReduce(
  mapFunction,
  reduceFunction,
  { out: "product_totals" }  
)
```

```js
db.product_totals.find()
```


Запустим 
```JS
store> db.inventory.mapReduce(  
...   mapFunction,  
...   reduceFunction,  
...   { out: "product_totals" }  // создаёт новую коллекцию product_totals  
... )  
...    
DeprecationWarning: Collection.mapReduce() is deprecated. Use an aggregation instead.  
See https://mongodb.com/docs/manual/core/map-reduce for details.  
{ result: 'product_totals', ok: 1 }  
store> db.product_totals.find()  
[  
 { _id: 1, value: 7 },  { _id: 20, value: 5 },  
 { _id: 6, value: 3 },  { _id: 19, value: 11 },  
 { _id: 12, value: 0 }, { _id: 2, value: 10 },  
 { _id: 9, value: 8 },  { _id: 5, value: 3 },  
 { _id: 3, value: 8 },  { _id: 13, value: 15 },  
 { _id: 15, value: 0 }, { _id: 16, value: 2 },  
 { _id: 4, value: 7 },  { _id: 11, value: 9 },  
 { _id: 7, value: 6 },  { _id: 10, value: 4 },  
 { _id: 17, value: 6 }, { _id: 8, value: 1 },  
 { _id: 18, value: 0 }, { _id: 14, value: 7 }  
]
```

Предлагают использовать функцию агрегации.

```
DeprecationWarning: Collection.mapReduce() is deprecated. Use an aggregation instead.  
```

Так как тут функция применяется к коллекции Inventory то данных по товарам нет, попробуем это исправить. Можно использовать aggregate либо менять структуру коллекции

Сделаем с aggregate:

```js
db.product_totals.aggregate([  
...     {  
...         $lookup: {  
...             from: "products",  
...             localField: "_id",     
...             foreignField: "_id",   
...             as: "product"  
...         }  
...     },  
...     { $unwind: "$product" },  
...     { $project: { _id: 0, name: "$product.name", totalQuantity: "$value" } },  
...     { $sort: { totalQuantity: -1 } }  
... ])  
...    
[  
 { name: 'Смартфон', totalQuantity: 15 },  
 { name: 'Видеокарта', totalQuantity: 11 },  
 { name: 'Компьютерная мышка', totalQuantity: 10 },  
 { name: 'Смарт-часы', totalQuantity: 9 },  
 { name: 'Жесткий диск 2TB', totalQuantity: 8 },  
 { name: 'Механическая клавиатура', totalQuantity: 8 },  
 { name: 'Игровой ноутбук', totalQuantity: 7 },  
 { name: 'Монитор 27"', totalQuantity: 7 },  
 { name: 'Роутер Wi-Fi 6', totalQuantity: 7 },  
 { name: 'МФУ принтер', totalQuantity: 6 },  
 { name: 'Внешний аккумулятор', totalQuantity: 6 },  
 { name: 'Материнская плата', totalQuantity: 5 },  
 { name: 'SSD 1TB', totalQuantity: 4 },  
 { name: 'Веб-камера FullHD', totalQuantity: 3 },  
 { name: 'Наушники с микрофоном', totalQuantity: 3 },  
 { name: 'Колонка Bluetooth', totalQuantity: 2 },  
 { name: 'Игровое кресло', totalQuantity: 1 },  
 { name: 'Планшет 10"', totalQuantity: 0 },  
 { name: 'Беспроводные наушники', totalQuantity: 0 },  
 { name: 'Флешка 128GB', totalQuantity: 0 }  
]
```


Сделаем новую коллекцию для теста MapReduce

```JS
store> db.inventory_full.insertMany([  
...   { product_id: 1, name: "Игровой ноутбук", warehouse: "Склад Москва", quantity: 5 },  
...   { product_id: 1, name: "Игровой ноутбук", warehouse: "Склад Санкт-Петербург", quantity: 2 },  
...   { product_id: 1, name: "Игровой ноутбук", warehouse: "Склад Краснодар", quantity: 0 },  
...      
...   { product_id: 2, name: "Компьютерная мышка", warehouse: "Склад Москва", quantity: 0 },  
...   { product_id: 2, name: "Компьютерная мышка", warehouse: "Склад Казань", quantity: 10 },  
...      
...   { product_id: 3, name: "Механическая клавиатура", warehouse: "Склад Москва", quantity: 8 },  
...   { product_id: 3, name: "Механическая клавиатура", warehouse: "Склад Новосибирск", quantity: 0 },  
...    
...   { product_id: 4, name: "Монитор 27\"", warehouse: "Склад Санкт-Петербург", quantity: 7 },  
...   { product_id: 5, name: "Наушники с микрофоном", warehouse: "Склад Санкт-Петербург", quantity: 0 },  
...   { product_id: 5, name: "Наушники с микрофоном", warehouse: "Склад Краснодар", quantity: 3 }  
... ])  
...    
{  
 acknowledged: true,  
 insertedIds: {  
   '0': ObjectId('68e068df308d96977bce5f47'),  
   '1': ObjectId('68e068df308d96977bce5f48'),  
   '2': ObjectId('68e068df308d96977bce5f49'),  
   '3': ObjectId('68e068df308d96977bce5f4a'),  
   '4': ObjectId('68e068df308d96977bce5f4b'),  
   '5': ObjectId('68e068df308d96977bce5f4c'),  
   '6': ObjectId('68e068df308d96977bce5f4d'),  
   '7': ObjectId('68e068df308d96977bce5f4e'),  
   '8': ObjectId('68e068df308d96977bce5f4f'),  
   '9': ObjectId('68e068df308d96977bce5f50')  
 }  
}  
store> var mapFunction = function() {  
...   emit(this.name, this.quantity); 
... };  
...    
... var reduceFunction = function(key, values) {  
...   return Array.sum(values); 
... };  
...    
... db.inventory_full.mapReduce(  
...   mapFunction,  
...   reduceFunction,  
...   { out: "product_totals_full" }  
... )  
...    
DeprecationWarning: Collection.mapReduce() is deprecated. Use an aggregation instead.  
See https://mongodb.com/docs/manual/core/map-reduce for details.  
{ result: 'product_totals_full', ok: 1 }  
store> db.product_totals_full.find()  
[  
 { _id: 'Механическая клавиатура', value: 8 },  
 { _id: 'Монитор 27"', value: 7 },  
 { _id: 'Компьютерная мышка', value: 10 },  
 { _id: 'Наушники с микрофоном', value: 3 },  
 { _id: 'Игровой ноутбук', value: 7 }  
]
```



Завершаем работы с mongodb

```sh
user@fedora:~$ docker stop mongodb  
mongodb
```
## Вывод

В результате проделанной работы - был запущен экземпляр Mongodb через docker, сделаны три коллекции для предметной области - магазин бытовой техники. Получены данные из коллекции средством agreggate и mapReduce
