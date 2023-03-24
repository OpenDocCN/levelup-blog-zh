# 用于执行 CRUD 操作的 MongoDB 方法

> 原文：<https://levelup.gitconnected.com/mongodb-methods-for-performing-crud-operations-6be663e73479>

了解如何使用 Nodejs 在 MongoDB 中轻松创建、读取、更新和删除数据。

![](img/91d22a1cdbf387b1ed0c6f8fc5e3cda4.png)

由[万花筒](https://unsplash.com/@kaleidico?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将探索 MongoDB 提供的执行基本 CRUD(创建、读取、更新、删除)操作的所有方法。

## **1。创建:**

下面是 MongoDB 提供的在集合中创建/插入文档的方法

插入一个:

它具有以下语法。

```
insertOne(doc, options, callback)
```

`doc`文件插入
`options`可选设置
`callback`回调函数得到插入操作的结果
返回= >如果没有回调通过则承诺

使用承诺:

使用回拨:

b)插入许多:

它具有以下语法。

```
insertMany(docs, options, callback)
```

`docs`数组文件插入
`options`可选设置
`callback`回调函数得到插入操作的结果
returns = >如果没有回调则承诺通过

使用承诺:

使用回拨:

还有一个`insert`方法，但它已被弃用，所以推荐使用`insertOne`或`insertMany`。

## **2。阅读:**

对于阅读，提供了以下方法

a) findOne:

它具有以下语法。

```
findOne(query, options, callback)
```

`query`查询查找文档
`options`可选设置
`callback`回调函数获取查找操作的结果
返回= >承诺如果没有回调通过

使用承诺:

使用回拨:

**b)查找:**

它具有以下语法。

```
find(query, options)
```

`query`查询查找文档
`options`可选设置

与其他方法相比，find 方法有所不同，因为 find 不返回结果，而是返回指向查询结果的光标。我们可以使用光标操作数据，比如获取文档数、遍历文档、将文档转换为数组等。

计算文档数:

循环浏览文档:

您可以在此找到所有可用的光标方法

## **3。更新:**

**a)更新一:**

它具有以下语法。

```
updateOne(filter, update, options, callback)
```

`filter`过滤器用于选择要更新的文档
`update`对文档应用的更新操作
`options`可选设置
`callback`回调函数获取更新操作的结果
returns = >如果没有回调通过

使用承诺:

这里，我们使用`$set`操作符更新雇员蒂姆的年龄。还有许多其他的更新操作符，如用于递增的`$inc`，用于执行重命名操作的`$rename`，等等。

你可以在这里找到更新操作符的完整列表:[https://docs.mongodb.com/manual/reference/operator/update/](https://docs.mongodb.com/manual/reference/operator/update/)

**b)更新许多:**

它具有以下语法。

```
updateMany(filter, update, options, callback)
```

`filter`用于选择要更新的文档的过滤器
`update`对文档应用的更新操作
`options`可选设置
`callback`回调函数获取更新操作的结果
如果没有通过回调，returns = > promise

使用承诺:

**c) findOneAndUpdate:**

它具有以下语法。

```
findOneAndUpdate(filter, update, options, callback)
```

`filter`用于选择要更新的文档的过滤器
`update`要应用于文档的更新操作
`options`可选设置
`callback`回调函数以获得更新操作的结果
如果没有通过回调则返回= >承诺

使用承诺:

## **4。删除:**

以下是为删除文档提供的方法

**a)删除一个:**

它具有以下语法。

```
deleteOne(filter, options, callback):
```

`filter`过滤器用来选择要删除的文档
`options`可选设置
`callback`回调函数来获取删除操作的结果
returns = >如果没有回调通过

使用承诺:

**b)删除许多:**

它具有以下语法。

```
deleteMany(filter, options, callback)
```

`filter`过滤器用来选择要删除的文档
`options`可选设置
`callback`回调函数来获得删除操作的结果
returns = >如果没有回调通过

使用承诺:

**c) findOneAndDelete:**

它具有以下语法。

```
findOneAndDelete**(**query**,** options**,** callback)
```

`query`查询找到文档
`options`可选设置
`callback`回调函数得到删除操作的结果
returns = >如果没有回调通过

使用承诺:

使用回拨:

可以在这里找到完整的 MongoDB API 参考:[http://MongoDB . github . io/node-MongoDB-native/3.4/API/index . html](http://mongodb.github.io/node-mongodb-native/3.4/api/index.html)

今天到此为止。希望你今天学到了新东西。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周简讯，里面有惊人的技巧、窍门和文章。**