# Node.js 提示— Redis、查询字符串、承诺的并发性、SQL 注入

> 原文：<https://levelup.gitconnected.com/node-js-tips-redis-query-strings-concurrency-of-promises-sql-injections-59b7192f907a>

![](img/ec4cf1060f59c6db1d5052c831ab8d60.png)

加布里埃尔·格里格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写节点应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些编写节点应用程序时常见问题的解决方案。

# 使用 ES6 的 Promise.all()时限制并发的最佳方式

为了在使用`Promise.all`时限制并发性，我们可以使用`es6-promise-pool`包。

例如，我们可以写:

```
const promiseProducer = function * () {
  for (let count = 1; count <= 5; count++) {
    yield delayValue(count, 1000)
  }
}

const promiseIterator = generatePromises();
const pool = new PromisePool(promiseIterator, 3);

pool.start()
  .then(function () {
    console.log('Complete')
  })
```

我们在`promiseProducer`生成器函数中得到承诺的值。

然后我们调用生成器函数，这样我们就得到一个迭代器。

然后我们将它传递给`PromisePool`构造函数。

3 是允许的并发承诺数。

最后，我们打电话给`pool.start`来祈求应许。

# 从目录中删除所有文件，但不删除 Node.js 中的目录

通过使用`readdir`方法读取目录的内容，我们可以在不删除目录的情况下删除目录中的所有文件。

然后我们可以遍历每个文件并对它们调用`unlink`。

例如，我们可以写:

```
const fs = require('fs');
const path = require('path');const directory = '/foo/bar';fs.readdir(directory, (err, files) => {
  if (err){
    return console.log(err);
  } for (const file of files) {
    fs.unlink(path.join(directory, file), err => {
      if (err) {
        console.log(err);
      }
    });
  }
});
```

我们调用`readdir`来读取目录的内容。然后，我们使用 for-of 循环遍历这些项目。

然后我们调用`unlink`来删除每个文件。

# Redis 和 Node.js

我们可以通过使用`redis`库来访问 Redis 数据库。

要安装它，我们运行:

```
npm install redis
```

然后在我们的节点应用程序中，我们可以通过编写以下内容来使用 Redis 客户端:

```
const redis = require("redis");
const client = redis.createClient();client.on("error", (err) => {
  console.log(err);
});client.set("key", "val", redis.print);
client.hset("hash key", "foo", "value 1", redis.print);
client.hset(["hash key", "bar", "value 2"], redis.print);
client.hkeys("hash key", (err, replies) => {
  replies.forEach((reply, i) => {
    console.log(i, reply);
  });
  client.quit();
});
```

我们使用`redis`模块并调用`createClient`来创建客户端。

然后我们附加一个监听器来监听`'error'`事件。

然后我们调用`set`来设置键和值。

我们可以使用`hset`来设置一个带有自己值的散列键。

`redis.print`打印数值。

`hkeys`获取散列键并打印索引和结果。

# 解析 Node.js 中的查询字符串

我们可以用`url`模块解析 Node.jhs 中的查询字符串。

例如，如果我们使用`http`模块来创建我们的服务器，我们可以写:

```
const http = require('http');
const url = require('url');const server = http.createServer((request, response) => {
  const queryData = url.parse(request.url, true).query;
  response.writeHead(200, { "Content-Type": "text/plain" }); if (queryData.name) {
    response.end(queryData.name);
  } else {
    response.end("hello world\n");
  }
});server.listen(8000);
```

我们调用`url.parse`来解析 URL 中的查询字符串。

请求 URL 存储在`request.url`属性中。

解析的结果被分配给`queryData`常量。

然后我们解析`name`查询参数，如果它存在的话。

如果用查询字符串`?name=joe`发出请求，那么`queryData.name`将是`'joe'`。

# 防止 Node.js 中的 SQL 注入

为了防止节点应用中的 SQL 注入，我们应该使用一个允许进行参数化查询的库。

例如，使用 node-mysql-native 库，我们可以编写:

```
const userId = 5;
const query = connection.query('SELECT * FROM users WHERE id = ?', [userId], (err, results) => {
  //...
});
```

`?`表示我们可以传入的参数。第二个参数是。

当查询完成时，回调被调用。

`results`有结果了。

清理通过以下规则完成:

*   数字没动过
*   布尔值被转换成字符串
*   日期对象被转换为 YYYY-mm-dd:ii:ss 字符串
*   缓冲区被转换成十六进制字符串
*   字符串被转义
*   数组变成了列表
*   嵌套数组被转换成分组列表
*   物体被变成`key = 'val'`对
*   `undefined`或`null`转换为`NULL`
*   `NaN`和`Infinity`保持原样。MySQL 不支持它们，如果它们存在，将会出错。

![](img/6509d562cf8bc839062576286d93cd33.png)

克里斯蒂娜·安妮·科斯特洛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们应该使用一个库来进行数据库查询，以防止 SQL 注入。

此外，我们可以使用库来限制并行承诺的并发性。

我们可以使用 Node 的 Redis 库来访问 Redis 数据库。

`url`包可以解析查询字符串。