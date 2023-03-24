# Node.js 基础知识—使用 MongoDB 进行日志记录

> 原文：<https://levelup.gitconnected.com/node-js-basics-logging-with-mongodb-7480a73eb775>

![](img/88869f0fc7b897ecfd3c6c69693d47b3.png)

托马斯·佩哈姆在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将了解如何开始使用 Node.js 创建程序。

# 记录

MongoDB Node.js 客户机内置了一个日志记录器。

要使用它，我们可以写:

```
const { MongoClient, Logger } = require('mongodb');
const connection = "mongodb://localhost:27017";
const client = new MongoClient(connection);async function run() {
  try {
    Logger.setLevel("debug");
    await client.connect();
    const db = client.db("test");
    db.dropCollection('test');
    db.createCollection('test');
    const testCollection = await db.collection('test');
    const result = await testCollection.insertMany([
      { "_id": 1, "name": "apples", "qty": 5, "rating": 3 },
      { "_id": 2, "name": "apples", "qty": 7, "rating": 1 },
      { "_id": 3, "name": "oranges", "qty": 6, "rating": 2 },
      { "_id": 4, "name": "avocados", "qty": 3, "rating": 5 },
    ]);
    console.log(result)
    const cursor = await testCollection.find();
    cursor.forEach(console.dir);
  } finally {
    await client.close();
  }
}
run().catch(console.dir);
```

调用`Logger.setLevel`到`'debug'`来查看其下面的方法运行时的所有调试消息。

我们应该会在控制台中看到比没有记录器时更多的消息。

# 对特定类别进行筛选

我们可以在登录时过滤项目，这样就不会看到太多项目。

例如，我们可以写:

```
const { MongoClient, Logger } = require('mongodb');
const connection = "mongodb://localhost:27017";
const client = new MongoClient(connection);async function run() {
  try {
    Logger.setLevel("debug");
    Logger.filter("class", ["Db"]);
    await client.connect();
    const db = client.db("test");
    db.dropCollection('test');
    db.createCollection('test');
    const testCollection = await db.collection('test');
    const result = await testCollection.insertMany([
      { "_id": 1, "name": "apples", "qty": 5, "rating": 3 },
      { "_id": 2, "name": "apples", "qty": 7, "rating": 1 },
      { "_id": 3, "name": "oranges", "qty": 6, "rating": 2 },
      { "_id": 4, "name": "avocados", "qty": 3, "rating": 5 },
    ]);
    console.log(result)
    const cursor = await testCollection.find();
    cursor.forEach(console.dir);
  } finally {
    await client.close();
  }
}
run().catch(console.dir);
```

带有`['Db']`数组的`Logger.filter`方法让我们显示`'Db'`类上的消息。

我们也可以用自己的类定制日志记录器。

例如，我们可以写:

```
const { MongoClient, Logger } = require('mongodb');
const connection = "mongodb://localhost:27017";
const client = new MongoClient(connection);class CustomLogger {
  constructor() {
    this.logger = new Logger("A");
  }log() {
    if (this.logger.isInfo()) {
      this.logger.info("logging A", {});
    }
  }
}async function run() {
  try {
    const customLogger = new CustomLogger();
    customLogger.log();
    await client.connect();
    const db = client.db("test");
    db.dropCollection('test');
    db.createCollection('test');
    const testCollection = await db.collection('test');
    const result = await testCollection.insertMany([
      { "_id": 1, "name": "apples", "qty": 5, "rating": 3 },
      { "_id": 2, "name": "apples", "qty": 7, "rating": 1 },
      { "_id": 3, "name": "oranges", "qty": 6, "rating": 2 },
      { "_id": 4, "name": "avocados", "qty": 3, "rating": 5 },
    ]);
    console.log(result)
    const cursor = await testCollection.find();
    cursor.forEach(console.dir);
  } finally {
    await client.close();
  }
}
run().catch(console.dir);
```

我们创建了`CustomLogger`类，并使用它的`log`方法进行日志记录。

同样，我们可以调用`setCurrentLogger`来设置一个定制的日志记录函数:

```
const { MongoClient, Logger } = require('mongodb');
const connection = "mongodb://localhost:27017";
const client = new MongoClient(connection);async function run() {
  try {
    Logger.setLevel("debug");
    Logger.setCurrentLogger((msg, context) => {
      context['foo'] = 'bar';
      msg = `Hello, World. ${msg}`;
      console.log(msg, context);
    });
    await client.connect();
    const db = client.db("test");
    db.dropCollection('test');
    db.createCollection('test');
    const testCollection = await db.collection('test');
    const result = await testCollection.insertMany([
      { "_id": 1, "name": "apples", "qty": 5, "rating": 3 },
      { "_id": 2, "name": "apples", "qty": 7, "rating": 1 },
      { "_id": 3, "name": "oranges", "qty": 6, "rating": 2 },
      { "_id": 4, "name": "avocados", "qty": 3, "rating": 5 },
    ]);
    console.log(result)
    const cursor = await testCollection.find();
    cursor.forEach(console.dir);
  } finally {
    await client.close();
  }
}
run().catch(console.dir);
```

`msg`参数有消息。`context`有关于记录事件的附加数据。

# 结论

我们可以使用 Node.js MongoDB 客户端在 MongoDB 代码中添加一个日志记录器。