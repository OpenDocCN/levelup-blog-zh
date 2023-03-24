# 序列中的表关系

> 原文：<https://levelup.gitconnected.com/table-relationships-in-sequelize-2e2533580c2a>

## 设置外键并制作 ORM 连接表

![](img/3d3e2251c472b5b1f3a5dcc73e035090.png)

由[阿贝尔·Y·科斯塔](https://unsplash.com/@abelycosta?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

开始使用 Sequelize 可能会有点令人生畏。但是对于那些更熟悉 JavaScript 和面向对象编程的人来说，它也可以使使用数据库变得更加容易。在这篇文章中，我将关注这个 API 的关系映射方面:设置外键和创建连接表。

## 介绍

Sequelize 是一个节点包，它在服务器或后端代码与 SQL 数据库(MySQL、SQLite、PostgreSQL 或 MSSQL)之间执行对象关系映射(ORM)。ORM 是什么？它基本上使用面向对象的代码在不兼容的数据类型系统(即 SQL 和 JavaScript)之间转换数据。使用 Sequelize，我们可以使用 JavaScript 创建数据库和表，以及对数据库的查询。

## 模型

Sequelize 不是创建表，而是使用模型来创建表。JavaScript 中将使用一个模型来存储属性(即表中的字段或列)，以及它与其他模型/表的关联和它查询数据库的方法。类似于在 SQL 中创建表，当您定义一个模型时，您还将定义每个键/列的数据类型。

在这篇文章中，让我们想象一个数据库，其中有用户、项目和一个连接表，用于按项目跟踪所有用户。在我们分配它们之间的关系之前，我们需要制作模型:

## 联合

在 SQL 中，我们设置外键来连接表，并且通常建立表之间的关系——数据库不知道您的表和它们的键之间的关系是什么类型。而在 Sequelize 中，我们实际上可以声明两个表之间的关联类型。

示例数据库中有两种类型的关系:

*   每个项目都有一个项目经理(用户):一对一的关系
*   每个项目可以有许多成员(用户),每个用户可以有许多项目:多对多关系

在数据库中建立模型之后，可以声明关联。这里有三种顺序关联类型:`belongsTo`、`hasMany`和`belongsToMany`。下面是它们的使用方法:

`belongsTo` & `hasMany`关联设置了多对一的关系。每个项目都有一个项目经理，那个项目*属于那个用户*，而那个用户*可以有许多*项目。Sequelize 将知道用户的外键将在项目表上设置。

请注意，外键的列标题可以设置为这些方法中的第二个参数。这一步不是必需的——Sequelize 将自动命名外键`user_id` 或`userId`，这取决于您是否启用了 camelCase 或 snake_case(参见文档中的[命名策略](https://sequelize.org/master/manual/naming-strategies.html))。在我们的例子中，我们希望首先拥有`id`,并注意这个用户是项目经理。

对于设置多对多关联和创建连接表，需要在两个模型上使用`belongsToMany`。每个用户可以*属于多个*项目，每个项目可以*属于多个*用户。这些方法调用中的每一个都将在结果连接表中设置外键。注意，`through`关键字在这里设置了连接表的名称。您可以在不创建模型的情况下创建多对多连接表，但是，如果您希望将数据存储在默认 id 列之外的列中，模型会很有用(请参见模型部分中的上一个示例)。

在 Sequelize 中还可以进行其他的关联，比如一对一，我在这里没有涉及到。在文档中阅读更多关于可以在 Sequelize 中设置的所有[关联](https://sequelize.org/master/manual/assocs.html)的信息。

## 方法

Sequelize 的模型和关联的强大之处在于它们提供了查询数据库的方法。下面我有两个例子，一个用于添加项目，另一个用于在连接表中链接项目和用户。

(注意，这里我使用的是 async/await——sequel ize 方法是异步的并返回承诺，您可以使用 ES6 承诺或 async/await 来处理它们。)

在第一个例子`addProject`中，我们使用一些基本的模型方法在表中查找和创建记录。一旦我们找到了用户并创建了新项目，我们就需要真正地*设置*外键。先前建立的关联为我们的模型生成了*获取器*和*设置器*。这里，我们使用自定义设置器`setUser`，将用户的 id 指定为新项目的外键。注意，从数据库返回的整个记录需要传递到这个方法中，以分配外键。

在第二个例子`joinUserProject`中，我们正在创建连接表。我们需要检索这两个记录，并使用另一个定制生成的方法`addUser`来连接它们。

为所有这些关联生成自定义方法。我只展示了两个，`set`和`add`。例如，项目和用户之间的`hasMany`关联会生成以下特殊方法:

*   `project.getUsers()`
*   `project.countUsers()`
*   `project.hasUser()`
*   `project.hasUsers()`
*   `project.setUsers()`
*   `project.addUser()`
*   `project.addUsers()`
*   `project.removeUser()`
*   `project.removeUsers()`
*   `project.createUser()`

在文档中阅读更多关于[序列特殊方法](https://sequelize.org/master/manual/assocs.html#special-methods-mixins-added-to-instances)的信息。

## 要使用哪个关联

很难确定要建立哪种关联，但是有一些策略可以帮助你确定这一点。首先是从目标和源模型的角度来考虑。源是外键来自的地方，目标是外键将存在的模型或表:

```
sourceModel.hasOne(targetModel)
sourceModel.hasMany(targetModel)
targetModel.belongsTo(sourceTable)
```

上面这段有用的代码片段来自 [Loren Stewart 关于同一主题的博客](https://lorenstewart.me/2016/09/12/sequelize-table-associations-joins/)，绝对值得一试。

另一个策略是尝试两种方法，然后在数据库中检查结果。如果你没有在正确的地方看到外键，你很可能把它弄反了！这里有更多关于为什么关联是成对定义的，以及如何确定使用哪一个:[为什么关联是成对定义的？](https://sequelize.org/master/manual/assocs.html#why-associations-are-defined-in-pairs-)

## 结论

在设置和使用序列式关联方面，还有很多需要探索的地方。在这篇文章中，我介绍了 Sequelize 对象关系映射的基础——使用这个 ORM 设置外键，在 SQL 数据库中创建连接表，并使用这些关联创建的一些特殊方法。Sequelize 提供了一种关系数据库查询方法，对于熟悉面向对象编程的人来说，这种方法可以使设置和处理数据变得更加容易和快速。