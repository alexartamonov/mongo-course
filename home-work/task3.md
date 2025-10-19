
Запускаем mongodb из прошлой работы [task2.md](home-work/task2.md)

```bash
user@fedora:~$ docker start mongodb 
mongodb
```
Подключаемся к mongosh и выбираем базу данных store из прошлой работы
```
user@fedora:~$ docker exec -it mongodb mongosh  
Current Mongosh Log ID: 68f52d99fd68aa67e4ce5f46
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.8
Using MongoDB:          6.0.26
Using Mongosh:          2.5.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/

------
   The server generated these startup warnings when booting
   2025-10-19T18:27:29.627+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-10-19T18:27:29.627+00:00: vm.max_map_count is too low
------

test> use store
switched to db store

```

Выведем список товаров:

```JS
store> db.products.find()
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
  {
    _id: 5,
    name: 'Наушники с микрофоном',
    price: 2500,
    description: 'Подходят для работы и онлайн-общения'
  },
  {
    _id: 6,
    name: 'Веб-камера FullHD',
    price: 1800,
    description: 'Камера для видеозвонков и стриминга'
  },
  {
    _id: 7,
    name: 'МФУ принтер',
    price: 7000,
    description: 'Лазерный принтер с функцией сканирования'
  },
  {
    _id: 8,
    name: 'Игровое кресло',
    price: 15000,
    description: 'Эргономичное кресло для геймеров'
  },
  {
    _id: 9,
    name: 'Жесткий диск 2TB',
    price: 4500,
    description: 'Надежное хранилище данных'
  },
  {
    _id: 10,
    name: 'SSD 1TB',
    price: 8500,
    description: 'Быстрый твердотельный накопитель'
  },
  {
    _id: 11,
    name: 'Смарт-часы',
    price: 9000,
    description: 'Фитнес-браслет с расширенными функциями'
  },
  {
    _id: 12,
    name: 'Планшет 10"',
    price: 18000,
    description: 'Планшет с высоким разрешением экрана'
  },
  {
    _id: 13,
    name: 'Смартфон',
    price: 30000,
    description: 'Флагманский смартфон последнего поколения'
  },
  {
    _id: 14,
    name: 'Роутер Wi-Fi 6',
    price: 3500,
    description: 'Высокоскоростной маршрутизатор для дома'
  },
  {
    _id: 15,
    name: 'Беспроводные наушники',
    price: 6000,
    description: 'Компактные и удобные наушники'
  },
  {
    _id: 16,
    name: 'Колонка Bluetooth',
    price: 4000,
    description: 'Портативная колонка с отличным звуком'
  },
  {
    _id: 17,
    name: 'Внешний аккумулятор',
    price: 2500,
    description: 'Powerbank на 20000 мАч'
  },
  {
    _id: 18,
    name: 'Флешка 128GB',
    price: 1200,
    description: 'USB накопитель для быстрого обмена данными'
  },
  {
    _id: 19,
    name: 'Видеокарта',
    price: 45000,
    description: 'Игровая видеокарта последнего поколения'
  },
  {
    _id: 20,
    name: 'Материнская плата',
    price: 15000,
    description: 'Материнская плата с поддержкой новейших процессоров'
  }
]

```

Добавим индекс для коллекции 

```js
store> db.products.createIndex({ "$**": "text" })
$**_text
store>
```

Поиск по коллекции

```js


store> db.products.find({ $text: { $search: "игровой" } })
[
  {
    _id: 1,
    name: 'Игровой ноутбук',
    price: 20000,
    description: 'Игровой ноутбук - последняя новинка от известного бренда'
  }
]


```

Сделаем explain запроса

