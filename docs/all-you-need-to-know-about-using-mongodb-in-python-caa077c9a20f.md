# 关于在 Python 中使用 MongoDB，您需要知道的是

> 原文：<https://levelup.gitconnected.com/all-you-need-to-know-about-using-mongodb-in-python-caa077c9a20f>

## 了解 Python 中 MongoDB 的常见用例

在本文中，我们将讨论如何在 Python 中使用 MongoDB。如果你还不了解 MongoDB 或者想更新一下，你可以查看[的这篇介绍性文章](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)和[的这篇聚合文章](https://medium.com/codex/learn-powerful-mongodb-aggregation-pipelines-from-practical-examples-efead98f08)来快速了解 MongoDB。

![](img/8d20a2432febab43c5a1578982c3c04d.png)

在我们能够在 MongoDB 中创建文档之前，我们需要有一个可用的 MongoDB 服务器。您可以使用由 [MongoDB Atlas](https://medium.com/codex/how-to-use-mongodb-atlas-to-manage-your-server-and-data-d97a6e7663c5) 托管的服务器，这是推荐用于生产用途的，或者在您的计算机上安装 MongoDB。但是，出于学习目的，建议使用 Docker 为 MongoDB 启动一个容器。这样，您可以始终使用 MongoDB 的最新版本，并且可以有更灵活的设置。一旦您知道如何使用 MongoDB，您只需要更改凭证来访问您的生产 MongoDB 服务器。

为 MongoDB 启动 Docker 容器的命令是:

```
$ docker network create mongo-net$ docker run --detach --network mongo-net --name mongo-server \
    --env MONGO_INITDB_ROOT_USERNAME=admin \
    --env MONGO_INITDB_ROOT_PASSWORD=pass \
    --env MONGO_INITDB_ROOT_DATABASE=admin \
    --volume mongo-data:/data/db \
    --publish 27017:27017 \
    mongo:5.0.6
```

此外，我们需要安装在 Python 中使用 MongoDB 的包。建议[创建一个虚拟环境](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)并在那里安装软件包，这样他们就不会搞乱系统库。为简单起见，我们将使用 [*conda*](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2) 来创建虚拟环境。我们需要安装[***py mongo***](https://pymongo.readthedocs.io/en/stable/)，这是一个包含使用 MongoDB 的工具的库，是从 Python 使用 MongoDB 的推荐方式。此外，我们将安装 [iPython](https://ipython.org/) ，以便更方便地交互式运行 Python 代码。

```
(base) $ **conda create --name mongodb python=3.10**
(base) $ **conda activate mongodb**
(mongodb) $ **pip install -U pymongo[srv] ipython**
(mongodb) $ **ipython**
```

注意，如果我们为 MongoDB 使用一个 [SRV URI](https://docs.mongodb.com/manual/reference/connection-string/#std-label-connections-dns-seedlist) ，比如 MongoDB Atlas 提供的，我们需要为`dnspython`安装`[srv]`依赖项。

现在一切都准备好了，我们可以开始用 Python 处理 MongoDB 了。

我们应该做的第一件事是创建一个 MongoClient 并连接到 MongoDB 服务器。可以复制下面的 Python 代码，直接在 iPython 内部运行。

建议使用 MongoDB URI 格式来指定主机、端口和认证，可以方便地与`[mongosh](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)`或[MongoDB ide](https://medium.com/codex/how-to-use-mongodb-with-graphical-ides-420597ede80e)共享。

然而，在生产中，我们不应该将认证指定为普通字符串，而应该通过环境变量来指定它们，最好使用 [pydantic](https://lynn-kwong.medium.com/how-to-use-pydantic-to-read-environment-variables-and-secret-files-in-python-8a6b8c56381c) ，这是一个强大而通用的 Python 库，可以非常方便地处理环境变量和秘密。

在 MongoDB 中，我们不需要显式地创建数据库或集合。您只需要访问它们，它们将在文档首次插入集合时创建。通过这种方式，MongoDB 中的数据库和集合被称为是*延迟创建的*。

让我们得到一个将在这篇文章中使用的数据库和集合。如果您已经阅读了 MongoDB 系列文章(可以在本文末尾找到),那么您应该已经有了一个`products`数据库和一个`laptops`集合。为了避免以后出现重复的关键问题，我们将分别把数据库命名为`products2`和集合命名为`laptops2`。

我们使用属性样式来访问数据库和集合。如果数据库和集合名称不是有效的 Python 标识符，比如`my-products`，那么可以用字典样式访问它们，比如`db = mongo_client["my-products"]`。

在进入更高级的聚合管道之前，让我们执行一些基本的 CRUD 操作来插入/读取/更新/删除一些文档。

## **创建文档**

我们可以用`insert_one()`方法创建单个文档，用`insert_many()`方法创建多个文档。如果你对`[mongosh](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)`中的 CRUD 操作有所了解，你只需要在所有字段名上加上引号，并将 JavaScript 中的 **camelCase** 方法改为 Python 中的 **snake_case** 。

请注意，如果您第二次运行以上命令，将会出现重复的键错误。如果我们需要更新一些文档，我们需要使用`update_one()`或`update_many()`。特别是对于`insert_many()`，当重复错误发生时，所有剩余的文件将被忽略。如果您只想跳过重复的并继续插入新的，您可以指定`ordered=False`选项，尽管有`BulkWriteError`。您可以将`insert_many()`放在`try/except`块中，更优雅地处理这种情况:

## **阅读文件**

现在让我们来阅读刚刚插入的文档。我们可以使用`find_one()`来查找第一个匹配的文档，或者使用`find()`来返回一个光标，这个光标可以用来遍历所有的匹配结果。您可以将过滤文档(包含过滤键/值对的字典)传递给`find_one()`或`find()`。如果没有指定过滤文档，所有文档都将被视为匹配。

这里的要点是:

*   指定一个空字典`{}`与不指定任何内容是一样的，这将查找集合中的所有文档，而`find_one()`将只返回第一个文档。
*   默认情况下，将返回匹配文档的所有字段。如果只是想返回一些特定的字段，可以在过滤文档后指定一个投影文档。在投影文档中，关键字是匹配文档中的字段，值为 1 或 0，表示是否应该输出相应的字段。
*   `find()`的工作原理与`find_one()`相同。唯一的区别是返回的是光标，而不是单个文档。我们可以像使用生成器一样使用结果光标，并遍历结果。
*   过滤文档是包含过滤键/值对的字典。键是匹配文档的字段，值是过滤的逻辑。如果我们想要通过默认的等号运算符进行筛选，我们可以直接指定我们想要筛选的值。但是，如果我们想要通过其他操作符进行过滤，我们需要使用类似于`{"price": {"$lt": 10000}}`的嵌套文档来指定条件。运算符应该以美元符号为前缀。这里的`lt`是指 **l** ess **t** 韩。如果您想了解更多关于 MongoDB 的过滤和投影文档，请查看[这篇基本的](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5)和[这篇高级的](https://medium.com/codex/learn-advanced-mongodb-queries-for-nested-documents-using-elemmatch-from-practical-examples-ec432efc2c0f)文章。

## **更新文档**

让我们将笔记本电脑 1 到 5 的数量以及笔记本电脑 2 和 3 的内存更新为 16GB。对于这些操作，我们可以分别使用`update_one()`和`update_many()`方法:

这里的要点是:

*   `update_one()`和`update_many()`的第一个参数是过滤文件，与`find_one()`和`find()`的参数相同。特别是，我们使用`"$in"`操作符来检查一个值是否在数组中。
*   `"$set"`操作符用于为字段设置新值。`"$set"`接受一个包含一组要更新的键/值对的字典。如果您想插入一个集合中没有的新文档，您可以将整个新文档指定为`"$set"`的值，并将`upsert`选项设置为`True`:

*   我们可以用点符号为嵌套文档中的字段设置一个新值。注意`attributes`是一个文档数组，我们可以通过从 0 开始的位置索引来访问它们。这里我们想更新数组第二个文档的内存，因此字段名是`"attributes.1.attribute_value"`。但是，我们不能像上面显示的那样在投影文档中使用位置索引。否则，它们将被用作字段名，输出中不会显示任何内容，因为我们没有名称为 0、1、2 等的字段。你可以自己试一试。

## **删除文件**

同样，我们可以用`delete_one()`方法删除一个文档，用`delete_many()`删除多个文档。这两个方法也接受一个过滤文档作为第一个参数。

上面我们已经介绍了 Python 中 MongoDB 的基本 CRUD 操作。然而，MongoDB 不仅仅是一个 NoSQL 文档数据库。我们可以使用聚合管道对集合中的文档执行复杂的分析。一个常见的任务是对结果进行分组，并获得每组的总数据或平均数据。您甚至可以在分组前过滤、转换或清理数据。

聚合管道由一个或多个按顺序处理文档的阶段组成。每个阶段对输入文档执行一些操作，并将处理过的文档传递给下一个阶段。例如，第一个阶段可以根据一些条件过滤文档，第二个阶段可以对过滤的文档进行分组并进行一些聚合，第三个阶段可以输出结果。

在我们开始用 Python 编写 MongoDB 聚合管道之前，让我们将更多的文档插入到`laptops`集合中，并有更多的数据可以使用。请运行以下命令，并将存储在[这个 JSON 文件](https://gist.github.com/lynnkwong/942310f2c65b672bb22a87cbf3f75de2)中的所有文档插入到`laptops`集合中:

我们可以使用`laptops`集合的`aggregate()`方法来执行聚合。此方法接受按顺序执行的阶段列表。前一阶段返回的文档作为输入传递给下一阶段。现在，让我们编写一个简单的聚合管道来计算每个品牌的可用笔记本电脑数量:

各阶段注意事项:

*   `$match`阶段过滤输入文档，只将符合指定条件的文档传递给下一个管道阶段。在本例中，我们筛选出不再可用的笔记本电脑。
*   `$group`阶段根据`_id`的指定字段对输入文档进行分组，并计算每个组的聚合结果。在本例中，我们按品牌对笔记本电脑进行分组，并计算每个品牌的笔记本电脑总数。
*   `$sort`阶段按照指定的字段以定义的顺序对输入文档进行排序。请注意，`$sort`阶段的输入文档是前一个`$group`阶段返回的文档，而不是原始文档。因此，在`$group`阶段创建的`total`字段可以被`$sort`阶段访问。

如果你想学习更多关于 MongoDB 中聚合管道的知识，强烈推荐你查看[这篇文章](https://medium.com/codex/learn-powerful-mongodb-aggregation-pipelines-from-practical-examples-efead98f08)，其中有更多的例子和更详细的关于聚合管道的公共阶段的介绍。对于用 JavaScript 为`mongosh`编写的例子，你只需要给所有的字段名和操作符加上引号，它们就可以直接在 Python 中运行了。

在这篇文章中，我们简要介绍了如何在 Python 中使用 MongoDB。如果你已经在`mongosh`或者 Compass 中使用过 MongoDB，那么在 Python 中使用应该会非常简单。在`mongosh` /Compass 和 Python 中，查询和管道的语法几乎相同。通常，您只需要将字段名和操作符放在引号中，并将 camelCase 方法改为相应的 snake_case 方法。如果你想在`mongosh`中了解更多关于 MongoDB 的知识，它是通用的，并不局限于特定的编程语言，请查看下面的相关文章。

相关文章:

*   [学习基础知识并开始使用 MongoDB](https://lynn-kwong.medium.com/learn-the-essentials-and-get-started-with-mongodb-8380026642d5?source=your_stories_page----------------------------------------)
*   [从实际例子中学习强大的 MongoDB 聚合管道](https://lynn-kwong.medium.com/learn-powerful-mongodb-aggregation-pipelines-from-practical-examples-efead98f08?source=your_stories_page----------------------------------------)