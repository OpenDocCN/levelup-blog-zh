# 转到项目结构

> 原文：<https://levelup.gitconnected.com/go-project-structure-5157f458c520>

![](img/ddd18a44e2542ad0ce7308cc5b02c3ac.png)

Maksym Kaharlytskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在一个大项目中组织 go 代码可能很难。 [Go 的标准包命名约定](https://go.dev/blog/package-names)很棒，但是例子过于简单，更多的是与库代码相关。有这么多公司使用 Go 作为他们的后端，我很惊讶在一个大的服务器项目中，包/文件夹组织的例子如此之少。在 [Tonal](https://www.tonal.com/) ，我们有一个支持许多微服务的 monorepo，经过过去四年的多次迭代，我认为我们终于有了一个良好的基本结构，它将很好地服务于我们的未来。Tonal 生产一种连接的力量训练设备，所以我的业务逻辑示例将与锻炼相关。

我们有大约 40 个微服务，每个微服务都有特定于它们支持的 API 的相关业务逻辑。为了将这些组织在一起，服务编译单元被放在我们根目录下的一个`cmd`子目录中。每个服务目录遵循模式`{serviceName}-service`。这不是一个有效的包名，因为这个文件夹只包含使用主包的`main.go`。我们有一个约定，所有服务都应该从这里开始本地开发，只有`go run main.go`。这个目录也有非 go 服务特定的设置，比如用于 Kubernetes 配置的`Dockerfile`和`k8s`目录。

服务目录还有一个`app`包，负责服务配置和启动。这个包在所有的微服务中都有一个通用的名称，为初始化依赖关系和注册服务支持的路由提供一个通用的起点。它还保持了`main.go`的紧凑性，因为大多数初始化都在服务应用包中。拥有这些的静态命名约定允许使用我们的标准引导和 CI/CD 配置生成新服务的代码。

最初，在每个服务目录中，我们有一个包含 API 处理程序的`controllers`目录，通常还有一个包含数据库访问代码的`repos`目录。我现在认为这个会议是个错误。随着代码的增长，这些目录变得越来越大，我觉得它们现在太通用了，就像通用的`utils`包一样，严重混淆了关注点。我的一个同事提出了一个更好的解决方案。他创建了基于功能而不是通用角色的包。

而不是:

```
controllers/
    workouts.go
repos/
     workouts.go
```

执行以下操作:

```
workouts/ 
    controllers.go
    repos.go
```

这一更改在文件浏览级别给出了完全相同的信息，但它使代码更简单，因为没有通用的包前缀。例如，从控制器包中调用的回购方法`FetchWorkout`可能是`repos.FetchWorkout`，但当代码在同一个包中时，它只是`FetchWorkout`。这更符合围棋的习惯。通常，以这种方式组织的代码只在包内使用，不需要导出，这使得修改更容易。

任何跨服务共享的代码都会根据功能放入根的顶层包中。这些包中的大多数都有特定于我们公司的业务逻辑或特性的名称，例如:`workouts`、`weights`和`leaderboard`。其他的是特定的实用程序包，如`service`，它提供了用于启动微服务和向标准监控和中间件注册 API 端点的通用实用程序。其他例子有`response`，它包含标准 API 响应的帮助器，以及`pg`，它允许我们的 postgresql DB 连接的通用配置。

基于微服务，我们需要共享客户端来调用其他服务。这意味着我们通常在顶层有与包含客户端代码的服务名称相匹配的包。这是将服务目录下推到`cmd`目录以最小化导航混乱的一个重要原因。我们有一个政策，任何服务都不能从另一个服务导入包，所以如果任何代码需要共享，它必须被提升到顶级目录中。

以下是我们顶级 git 回购的完整示例:

```
cmd/
    workouts-service/
        main.go
        app/         // service setup
            setup.go
        workouts/    // business logic
            controller.go
            repo.go

        k8s/         // kubernetes config
        Dockerfile    // deployment packaging
    media-service/
        ...          // same layout as workouts-service
workouts/            // business logic shared lib
    client.go
    models.go
response/           // generic shared lib
    errors.go
pg/                 // generic shared lib
    connection.go
```

这种组织是简单的，并且是有意扁平的，以去除不必要的目录分类(no `pkg` dir)。我认为许多公司可能会使用类似的结构，但我认为每个公司的项目看起来会非常不同。层次结构中很少有静态名称，遵循这种方法需要根据您的业务逻辑选择好的名称。

交流可以跨问题域使用的静态名称要比交流特定于应用程序的好名称容易得多。Ruby on Rails 使用类似于`models`或`controllers`的文件夹名。我们在早期使用了这种方法，但是由于代码中使用包名的方式，它很快变得非常混乱。它还将域与代码混合在一起，将不同的特性混合在一起。使用通用的包名最终会变成样板文件，传达的信息很少。这里使用的唯一通用包是`app`包，它只在`main.go`中使用，所以不会浪费业务逻辑代码。

甚至我在这篇文章中使用的例子也不是真实的，因为实际的名字是如此的领域特定，以至于不被广大读者所理解。我很幸运，我开发的产品大多数人都不知道什么是锻炼。每个开发人员最了解他们公司的领域，应该在结构中选择反映这些问题的名称。

如果其他人已经找到了组织他们的服务器 Go 代码的好方法，我很乐意在评论中听到。