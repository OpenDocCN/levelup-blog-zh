# 使用 Node.js 利用 MongoDB 的“变更流”实现实时物联网应用

> 原文：<https://levelup.gitconnected.com/taking-advantage-of-mongodbs-change-streams-for-real-time-iot-applications-using-nodejs-a0a76ae1376d>

![](img/72b1c156d4c2845fb1faa86ea7d39cc6.png)

图片来自 [Pexel](https://www.pexels.com/@cookiecutter)

# 介绍

几个月前，我参与了一个项目，该项目有一个微控制器，它带有几个传感器，连接到一台 PC，不断地向它输入新数据。PC 将存储和处理这些数据，生成事件触发器，通过套接字发送到网页。我需要一种方法来存储每个新事件，同时保持网页实时更新。

# 为什么是 MongoDB？

MongoDB 是一个 NoSQL(非关系)数据库，这意味着它不需要预定义的表模式。这在处理大量非结构化数据时尤其有用，这些数据在项目的生命周期中可能会改变，也可能不会改变。鉴于项目规格的简单变化可能需要添加新的传感器，或者从这些传感器存储的新数据，非关系数据库完全符合我的需求。

# 节点服务器

尽管有其他用于服务器开发的框架和编程语言，但 Node 已被证明易于实现和部署。我利用了 [Express](https://medium.com/javascript-scene/introduction-to-node-express-90c431f9e6fd) 并用 Vue 开发了网页，因为……让我们面对现实吧，Vue 简直棒极了。

因此，让我们深入研究代码:

*server.js* :

```
var MongoClient = require('mongodb').MongoClient;const pipeline = [{$project: { documentKey: false }}];MongoClient.connect("mongodb://localhost:27017?replicaSet=mongo-repl", { useNewUrlParser: true }, function(err, database) { if (err) throw err; var db = database.db("demo-db"); console.log("[mongodb] Mongo connection established"); // Show database docs for debugging console.log("[mongodb] Mongo db dump: "); db.collection("data").find({},{fields: {"_id" : false }}).toArray(function(err, result) { if (err) throw err; console.log(result); }); const changeStream = db.collection("data").watch(pipeline); changeStream.on("change", function(change) { dataObj = {"sensor" : change.fullDocument.sensor} console.log(dataObj); });});
```

为了运行这段代码，首先需要做几件事情。

*   你需要[安装](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) MongoDB 服务器，点击链接查看如何在 Ubuntu 上安装的详细说明。
*   安装后，您需要在项目文件夹中创建一个特定的文件夹。

假设您的项目在`~/project,`上，您需要运行:

```
cd ~/project
mkdir data
```

然后(这是关键部分):

```
mongod --port 27017 --dbpath ./data --smallfiles --replSet mongo-repl
```

在另一个终端运行中:

```
mongo --port 27017 --eval 'rs.initiate({"_id":"mongo-repl","members":[{"_id":0,"host":"localhost:27017"}]})'
```

好吧，那这是怎么回事？基本上，在为我们所有的数据库信息创建一个文件夹后，我们需要运行`mongod`服务(我们刚刚安装的)并为您的 ***副本集*** 指定端口和路径。是的，一套复制品。这就是我第一次尝试这种方法时遇到这么多麻烦的原因。为了能够*实时观察我们数据库中的*变化(在 mongodb 的[文档](https://docs.mongodb.com/manual/changeStreams/)中定义为*变化流*)，您需要运行的不是我们 db 的一个实例，而是一个 ***集群*** 。这基本上就是你用上面的代码做的事情。

第一行创建副本集，指定您最近创建的`data`文件夹，以便集群在那里存储它的信息。您还告诉`mongod`我们的集群将在哪个端口运行，以及其他一些选项，参数`replSet`是我们集群的名称。

之后，您需要进入`mongo` shell 并初始化我们最近创建的集群，这是第二个命令所做的。

*几个注意事项:*

*   副本集只需要初始化一次，没有必要每次都运行那个命令。
*   如果您将它作为脚本运行，可以考虑在 mongod 命令的末尾添加'&'，以避免使用多个终端。

一旦你完成了所有这些，我们就准备好运行我们的 *server.js* ，所以:

```
cd ~/project
npm init
npm install mongodb --save
node server.js
```

(我假设您熟悉 Node 和 npm)

现在，您可以在不同的终端中运行另一个脚本，在最近创建的数据库中插入一个新项目，并查看`server.js`如何在集群中打印每个新插入的项目。在新的终端运行中:

```
mongo
```

这将打开 Mongo 的控制台，现在我们可以与我们的数据库进行交互:

```
use demo-db
db.data.insert({sensor: 'Sensor A'})
```

就像这样，在我们的 *server.js* 终端中，我们应该看到 DB insert 作为控制台日志出现。

# 结论

物联网不仅改变了我们看待硬件的方式，也改变了我们设计软件的方式。要求通常包括能够实时处理数据，并将其存储用于机器学习和大数据分析。所有这些需求都会对我们的服务器应用程序产生影响，因此选择正确的工具至关重要。

在数据存储方面，MongoDB 已经被证明是一个非常灵活的物联网应用工具。如果一切顺利，你应该能够将这个简单的例子集成到你的项目中。

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/mongodb) [## 学习 MongoDB -最佳 MongoDB 教程(2019) | gitconnected

### 8 大 MongoDB 教程-免费学习 MongoDB。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/mongodb) [](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### 前 31 个 Node.js 教程-免费学习 Node.js。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/node-js)