# 使用 Golang 使用 MongoDB

> 原文：<https://levelup.gitconnected.com/working-with-mongodb-using-golang-754ead0c10c>

## 使用 mongo-go-driver 实现 Go 和 MongoDB 的基本 CRUD 操作

![](img/e37f30d730064dce5e57577a957ed412.png)

照片由[法比奥](https://unsplash.com/@fabioha?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在本文中，我将向您展示如何使用 Go 在 MongoDB 数据库上执行基本的创建、读取、更新和删除(CRUD)操作。我使用官方的 MongoDB Go 驱动程序来实现上述操作。

**注意:**在您继续学习之前，我希望您对 GoLang 语法和原始类型有一个初级的了解，以便理解源代码。我还希望您对 MongoDB 概念有一个基本的了解。

## 安装 MongoDB Go 驱动程序

像其他官方 MongoDB 驱动程序一样，[Go 驱动程序](https://github.com/mongodb/mongo-go-driver)是 Go 编程语言的惯用语言。它提供了一种简单的方法来使用 MongoDB 作为 Go 程序的数据库解决方案。它与 MongoDB API 完全集成，并公开了 API 的所有查询、索引和聚合特性，以及其他高级特性。如果您正在使用“**go get”**，您可以使用以下命令安装驱动程序:

```
go get go.mongodb.org/mongo-driver
```

## 创建数据库连接

我实现了一个名为`***connectionhelper***` 的包，帮助您处理 MongoDB 数据库连接。这个包的实现解释如下。

代码是不言自明的。需要提及的重要一点是，我已经使用`***sync***` *包中的`**“Once”**` 对象创建了 MongoDB 客户端的 singleton 对象。* `**Once**`是一个只会执行一个动作的对象。***GetMongoClient()***方法给你一个 MongoDB 客户端对象来使用。在第一次调用***GetMongoClient()***时，创建并初始化 MongoDB 客户端的对象。对该函数的任何后续调用都不会创建一个新的客户机对象，它们与先前创建的对象一起提供服务。

## 用 *bson* 标签定义结构

我将问题管理器应用程序作为一个例子。为了处理应用程序中的“问题”实体，我定义了下面的*结构*

清单 2:定义一个结构来映射文档。

MongoDB 驱动程序使用`bson`标签将 *struct* 字段映射到 MongoDB 集合中的文档属性。您不需要指定和使用`bson`标签，在这种情况下，驱动程序在编码结构值时通常只使用小写的字段名。但是，当您需要不同的名称时，bson 标签是必需的。在整篇文章中，我在每个代码清单中都使用了这个结构。

## 在集合中添加新文档

要插入单个文档，请使用 ***集合。*InsertOne()**方法。函数`CreateIssue()`在集合中添加新文档，并返回布尔成功状态。

清单 3:实现插入一个操作

要一次插入多个文档， ***集合。InsertMany()*** 该方法将对对象进行切片。函数`CreateMany()`将 struct 问题的一部分作为输入，并将其插入到集合中。

清单 4:实现插入多个操作

## 从集合中获取特定文档。

为了找到一个文档，你需要一个*过滤器*文档以及一个指向结果可以被解码成的值的指针。要查找单个文档，使用 ***集合。FindOne()。*** 这个方法返回一个结果，可以解码成一个值。函数`GetIssuesByCode`将 code 作为输入参数，与文档的`code`字段进行比较，返回第一个匹配的文档。

清单 5:实现插入一个操作

## 从集合中获取所有文档

要查找多个文档，使用**和*集合。查找()。*** 该方法返回一个*光标*。一个*光标*提供了一个文档流，通过它你可以一次迭代和解码一个文档。一旦游标耗尽，您应该关闭游标。函数****GetAllIssues()***返回集合中存在的所有文档。*

*清单 6:实现 Find All 操作*

## *更新集合中的文档*

****收藏。UpdateOne()*** 方法允许你更新单个文档。它需要一个*过滤器*文档来匹配数据库中的文档，还需要一个*更新器*文档来描述更新操作。您可以使用 bson 构建这些。d 型。函数***mark completed()***将“code”作为输入参数，更新第一个匹配的文档&并将其`completed`标志设置为 true。*

*清单 7:实现更新一个操作*

## *从集合中删除文档*

*最后，您可以使用 ***集合删除文档。*delete one()**或**收藏。DeleteMany()** 。这里你通过 ***bson。D{{}}*** 作为*过滤器*参数，它将匹配集合中的所有文档。你也可以使用一个 ***集合。*drop()**删除整个收藏。*

*清单 8:实现删除一个操作*

****DeleteOne()*** 方法仅删除集合中的第一个匹配文档，而 ***DeleteMany()*** 删除集合中存在的所有匹配文档。*

*清单 8:实现删除多个操作*

*`***connectionhelper***`包的代码在这里是。你可以在这里找到剩余 CRUD 操作的代码[。](https://gist.github.com/radhakishans1378/852fd10a286a03bd76ffcad00aec417b)*

## *结论*

*在本文中，我们已经实现了基本的 CRUD 操作。我们更深入地了解了 MongoDB Golang 驱动程序及其实现。MongoDB Golang 驱动程序为您提供了多种与 BSON 数据交互的方式。MongoDB Go 驱动程序允许您将 MongoDB 集成到任何应用程序中，并利用令人印象深刻的 Go 生态系统。*

*目前就这些。快乐学习…继续读。*