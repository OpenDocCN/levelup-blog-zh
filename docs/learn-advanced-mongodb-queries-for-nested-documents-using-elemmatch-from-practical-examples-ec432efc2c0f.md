# 从实际例子中学习嵌套文档的高级 MongoDB 查询

> 原文：<https://levelup.gitconnected.com/learn-advanced-mongodb-queries-for-nested-documents-using-elemmatch-from-practical-examples-ec432efc2c0f>

## 学习 MongoDB 中的 elemMatch 操作符

MongoDB 是一个开源的面向文档的 NoSQL 数据库。我们已经介绍了[如何使用](https://medium.com/codex/learn-the-essentials-and-get-started-with-mongodb-8380026642d5) `[mongosh](https://medium.com/codex/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)` [在控制台中运行基本的 CRUD 命令](https://medium.com/codex/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)。此外，我们还介绍了如何使用 [MongoDB Atlas](https://medium.com/codex/how-to-use-mongodb-atlas-to-manage-your-server-and-data-d97a6e7663c5) 来托管您的 MongoDB 服务器和数据。在本文中，我们将更进一步，从实际例子中学习使用`$elemMatch`操作符对嵌套文档进行高级 MongoDB 查询。我们仍将使用`mongosh`在控制台中运行查询，因此您不需要额外安装任何东西。但是，如果您喜欢在可视化环境中工作，您可以使用 MongoDB 的[图形 IDE。](https://medium.com/codex/how-to-use-mongodb-with-graphical-ides-420597ede80e)

![](img/6c6e3118e20e36ec069290acafe7cf28.png)

图片来自 [Pixabay](https://pixabay.com/vectors/mango-funny-alive-the-fruit-juicy-1431418/) 。

在我们开始这个高级旅程之前，我们应该有一个可用的 MongoDB 服务器。您可以[使用 Docker](https://medium.com/codex/learn-the-essentials-and-get-started-with-mongodb-8380026642d5) 在容器中启动一个 MongoDB 服务器，或者[使用 MongoDB Atlas](https://medium.com/codex/how-to-use-mongodb-atlas-to-manage-your-server-and-data-d97a6e7663c5) 启动一个由 Atlas 托管和维护的服务器。为了简单起见，我们将在本文中使用 Docker 容器，但是您可以自由选择您喜欢的容器。为 MongoDB 启动 Docker 容器的命令是:

```
$ docker network create mongo-net$ docker run --detach --network mongo-net --name mongo-server \
    --env MONGO_INITDB_ROOT_USERNAME=admin \
    --env MONGO_INITDB_ROOT_PASSWORD=pass \
    --env MONGO_INITDB_ROOT_DATABASE=admin \
    --volume mongo-data:/data/db \
    --publish 27017:27017 \
    mongo:5.0.6
```

你可以在你的电脑上安装 `[mongosh](https://docs.mongodb.com/mongodb-shell/install/#std-label-mdb-shell-install)`或者使用 Docker 容器自带的:

```
$ docker exec -it mongo-server bash
$ mongosh "mongodb://admin:pass@localhost:27017"
```

现在我们可以使用`mongosh`开始使用 MongoDB 数据库了。如果这是你第一次使用`mongosh`，建议你[先查看这篇介绍性文章](https://medium.com/codex/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)，这样你就不会迷失在高级查询中。

在这篇文章中，我们将使用一些笔记本电脑网上商店的产品数据，如 Elasticsearch 的[文章所示。您可以下载这个 JSON 文件](https://medium.com/codex/learn-elasticsearch-from-practical-examples-495f2f8db83e)来获得将要使用的原始数据。

如果你像我一样使用 Docker 容器内部的`mongosh`，你需要将 JSON 文件复制到容器中:

```
$ **docker cp ./laptops_demo_for_mongodb.json mongo-server:/**
```

如果你使用安装在你电脑上的`monosh`，你需要在你当前的工作目录中有这个 JSON 文件。然后，我们可以加载这个文件，并将所有文档插入到一个集合中:

上述代码的要点:

*   我们使用`use products`来声明一个名为`products`的 MongoDB 数据库，当一个文档被插入其中的一个集合时，就会创建这个数据库。
*   JSON 数据由`require`命令加载。并且加载的数据是可以被`insertMany()`方法直接使用的格式。笔记本电脑的数据被插入到一个名为`laptops`的集合中。
*   `countDocuments()`用于检查集合中的文档数量，而`findOne()`方法用于显示集合中的第一个文档。

现在数据已经准备好了，我们可以开始为它编写一些查询。

正如我们所见，`laptops`集合中的笔记本文档有一个`attributes`字段，它是一个嵌入文档的数组。查询和更新这样的字段比简单的非嵌套字段更复杂。

首先，我们来找出所有 CPU 为`Intel Core i7–8550U`的笔记本电脑。这应该很简单，因为 CPU 是明确的:

请注意，嵌套字段是用点符号查询的，必须用引号括起来。如果你喜欢用图形 IDE 编写跨多行的查询，请参考[这篇关于 MongoDB IDE 的文章](https://medium.com/codex/how-to-use-mongodb-with-graphical-ides-420597ede80e)。

我们应该得到我们想要的结果，因为在`attributes`数组中只有一个嵌套文档具有 CPU 型号的值:

现在让我们找出所有内存为 16GB 的笔记本电脑。凭直觉，您可能希望使用如下查询:

当执行上述查询时，似乎返回了所有内存为 16GB 的笔记本电脑:

然而，这是许多 MongoDB 初学者犯错误的地方，也是一些 bug 被引入您的代码的地方。如果您在`mongosh`中输入“it”以显示更多结果，或者使用 IDE 向下滚动到结果页面的底部并仔细检查，您会发现一些奇怪的事情:

通过上面的查询，我们得到了 16GB 的笔记本电脑！这是因为上面的查询找到的文档中，`attributes`数组至少有一个包含字段`attribute_name`等于`memory`的嵌入文档，以及至少有一个包含字段`attribute_value`等于`16GB`的嵌入文档(但不一定是同一个嵌入文档)。

由于所有的`attributes`数组都有一个`attribute_name`为`memory`的嵌入文档，上面的查询给出了错误的结果。我们想要的是，对于同一个嵌入文档，这两个条件都应该满足。为了实现这一点，我们不能按如上所示的点符号进行查询，而是需要使用`$elemMatch`操作符:

这一次存储为 16GB 但内存不是 16GB 的笔记本电脑将不会被退回。如果您不相信，您可以计算两个查询返回的文档数:

现在让我们尝试一个更复杂的例子，找出所有内存为 16GB、存储容量为 1TB 的笔记本电脑。我们需要使用`$and`操作符来指定两个嵌套文档的条件，这非常类似于`bool`和`must`关键字在[嵌套文档](https://lynn-kwong.medium.com/learn-advanced-crud-and-search-queries-for-nested-objects-in-elasticsearch-from-practical-examples-7aebc1408d6f)查询中的用法。

该查询将返回我们想要的结果:

注意，尽管`$and`操作符是 MongoDB 中的默认操作符，但它在这里是强制的，因为我们在两种不同的条件下查询同一个字段。否则，我们将只按第二个条件进行查询，并将得到不正确的结果:

在这种情况下，您将得到一个不正确的结果。如果你尝试其他条件，你会得到更多不正确的结果。

一旦您知道如何查询包含嵌套文档的数组，更新它应该相当容易。请注意，嵌套文档在数组中是有序的，因此您可以通过索引位置来访问嵌套文档。

让我们将笔记本电脑的内存更新为 16GB，其中`_id`等于 1:

如果我们现在检查这台笔记本电脑，它的属性应该已经更新:

这里我们使用投影来显示文档的`attributes`字段:

干杯！嵌套文档更新成功！

在这篇文章中，我们介绍了如何在 MongoDB 中查询和更新一组嵌套文档。这是一种高级用法，但在实践中很常见。我相信你现在对在工作中处理这类问题相当有信心。

相关文章:

*   [学习基础知识并开始使用 MongoDB](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5?source=your_stories_page----------------------------------------)
*   [关于在 Python 中使用 MongoDB 你需要知道的一切](https://lynn-kwong.medium.com/all-you-need-to-know-about-using-mongodb-in-python-caa077c9a20f?source=your_stories_page----------------------------------------)