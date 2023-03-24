# 从运行 SQL 查询。NodeJS (sqlite)中的“SQL”文件

> 原文：<https://levelup.gitconnected.com/running-sql-queries-from-an-sql-file-in-a-nodejs-app-sqlite-a927f0e8a545>

![](img/29601bd86a14c238762f0ba8e0f2ce10.png)

信用:https://i0.wp.com

在这篇文章中，我将展示如何让我的 NodeJS 应用程序使用`sqlite3`和 NodeJS’`fs`API 从一个 SQL 文件运行我的 SQL 查询。即使您没有使用 SQLite，您可能仍然会发现我在这里使用的技术很有帮助(您可以跳过 SQLite 特定的部分)。

**背景**:我需要使用一个 SQL 文件来启动并运行我的 SQLite 数据库，这个文件正好有 195，369 行 SQL 代码！我需要将这些查询发送到数据库，最好的方式是通过编程，但是怎么做呢？

如果你发现自己处于这种情况，这篇文章是关于我如何能够安全并按时完成这个挑战的。

以下是我采取的步骤。

**步骤 1** ，安装名为`[sqlite3](https://www.npmjs.com/package/sqlite3)`的 NPM 包(此处阅读 sqlite3 文档[)。`sqlite3`帮助您连接到 SQLite 数据库并运行查询。](https://github.com/mapbox/node-sqlite3/wiki/API)

**步骤 2** ，在你要运行 SQL 的 JS 文件中，导入/要求`sqlite3`和`fs`(不，你不需要安装这个。自带 NodeJS)。

**步骤 3，**设置内存数据库连接，如下所示:

```
let *db* = new sqlite3.Database('mydatabase', (*err*) => {
   if (*err*){
       return console.error(*err*.*message*);
   }
   console.log('Connected to the in-memory SQlite database.');
});
```

紧密联系，像这样:

```
*db*.close((*err*) => {
  if (*err*) {
    return console.error(*err*.*message*);
  }
  console.log('Closed the database connection.');
});
```

**第四步，**通过`node db.js`或别的什么运行文件，看看设置是否真的有效。

**步骤 5** ，使用`fs`将 SQL 查询作为字符串读取并解析到 JS 文件中，如下所示:

```
const *dataSql* = *fs*.readFileSync('./data.sql').toString();
```

此时，我可以在`dataSql`变量中使用全部 195，369 行 SQL 查询。

我是这样把它们放在一起的:

```
// Require or import the dependenciesconst *fs* = require('fs');
const *sqlite3* = require('sqlite3').verbose(); // Read the SQL fileconst *dataSql* = *fs*.readFileSync('./data.sql').toString(); // Setup the database connectionlet *db* = new sqlite3.Database('mydatabase', (*err*) => {
  if (*err*) {
    return console.error(*err*.*message*);
  } console.log('Connected to the in-memory SQLite database.');
}); // Convert the SQL string to array so that you can run them one at a time.
// You can split the strings using the query delimiter i.e. `;` in // my case I used `);` because some data in the queries had `;`.const *dataArr* = *dataSql*.toString().split(');'); // db.serialize ensures that your queries are one after the other depending on which one came first in your `dataArr`*db*.serialize(() => { // db.run runs your SQL query against the DB *db*.run('PRAGMA foreign_keys=OFF;'); *db*.run('BEGIN TRANSACTION;'); *// Loop through the `dataArr` and db.run each query
  dataArr*.forEach((*query*) => {
    if(*query*) {
      // Add the delimiter back to each query before you run them
      // In my case the it was `);` *query* += ');';
      *db*.run(*query*, (*err*) => {
         if(*err*) throw *err*;
      });
    }
  }); *db*.run('COMMIT;');
}); // Close the DB connection*db*.close((*err*) => {
  if (*err*) {
    return console.error(*err*.*message*);
  }
  console.log('Closed the database connection.');
});
```

如果你阅读上面的代码有困难，这里有一个 Github 要点给你。

是啊。就是这样！在运行`node index.js`时，查询运行，我需要的东西被加载到我的 SQLite 数据库中。

如果你有任何问题或建议，请在下面给我留言。如果你觉得这篇文章有趣，别忘了鼓励我；)

*感谢阅读！*

鸣谢:我发现这个网站非常有用[https://www.sqlitetutorial.net/sqlite-nodejs/](https://www.sqlitetutorial.net/sqlite-nodejs/)。看看这个。