```JS
store> db.products.find({ $text: { $search: "игровой" } }).explain("executionStats")
... 
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'store.products',
    indexFilterSet: false,
    parsedQuery: {
      '$text': {
        '$search': 'игровой',
        '$language': 'english',
        '$caseSensitive': false,
        '$diacriticSensitive': false
      }
    },
    queryHash: '07C96184',
    planCacheKey: '7E54BFDF',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'TEXT_MATCH',
      indexPrefix: {},
      indexName: '$**_text',
      parsedTextQuery: {
        terms: [ 'игровои' ],
        negatedTerms: [],
        phrases: [],
        negatedPhrases: []
      },
      textIndexVersion: 3,
      inputStage: {
        stage: 'FETCH',
        inputStage: {
          stage: 'IXSCAN',
          keyPattern: { _fts: 'text', _ftsx: 1 },
          indexName: '$**_text',
          isMultiKey: true,
          isUnique: false,
          isSparse: false,
          isPartial: false,
          indexVersion: 2,
          direction: 'backward',
          indexBounds: {}
        }
      }
    },
    rejectedPlans: []
  },
  executionStats: {
    executionSuccess: true,
    nReturned: 1,
    executionTimeMillis: 0,
    totalKeysExamined: 1,
    totalDocsExamined: 1,
    executionStages: {
      stage: 'TEXT_MATCH',
      nReturned: 1,
      executionTimeMillisEstimate: 0,
      works: 2,
      advanced: 1,
      needTime: 0,
      needYield: 0,
      saveState: 0,
      restoreState: 0,
      isEOF: 1,
      indexPrefix: {},
      indexName: '$**_text',
      parsedTextQuery: {
        terms: [ 'игровои' ],
        negatedTerms: [],
        phrases: [],
        negatedPhrases: []
      },
      textIndexVersion: 3,
      docsRejected: 0,
      inputStage: {
        stage: 'FETCH',
        nReturned: 1,
        executionTimeMillisEstimate: 0,
        works: 2,
        advanced: 1,
        needTime: 0,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        docsExamined: 1,
        alreadyHasObj: 0,
        inputStage: {
          stage: 'IXSCAN',
          nReturned: 1,
          executionTimeMillisEstimate: 0,
          works: 2,
          advanced: 1,
          needTime: 0,
          needYield: 0,
          saveState: 0,
          restoreState: 0,
          isEOF: 1,
          keyPattern: { _fts: 'text', _ftsx: 1 },
          indexName: '$**_text',
          isMultiKey: true,
          isUnique: false,
          isSparse: false,
          isPartial: false,
          indexVersion: 2,
          direction: 'backward',
          indexBounds: {},
          keysExamined: 1,
          seeks: 1,
          dupsTested: 1,
          dupsDropped: 0
        }
      }
    }
  },
  command: {
    find: 'products',
    filter: { '$text': { '$search': 'игровой' } },
    '$db': 'store'
  },
  serverInfo: {
    host: '7dc500973071',
    port: 27017,
    version: '6.0.26',
    gitVersion: '0c4ec4b6005f75582ce208fc800f09f561b6c2e8'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600
  },
  ok: 1
}

```

Смотрим план запроса - видим, что наш index попал в wining plan:

```
 winningPlan: {
      stage: 'TEXT_MATCH',
      indexPrefix: {},
      indexName: '$**_text',
```

и 

```
    totalKeysExamined: 1,
    totalDocsExamined: 1,
```

Это означает, что был использован индекс и всего было пройдено по 1 документу, остальные не затронуты - значит наш индекс работает и ускоряет поиск.

Но в нашей коллекции есть не только "Игровой ноутбук", но и ещё какие то игровые устройства:

```JS
store> db.products.find({
...   $or: [
...     { name: { $regex: "Игро", $options: "i" } },
...     { description: { $regex: "Игро", $options: "i" } }
...   ]
... })
... 
[
  {
    _id: 1,
    name: 'Игровой ноутбук',
    price: 20000,
    description: 'Игровой ноутбук - последняя новинка от известного бренда'
  },
  {
    _id: 8,
    name: 'Игровое кресло',
    price: 15000,
    description: 'Эргономичное кресло для геймеров'
  },
  {
    _id: 19,
    name: 'Видеокарта',
    price: 45000,
    description: 'Игровая видеокарта последнего поколения'
  }
]
```

Попробуем разобраться почему так произошло?

Видно что язык индекса указан как `english`, попробуем поменять его на русский
```
parsedQuery: {
      '$text': {
        '$search': 'игровой',
        '$language': 'english',
        '$caseSensitive': false,
        '$diacriticSensitive': false
      }
    },
```


Пересоздадим индекс:

