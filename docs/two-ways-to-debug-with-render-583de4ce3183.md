# 使用 Render 进行调试的两种方法

> 原文：<https://levelup.gitconnected.com/two-ways-to-debug-with-render-583de4ce3183>

![](img/b231ca2d055299f4cbebdd8e16300064.png)

在 [Unsplash](https://unsplash.com/s/photos/coffee-programmer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Austin Distel](https://unsplash.com/es/@austindistel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

## 咖啡店里的编码

## 使用数据狗和日志流轻松调试您的 web 应用程序

Render 是 web 应用程序基础架构的一站式商店，简化了静态内容、数据库、微服务等的托管。无论您是维护一个整体应用程序还是采用微服务模式，并且您还没有准备好创建一个完整的 SRE 团队，Render 都是满足您的基础架构和托管需求的好选择。

在本文中，我们将重点讨论 Render 中的调试。我们将使用 PostgreSQL 数据库构建一个简单的 Node.js 微服务。我们将使用 Render 部署这两个组件，然后演示两种不同的方法来调试我们的服务和数据库。我们将使用日志流来访问 syslogs，使用 Datadog 来访问可观察性指标和警报。

你准备好了吗？让我们开始吧。

# 使用 Render 托管 Node.js 应用程序

本文的目的是*而不是*深入构建 Node.js 微服务应用程序。但是，如果您对 Node.js、TypeScript、Express 和 PostgreSQL 有一些经验，您可能会在这里展示的代码中发现额外的价值。

我们的迷你演示应用程序是一个用户管理 API，带有几个典型的端点，用于检索、创建、更新或删除用户。在后台，该服务对 PostgreSQL 数据库执行查询操作。你可以在这里找到完整的应用代码[。](https://github.com/alvinslee/render-nodejs-debugging)

## 设置 Datadog 帐户

在设置 PostgreSQL 实例之前，我们需要得到一个 Datadog API 密匙。你可以[注册一个免费试用账户](https://app.datadoghq.com/signup)，然后按照[这些说明](https://docs.datadoghq.com/account_management/api-app-keys/#add-an-api-key-or-client-token)获取一个 API 密匙。我们将很快更详细地讨论 Datadog。

## 部署 PostgreSQL 实例

我们将从 PostgreSQL 实例开始，因为在使用我们的用户服务之前，我们需要数据库凭证。为你的项目创建一个实例，登录[渲染](https://dashboard.render.com/)，点击右上角的**新建+** 按钮。然后，点击 **PostgreSQL** 开始该过程。这将使您进入一个配置数据库的表单:

![](img/0b6b0c9edc2a7bd0bb0045689d030006.png)

为您的实例指定一个名称，以及用于访问的数据库名称和用户名。对于“Datadog API Key”字段，现在*添加这一点很重要*，因为在数据库创建后很难添加。粘贴您在上一步中生成的 API 密钥。

点击**创建数据库**并等待初始化完成。Render 将显示新数据库的凭据。稍后在设置用户服务时，您将需要这些信息。

## **初始化数据库模式**

还要注意，我们需要在这个新数据库中初始化模式，创建一个包含适当列的用户表。我们将通过`node-pg-migrate`包来实现这一点。初始化数据库的说明在代码库的 [README](https://github.com/alvinslee/render-nodejs-debugging/blob/master/README.md) 文件中。然而，我们将把这个步骤捆绑到应用程序的 Render 启动命令中。

## 部署用户微服务

使用 Render 部署微服务非常简单，尤其是如果您将代码托管在 [GitLab](https://render.com/docs/gitlab) 或 [GitHub](https://render.com/docs/github) 中。将代码托管帐户连接到渲染后，返回到渲染仪表板。点击**新建+** 按钮，选择 **Web 服务**。使用您的微服务选择代码库。

![](img/45df0b85a8e693aa1baf476cb0da4180.png)

在这个页面上，每个输入字段都有详细的文档记录。对于我们的示例 Node.js 服务，我们希望确保以下几点:

*   “环境”应设置为`Node`。
*   “构建命令”应该是`yarn install && yarn build`，这样所有的依赖项都被安装，最终的系统被构建。
*   这个特定产品的“启动命令”(未显示)将运行我们的数据库迁移并启动我们的应用程序。应该是:`yarn migrate up --no-reject-unauthorized && yarn start:prod`

此外，“高级”菜单包含我们需要为这个项目填写的其他字段。

![](img/106975e0975be5e4f76f0023f4303557.png)

我们需要为数据库连接设置五个环境变量:

1.  `DB_HOST`
2.  `DB_NAME`
3.  `DB_USER`
4.  `DB_PASSWORD`
5.  `DATABASE_URL` —这是`node-pg-migrate`使用的数据库连接字符串。它的值是`postgres://DB_USER:DB_PASSWORD@DB_HOST:5432/DB_NAME`，填充了这些占位符的实际值。例如:`postgres://john:Ue473C@dpg-1g50-a.oregon-postgres.render.com:5432/mydb`

这些凭证应该来自您在上一步中创建的 PostgreSQL 实例。

最后，确保将“健康检查路径”设置为`/health`，以促进渲染中的零停机部署和健康监控。

点击**创建 Web 服务**。Render 将发挥它的魔力，从您的存储库中部署这个服务！

# 使用 Datadog 调试

Datadog 是一个基于云的 SaaS，提供广泛的日志聚合和搜索工具以及监控和警报功能。无论您有单个服务还是一万个服务，Datadog 都是一个有用的工具，可以帮助您更深入地了解您的资源是如何运行的。

我们已经通过在创建过程中包含 Datadog API 键将 PostgreSQL 实例连接到了 Datadog。这为该数据库实例上的许多不同指标提供了监视功能。PostgreSQL 支持的指标的完整列表可在[这里](https://render.com/docs/datadog#available-metrics)获得。让我们看看如何使用这些指标进行监控和警报。

## 探索度量标准

登录您的 Datadog 帐户并导航至**指标**页面。

![](img/fabf5875cfb8f7cd9f03ee482a9816a2.png)

**指标浏览器**的第一行显示了*指标查询*工具栏，带有各种下拉菜单和输入框，用于过滤图表中显示的指标。显示默认指标`system.cpu.user`。如果您的数据库使用 API 键进行了正确配置，那么您应该会看到一个折线图，显示 CPU 运行用户空间进程所花费的时间百分比。

当您单击“from”下拉菜单时，您将看到数据库指标的两个潜在来源:

![](img/e043036bd97ab708f666e5670ffef964.png)

可以基于主机中的特定数据库或整个主机来显示度量。对于我们的示例，主机上只有一个数据库实例在运行。因此，使用这个特定的指标，特定于数据库和特定于主机的指标之间没有区别。

如果您想调查不同的指标，选择第一个输入框并输入`system.mem`。自动完成将显示不同的内存相关指标:

![](img/c123e3df84ea10d421ba496ca71ee847.png)

如果您选择`system.mem.used`，您将开始看到数据库主机正在使用的内存。请注意，该对话框提供了所选指标的描述。

Datadog 中提供的 PostgreSQL 特定指标也很有趣。例如，我们可以随时监控数据库的活动连接数。

![](img/2befb510a5eaae8065dec15142d036a7.png)

在这个查询中,“from”字段使用单数据库实例，Datadog 根据指标上的`database-id`属性对数据求和。

监控是一种有用的调试工具。如果您的应用程序数据库响应缓慢，您可以在 Datadog 中查看当前指标。通过将查询时间或连接计数等 PostgreSQL 指标与应用程序响应时间相关联，您可能会发现一个与特定端点或特定代码相关联的编写不良的查询，该查询不正确地清理了数据库连接。访问这些指标可能会提供解决问题的关键。

当然，解决问题通常取决于您对问题发生的意识。

幸运的是，Datadog 为*提供了大量资源，用于在您的系统出现性能下降时向*发出警报。您可以配置这些警报来监控您选择的指标，将通知推送到各个地方。

让我们谈谈作为调试 web 应用程序的工具的警报。

## Datadog 中的警报

从 Datadog 导航栏中选择**监视器**，然后选择**新监视器**并选择**公制**监视器类型。

![](img/dad31beaaa0ddd316c2e20618ccaa3da.png)

我们将创建一个监视器，在活动数据库连接数超过阈值时发出警报。

![](img/ffb4271dbc9963afef011409c544f260.png)

选择**阈值警报**，然后像在指标页面上一样定义指标查询。我们希望使用来自单个数据库实例的`postgres.connections`指标。最后，我们通过阈值选择**最小值。如果“最小连接数”高于某个阈值，则表明该数据库实例上的连接过多。那就是我们应该警惕的时候了。**

接下来，定义触发警报的阈值:

![](img/869da9faa1560e095722fbe7eb7b1b25.png)

这是一个虚构的例子，但这意味着如果 Datadog 在过去的五分钟内检测到 12 个数据库连接，它将触发一个**警告**。同时，在过去的五分钟内，我们将收到一个关于 20 个数据库连接的完整的**警报**。

最后，我们将通知设置为在警报触发时发生。例如，Datadog 可以向吉拉、Slack 或自定义 webhook 端点发送通知。设置通知超出了本文的范围，但是用于通知的 Datadog [文档很清楚。](https://docs.datadoghq.com/monitors/notify/)

警报对于调试 web 应用程序是很重要的，因为它们提高了对正在发生的问题的认识，将您指向描述系统当前状态的相关指标。

到目前为止，我们所看到的度量标准大多是“系统之外的展望”类型。接下来，让我们看看如何使用日志来了解系统本身的内部*正在发生什么。*

# 使用日志流调试

在 Node.js 开发中，使用`console.log`是一种常见的调试技术。当您将 Node.js 应用程序部署到本地机器上时，查看日志输出很容易，但是如果您将它部署到云上，这就很有挑战性了。这就是[日志流](https://render.com/docs/log-streams)帮助的地方。

呈现日志流支持将 syslogs 从您部署的服务导出到外部日志聚合器。Datadog 就是一个例子，但是 Render 也支持其他的，包括 [Papertrail](https://papertrailapp.com) 和 [LogTail](https://logtail.com/) 。

## 设置系统日志导出

要设置 syslog 导出，请登录您的渲染帐户，并转到您的**帐户设置**页面。点击日志流。为您的日志聚合器添加端点，以及 API 令牌(如果有的话)。对于我们的例子，我们将把 syslogs 发送给 Datadog。

![](img/b5651d92bedd29f6e4118517fceb13c7.png)

在数据狗中，导航至**日志**选项卡。

![](img/91a9c146cf36576fe896f0efc43e5110.png)

这将把您带到**日志搜索**仪表板，在这里您可以看到您的 Datadog 帐户正在摄取的所有日志的实时尾部。这包括数据库实例 Node.js 微服务发出的日志，以及微服务的渲染构建过程。

为了缩小日志范围，我们可以将主机名添加到查询搜索栏中。结果将只显示来自用户微服务实例的那些日志(应用程序日志和渲染构建日志)。

![](img/5abee1e740142d3ae03a6054ae95578c.png)

应用程序通过`console.log`或 Pino 之类的记录器发出的日志会显示在这里，帮助调试工作。通过在各种服务日志中跟踪请求，您可以在系统中跟踪整个请求。当数据在系统中从 API 端点流向服务层，然后流向数据库层时，您可以使用日志来跟踪问题的根本原因。

## 利用日志进行调试

如何使用 Node.js 应用程序中的日志来帮助您进行调试？以下是一些有用的建议:

1.  **捕获异常时发出日志。**使用 [try-catch](https://javascript.info/try-catch) 是一个很好的实践，因为它确保了在抛出错误时的优雅处理。在`catch`块中，您可以做的不仅仅是处理异常；发出包含有用上下文信息的日志消息。然后，在您的日志管理系统中查询这些类型的消息，以追踪根本原因。
2.  **使用除** `**log**` **以外的其他严重性。**使用[控制台](https://nodejs.org/api/console.html#consoleerrordata-args)时，不仅限于使用`console.log`。您也可以使用`console.info`、`console.debug`和`console.error`等功能。这样做将有助于您区分所记录内容的严重性，从而更容易查询或过滤特定问题。
3.  **在特定日志条件下设置警报。**大多数日志管理系统都有一个警报机制，允许您对传入的日志设置某些条件，从而触发通知。例如，如果 Node.js 应用程序记录每一个 HTTP 响应代码(比如`200`、`401`和`500`，那么您可以设置一个警报，在某个响应代码出现得太频繁时通知您。您可以设置一个警报，当一个`500`或`401`代码在十分钟内出现超过五次时，它会提醒您，表明可能存在需要立即处理的问题。

# 结论

在我们的迷你项目中，我们使用简单的 Node.js 后端服务和 PostgreSQL 数据库构建了一个小型微服务应用程序。我们将这两部分都部署到 Render 中，这样我们就可以通过 Render 探索系统调试。我们研究了两种不同的调试方法:

1.  向 Datadog 发送可观察性指标，与警报和通知协同工作。
2.  使用日志流从不同的应用程序组件中导出 syslogs，我们也在 Datadog 中查看过。

当微服务应用出现问题时，您的团队需要快速响应，以减少或避免停机。有效的调试技术加上警报和通知可以让您快速响应。如果您正在部署应用程序进行渲染，那么您将拥有快速而简单的工具 Datadog 集成和日志流—来提供实时调试洞察和警报。