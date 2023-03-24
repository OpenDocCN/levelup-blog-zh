# 围棋中的单模型方法

> 原文：<https://levelup.gitconnected.com/single-model-approach-in-go-27f5c2633bb8>

![](img/1521d72ff5163017f2de21dcd941d11f.png)

[法院](https://unsplash.com/@courtwhim?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

对于许多应用程序 API，大多数 API 调用最终都是围绕数据库记录的瘦 CRUD(创建、读取、更新、删除)包装器。这意味着在代码中通常会有一个表示数据库记录的结构。您还需要将这些数据封送到网络，以便为您的 API 返回这些数据。在微服务环境中，您通常还有一个 API 客户端，用于将连接格式解析回结构以供使用。许多服务器架构模式建议隔离这些不同的关注点，包括为所有三个地方设置单独的对象。

我在几个地方看到过这种模式。我最近管理了一个用 Java 编写这种模式代码的团队。经常会有由于忘记给一个对象添加新字段而导致的错误。它还需要维护许多方法来将字段从一种表示复制到另一种表示。我曾与一些优步的工程师交谈过，他们出于各种原因在他们的代码中做了同样的事情，比如在一个大型团队中协调客户机/服务器 API 更新的困难，或者使用 gRPC 生成的结构难以进行某些类型的修改。gRPC for Go 将为服务器和客户机默认使用相同的对象生成存根，但是出于其他目的修改生成的代码并不是一个好主意。我还听说一些团队会从原型文件中生成他们自己的客户端，以锁定可能的更改，或者试图将代码从源代码中“分离”。这意味着许多结构都表示同一个数据对象。

当我在 Tonal 看到同样的模式出现时，我质疑是否有必要拥有多个结构，或者是否可以对所有事情使用同一个结构。我总是认为最好是有一个单一的真理来源，所以有一个单一的结构对我来说很有吸引力。我们使用 [gorm](https://gorm.io) 来包装 postgres，并且我们有一个简单的 REST JSON API，所以我们需要一个 DB 模型以及 API 的服务器和客户端。起初，我担心我错过了什么，如果我违背常识，这些东西会回来咬我们。但是考虑到我们的小团队，以及我希望这将防止我在过去看到的错误类型，我冒险决定我们只有一个模型结构。

在系统内工作乍一看确实有点奇怪，因为这个结构既有 gorm(数据库装饰器，如索引和主键)的标签，也有 JSON 的标签。这些都不会在特定的上下文中使用；进行客户端调用的其他服务不知道或不关心另一个服务如何存储/获取记录。而且数据库层不关心 JSON。一旦我们习惯了这种模式，这种结构对我们非常有用。它完全去除了样板复制方法，单一的模型结构意味着数据只有一个真实的来源。

此外，我们使用 gorm 的 [AutoMigrate](https://pkg.go.dev/gorm.io/gorm#DB.AutoMigrate) 特性，该特性基于 struct 中的字段创建和修改数据库结构。添加存储在数据库中并在 API 中返回的新字段只需一行代码。我们利用 struct 标记偶尔隐藏 JSON 序列化或 DB 存储中的字段。

模特的位置也值得注意。我们遵循我在这里更广泛地谈到过的组织结构。TL；dr 是我们有一个规定，不允许跨微服务导入包。执行服务器处理程序的控制器代码和执行数据库操作的存储库代码归微服务所有，它们从顶层共享包中导入模型结构。客户端代码在同一个顶层包中，因为客户端跨多个微服务使用。这里有一个例子:

```
workouts/            // business logic shared lib
    client.go        // client APIs for *Workout
    models.go        // contains Workout struct
cmd/
    workouts-service/
        main.go
        app/         
            setup.go      // gorm AutoMigrate for *workouts.Workout
        workouts/    
            controller.go // server APIs for *workouts.Workout
            repo.go       // DB access for *workouts.Workout
```

我们还有许多 API，它们与数据库不是一一对应的，在这种情况下，我们有单独的模型。但是对于大多数人来说，这种方法在过去的 4 年里非常有效，没有严重的副作用。该解决方案干净、简单且易于维护，可能也非常适合您的 Go 微服务。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容在[关卡升级编码](https://levelup.gitconnected.com/)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)
*   🚀👉 [**软件工程师的热门职位**](https://jobs.levelup.dev/)