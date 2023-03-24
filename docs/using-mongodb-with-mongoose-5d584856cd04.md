# 使用 MongoDB 和 Mongoose

> 原文：<https://levelup.gitconnected.com/using-mongodb-with-mongoose-5d584856cd04>

![](img/46a7b948d3d36b352e51cceb25872177.png)

照片由[马特·伊森](https://unsplash.com/@matteason?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将了解如何使用 Mongoose 来操作我们的 MongoDB 数据库。

# Mongoose 入门

我们可以通过运行以下命令来安装该软件包:

```
npm install mongoose --save
```

然后我们可以通过写来使用它:

```
const mongoose = require('mongoose');
const connection = "mongodb://localhost:27017";
mongoose.connect(connection, { useNewUrlParser: true });
const db = mongoose.connection;
db.on('error', () => console.error('connection error:'));
db.once('open', () => {
  console.log('connected')
});
```

我们使用`mongoose.connect`方法将我们的 MongoDB 数据库与 Mongoose 连接起来。

然后为了看我们是否连接成功，我们可以监听`open`事件。

# 定义模式

现在我们可以定义一个模式来限制我们可以放在文档中的内容。

这是原生 MongoDB 客户端所不具备的。

例如，我们可以写:

```
const mongoose = require('mongoose');
const connection = "mongodb://localhost:27017";
mongoose.connect(connection, { useNewUrlParser: true });
const db = mongoose.connection;
db.on('error', () => console.error('connection error:'));
db.once('open', () => {
  console.log('connected')
});const kittySchema = new mongoose.Schema({
  name: String
});const Kitten = mongoose.model('Kitten', kittySchema);
const baby = new Kitten({ name: 'james' });
baby.save((err, baby) => {
  if (err) {
    return console.error(err);
  }
  console.log(baby.name);
});
```

我们用`mongoose.Schema`构造函数创建模式。

该对象将字段作为键，将数据类型作为值。

然后我们用`mongoose.model`方法定义模型。

然后我们使用`Kitten`构造函数，然后我们调用`save`将`Kitten`对象保存到数据库中。

将为每个插入的条目自动生成`_id`。

如果我们想得到条目，那么我们可以调用`find`方法:

```
const mongoose = require('mongoose');
const connection = "mongodb://localhost:27017/test";
mongoose.connect(connection, { useNewUrlParser: true });
const db = mongoose.connection;
db.on('error', () => console.error('connection error:'));
db.once('open', () => {
  console.log('connected')
});const kittySchema = new mongoose.Schema({
  name: String
});const Kitten = mongoose.model('Kitten', kittySchema);
const baby = new Kitten({ name: 'james' });
baby.save((err, baby) => {
  if (err) {
    return console.error(err);
  }
  console.log(baby.name);
});Kitten.find((err, kittens) => {
  if (err) return console.error(err);
  console.log(kittens);
})
```

如果我们想找到一个特定的条目，我们可以将一个对象传递给`find`的第一个参数:

```
const mongoose = require('mongoose');
const connection = "mongodb://localhost:27017/test";
mongoose.connect(connection, { useNewUrlParser: true });
const db = mongoose.connection;
db.on('error', () => console.error('connection error:'));
db.once('open', () => {
  console.log('connected')
});const kittySchema = new mongoose.Schema({
  name: String
});kittySchema.methods.speak = function () {
  console.log(`hello ${this.name}`);
}const Kitten = mongoose.model('Kitten', kittySchema);
const baby = new Kitten({ name: 'james' });
baby.speak();
baby.save((err, baby) => {
  if (err) {
    return console.error(err);
  }
  console.log(baby.name);
});Kitten.find({ name: /^james/ },(err, kittens) => {
  if (err) return console.error(err);
  console.log(kittens);
})
```

如果我们想添加方法，我们可以写:

```
const mongoose = require('mongoose');
const connection = "mongodb://localhost:27017/test";
mongoose.connect(connection, { useNewUrlParser: true });
const db = mongoose.connection;
db.on('error', () => console.error('connection error:'));
db.once('open', () => {
  console.log('connected')
});const kittySchema = new mongoose.Schema({
  name: String
});kittySchema.methods.speak = function () {
  console.log(`hello ${this.name}`);
}const Kitten = mongoose.model('Kitten', kittySchema);
const baby = new Kitten({ name: 'baby' });
baby.speak();
baby.save((err, baby) => {
  if (err) {
    return console.error(err);
  }
  console.log(baby.name);
});Kitten.find((err, kittens) => {
  if (err) return console.error(err);
  console.log(kittens);
})
```

我们通过向`methods`对象属性添加一个方法，向`Kitten`模型添加了一个方法。

# 结论

我们可以用 Mongoose 创建模式、添加项目和查找项目。