```JS
store> db.products.createIndex(
...   { "$**": "text" },
...   { default_language: "russian" }
... )
... 
MongoServerError[IndexOptionsConflict]: An equivalent index already exists with the same name but different options. Requested index: { v: 2, key: { _fts: "text", _ftsx: 1 }, name: "$**_text", default_language: "russian", weights: { $**: 1 }, language_override: "language", textIndexVersion: 3 }, existing index: { v: 2, key: { _fts: "text", _ftsx: 1 }, name: "$**_text", weights: { $**: 1 }, default_language: "english", language_override: "language", textIndexVersion: 3 }
store> db.products.dropIndex("$**_text")
... 
{ nIndexesWas: 2, ok: 1 }
store> db.products.createIndex(
store> db.products.createIndex(
...   { "$**": "text" },
...   { default_language: "russian" }
... )
... 
$**_text

```

и теперь повторяем заново всё:

```JS
store> db.products.find({ $text: { $search: "игровой" } })
[
  {
    _id: 1,
    name: 'Игровой ноутбук',
    price: 20000,
    description: 'Игровой ноутбук - последняя новинка от известного бренда'
  },
  {
    _id: 8,
    name: 'Игровое кресло',
    price: 15000,
    description: 'Эргономичное кресло для геймеров'
  },
  {
    _id: 19,
    name: 'Видеокарта',
    price: 45000,
    description: 'Игровая видеокарта последнего поколения'
  }
]

```

Ура! Теперь выводим три товара из нашего списка, посмотрим изменилось ли что то в плане запроса - 

```
store> db.products.find({ $text: { $search: "игровой" } }).explain("executionStats")
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'store.products',
    indexFilterSet: false,
    parsedQuery: {
      '$text': {
        '$search': 'игровой',
        '$language': 'russian',
        '$caseSensitive': false,
        '$diacriticSensitive': false
      }
    },
    queryHash: '07C96184',
    planCacheKey: '7E54BFDF',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'TEXT_MATCH',
      indexPrefix: {},
      indexName: '$**_text',
      parsedTextQuery: {
        terms: [ 'игров' ],
        negatedTerms: [],
        phrases: [],
        negatedPhrases: []
      },
      textIndexVersion: 3,
      inputStage: {
        stage: 'FETCH',
        inputStage: {
          stage: 'IXSCAN',
          keyPattern: { _fts: 'text', _ftsx: 1 },
          indexName: '$**_text',
          isMultiKey: true,
          isUnique: false,
          isSparse: false,
          isPartial: false,
          indexVersion: 2,
          direction: 'backward',
          indexBounds: {}
        }
      }
    },
    rejectedPlans: []
  },
  executionStats: {
    executionSuccess: true,
    nReturned: 3,
    executionTimeMillis: 0,
    totalKeysExamined: 3,
    totalDocsExamined: 3,
    executionStages: {
      stage: 'TEXT_MATCH',
      nReturned: 3,
      executionTimeMillisEstimate: 0,
      works: 4,
      advanced: 3,
      needTime: 0,
      needYield: 0,
      saveState: 0,
      restoreState: 0,
      isEOF: 1,
      indexPrefix: {},
      indexName: '$**_text',
      parsedTextQuery: {
        terms: [ 'игров' ],
        negatedTerms: [],
        phrases: [],
        negatedPhrases: []
      },
      textIndexVersion: 3,
      docsRejected: 0,
      inputStage: {
        stage: 'FETCH',
        nReturned: 3,
        executionTimeMillisEstimate: 0,
        works: 4,
        advanced: 3,
        needTime: 0,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        docsExamined: 3,
        alreadyHasObj: 0,
        inputStage: {
          stage: 'IXSCAN',
          nReturned: 3,
          executionTimeMillisEstimate: 0,
          works: 4,
          advanced: 3,
          needTime: 0,
          needYield: 0,
          saveState: 0,
          restoreState: 0,
          isEOF: 1,
          keyPattern: { _fts: 'text', _ftsx: 1 },
          indexName: '$**_text',
          isMultiKey: true,
          isUnique: false,
          isSparse: false,
          isPartial: false,
          indexVersion: 2,
          direction: 'backward',
          indexBounds: {},
          keysExamined: 3,
          seeks: 1,
          dupsTested: 3,
          dupsDropped: 0
        }
      }
    }
  },
  command: {
    find: 'products',
    filter: { '$text': { '$search': 'игровой' } },
    '$db': 'store'
  },
  serverInfo: {
    host: '7dc500973071',
    port: 27017,
    version: '6.0.26',
    gitVersion: '0c4ec4b6005f75582ce208fc800f09f561b6c2e8'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600
  },
  ok: 1
}

```




