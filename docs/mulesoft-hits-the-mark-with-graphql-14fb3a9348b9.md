# MuleSoft 用 GraphQL 击中目标

> 原文：<https://levelup.gitconnected.com/mulesoft-hits-the-mark-with-graphql-14fb3a9348b9>

![](img/934e4c99c45d8ac36b894b1e320dae57.png)

Mule Connect 2020 虚拟会议已经结束，我们看到了 MuleSoft 对 GraphQL 愿景的更多细节，这表明 MuleSoft 正在积极努力填补 GraphQL 的空白。让我们回顾一下什么是 GraphQL，它面临的挑战，以及 MuleSoft 如何应对这些挑战。

# GraphQL 基础知识

GraphQL 是用于跨其他 API 查询数据的 API 规范。它起源于 2012 年的脸书，2015 年作为开源发布。它允许 API 客户端告诉 API 它想要什么，而不是 API 假设它知道客户端想要什么。这对支持 ui 的 API 特别有用。UIs 的一个挑战是他们可能需要来自许多来源的数据。调用所有这些资源会使 UI 变得复杂。它还会给对带宽敏感的移动应用程序增加过多的干扰。我们通常通过用一个复合视图-模型 API 聚合较低级别的业务 API，或者通过使用[后端对前端(BFF)模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends)来解决这个问题。

不过，这有一些问题。这些 API 与它们支持的页面紧密结合。当您同时拥有 web 和移动 UI 时，您通常需要编写两种风格的视图模型或 BFF APIs。当你想在 UI 上重新安排事情时，比如 A/B 测试一个新的体验，你再次需要重写这些 API。

对于 GraphQL，UI 层使用 GraphQL 规范告诉 GraphQL 层它需要什么，然后 API 返回它。现在，您可以用一个 GraphQL API 来支持您的移动和 web UIs，而不是两个视图模型或 BFF。想要 AB 测试移动一些字段到不同的页面？只需让新页面在需要时请求新字段；不需要去改变 API 层。

## GraphQL 与 OData

等等，这不就是小田吗？不，OData 大致上就像是 web 上的 ODBC/JDBC。查询被下推到数据库中，数据库处理该查询。这造成了一些限制。

*   OData API 所有者对用户发送什么样的查询控制较少。这为注入攻击打开了方便之门，注入攻击利用错误配置的数据库或者用失控的查询使数据库过载。
*   您仅限于可以直接连接的数据库。如果您需要查询只能通过服务接口(SOAP、REST 等)访问的数据。)，那你就倒霉了。
*   跨多个数据库连接数据很困难。随着 IT 部门转向微服务架构并打破他们的数据铁板一块，这尤其成问题。拥有一个贷款数据库作为记录系统的情况越来越少。

GraphQL 运行在 API 层。因此，API 可以访问的任何数据源都可以被 GraphQL 查询。此外，它可以跨 API 连接数据，例如从客户帐户 API 中提取客户，并通过单独的订单和计费 API 将其与客户的订单和计费历史连接起来。

## GraphQL 挑战

虽然 UI 开发人员可能喜欢 GraphQL，但后端开发人员对它的喜爱要少得多。这是因为 GraphQL 可能很难维护。您需要定义 GraphQL API 支持的许多数据源之间的关系。此外，您需要*记录*它们，以便最终用户可以知道每个 API 实体如何与其他实体相关联。最后，GraphQL 的学习曲线比 REST 陡峭得多。这些挑战可能会成为采用的障碍。

# MuleSoft 的 GraphQL 图形用户界面

在 Mule Connect 的主题演讲中，他们展示了 MuleSoft 即将推出的 GraphQL UI 的演示，他们称之为 API Federation。它似乎解决了上述挑战。

首先，GraphQL 维护者可以导入由 Anypoint 平台管理的 API。Anypoint 已经拥有了存储在 Anypoint Exchange API 目录中的每个 API 的 OpenAPI 规范，并且这些信息被自动带入到图形编辑器中。然后通过拖放 UI，您可以定义各种 API 之间的实体关系。然后，Anypoint 将生成您需要的模式，以便在 Anypoint 平台上自动构建 GraphQL API，并将该模式发布到 Anypoint Exchange API 目录。

其次，在消费端，UI 开发人员可以通过搜索任意点交换来发现模式。从那里，他可以看到模式的可视化，并使用它通过单击他需要的实体和字段来构建查询。甚至还有一个预览窗格，可以进行实时查询，并在您编辑查询时向您显示结果。完成后，Anypoint 会以各种语言生成您需要的查询，因此您可以将其复制/粘贴到您的代码中。

我的印象是，它将使后端开发人员和 UI 开发人员更容易创建、维护和使用 GraphQL。尤其是如果您已经是 MuleSoft 的客户，并且不愿意在您的 API 空间中添加更多的供应商。

不幸的是，尽管这是一个 100%免费的虚拟会议，MuleSoft 并没有制作这个公开的视频。因此，为什么我不能给你提供截图。因此，如果您对 GraphQL 感兴趣，请关注即将发布的版本。

同时，做一些关于 GraphQL 的更深入的阅读。即使您对 MuleSoft 不感兴趣，也有越来越多的开源和商业产品供您探索。对于试图加快用户体验交付速度的 IT 团队来说，GraphQL 将是您工具箱中的一个重要工具。