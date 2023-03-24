# 具有 NodeJS 的 DynamoDB 库

> 原文：<https://levelup.gitconnected.com/dynamodb-library-with-nodejs-1ca38abdb355>

![](img/cb13abecfade39b2bd5dea0c9a1b2b16.png)

[GitHub 资源库](https://github.com/RodionChachura/awsdynamoutils)

# 介绍

这个故事的目标是通过解释在不同项目中帮助过我的库，展示一些有用的函数，可以帮助你解决在处理 DynamoDB 时出现的常见问题，这个库在增加者的 [Pomodoro 中被积极使用。](https://pomodoro.increaser.org)

我在第一份工作中决定使用 DynamoDB，因为它很容易上手，而且不需要额外的努力就可以从部署的服务连接到它。因为那份工作中的其他微服务也使用 DynamoDB，同时我在做一个使用相同数据库的副业项目，我创建了一个小的 [NPM 模块](https://www.npmjs.com/package/awsdynamoutils)，它具有可重用的功能，以避免复制粘贴。

在使用了这个库一年多之后，我决定写一写它，因为它对我帮助很大。该库由一个文件组成，我们将采用自底向上的方法来更好地理解其中发生的事情。

不再拖延。你有时间实现目标！ [**增员会引导你。**](https://increaser.org)

![](img/811da9ee4841a30aaebecff8df4718fc.png)

[**实现你的抱负！**](https://increaser.org/)

# 开始

您可以通过克隆[库](https://github.com/RodionChachura/awsdynamoutils)并继续这个故事，或者通过创建一个文件并安装我们将需要的两个库来继续。

```
npm install aws-sdk uuid
```

首先，我们将导入我们需要的所有库，并创建一个返回包含 utils 的对象的函数。该模块将导出该函数和该函数调用的结果。

getModule()

看起来很奇怪，为什么我们需要导出函数来返回包含 utils 的对象？

当我们在本地开发一个服务时，我们通常在 docker 中启动 DynamoDB。为了让`DocumentClient`了解它应该连接到哪个数据库，我们向它传递一个配置对象。当应用程序在 AWS 上运行时，我们不需要指定任何内容，因为它可以从环境变量中获取配置。

在[Pomodoro by increator](https://pomodoro.increaser.org)中，我只导入库一次，传递一个 config 给它，然后像这样导出模块:

utils/db.js

# 创建表格

通常，我们通过 Terraform、CloudFormation 或在 AWS 中手动创建 DynamoDB 表用于生产，但是，当我们想要在本地测试应用程序时，在运行测试之前以编程方式创建表更容易。

要创建一个表，我们需要有一个描述表的相当大的对象。为了简化表的创建过程，我们将编写一个函数，用一个单键和另一个组合键为表构造对象。

tableParams()

几乎在任何应用程序中，我们都有用户，通常主键是字符串形式的 ID。

tableParams()示例

在[Pomodoro by increator](https://pomodoro.increaser.org)中，为了有测试用的表，我构造了参数，然后遍历它们来创建表。

tableParams()真实示例

# 只拿我们需要的东西

通常情况下，我们只想获取项目的特定字段，DynamoDB 允许我们通过在参数中指定 *ProjectionExpression* 来做到这一点。

通常，我们还需要将*expression attributes name*放在 parameters 对象中，因为我们想要投影的字段可以与保留关键字同名。比如我们要拿用户项目，就不需要 *ExpressionAttributeNames* 。

```
{
    ProjectionExpression: "projects"
}
```

如果我们还想获得用户名，我们应该将 *ExpressionAttributeNames* 添加到参数中，因为该名称是保留的 DynamoDB 关键字。

```
{
    ProjectionExpression: "projects, #name",
    ExpressionAttributeNames: {
        "#name": "name"
    }
}
```

我们可以看到这可能相当麻烦。最好有一个函数，我们可以向它传递一个所需字段的数组，以接收一个包含 *ProjectionExpression* 和*expression attributenames*的对象。

projectionExpression()

让我们测试这个函数，看看我们会得到什么。

projectionExpression()示例

# 获取所有项目

当我们扫描或查询一个有很多条目的表时，由于分页的原因，我们将按部分接收数据。但是我们可以写一个函数来一次得到全部。

该函数接收我们想要执行的方法(scan 或 equery ),并返回一个函数，该函数返回具有分页意识的方法调用的结果。

paginationAware()

我们可以使用该函数通过无条件扫描来获取表中的所有项目。

getAll()

比如在[Pomodoro by increator](https://pomodoro.increaser.org)中，我用这个函数获取用户提出的所有特性。

```
const features = await getAll(TABLE_NAME.FEATURES)
```

# 搜索项目

当我们有一个具有组合键的表时，我们可能希望找到具有相同键部分的条目。

searchByPRParams()

具有组合键的表的例子是具有共享项目的表。其中散列键是项目的 id，范围键是用户的 id。如果我们想找到共享项目的用户的 id，我们将编写如下所示的函数。

有时我们希望找到所有共享相同关键字的项目，例如，找到所有在同一个国家的用户。唯一的变化是——我们用 *FilterExpression* 替换了 *KeyConditionExpression* 。

searchByKeyParams()

常见的用例可能是检查具有给定电子邮件的用户是否已经存在。

searchByKeyParams()示例

# 合并参数

在前面的例子中，我们使用下面列出的函数合并了两个参数对象。

合并参数()

这个函数检查字段是数组还是对象，并相应地采取行动，所以在合并之后，我们不会丢失一个重要的参数。在下面的例子中，我们可以看到函数如何解决两个对象共享相同属性的情况。

mergedParams()示例

# 身份

通常，我们的表中的主键是一个随机的字符串。让一个函数创建一个 id，让一个函数接收一个对象并返回一个带有 id 的对象，这将是非常有用的。

getId()和 withId()

我们使用这个库来生成 id 并删除 DynamoDB 不允许的符号。

getid 和 withId()示例

# 更新项目

通常，我们可能需要更新 item 属性的值。

setNewValue()

例如，我在[Pomodoro by increator](https://pomodoro.increaser.org)中使用这个函数来更新用户项目。

setNewValue()真实例子

如果我们想一次更新几个参数呢？

flatUpdateParameters()

例如，我们可以使用这个函数来更新用户地址。

flatUpdateParams()示例

# 获取特定项目

我们有一个主键，我们想得到相应的项目。

getByPK()

在[番茄大战中](https://pomodoro.increaser.org)，我为每张桌子都准备了一个专用文件。我从 *db/users.js* 中导出函数，通过 id 获取用户。

getByPK()真实例子

# 列表操作

在 JS 中，我们可以像这样轻松地将一个数组合并到另一个数组中:

```
const newArray = [...another, ...one]
```

如果我们的图书馆也有类似的功能就好了。

mergeInList()

如果我们想做这样的事情呢？

```
array.push(element)
```

putToList()

在 Pomodoro by increaser 用户可以有项目，当他创建一个项目时，下面显示的函数被调用。

putToList()现实生活中的例子

我们库中的最后一个函数将帮助我们通过数组元素的索引来移除它。

removeFromListByIndex()

我们可以想一个例子，我们必须删除带有给定索引的项目。

removeFromListByIndex()示例

我们完成了一个小型的 DynamoDB 库，涵盖了大多数操作。如果您有任何问题，请留下您的评论。如果您想做一些改进或添加功能，请向[库](https://github.com/RodionChachura/awsdynamoutils)发出一个 pull 请求。

借助[increaser.org](http://increaser.org/)，专注度和工作效率更上一层楼。

![](img/e473170141022bbe35276ab0d6f31399.png)

[增加器](https://increaser.org)