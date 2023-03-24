# 用 GraphQL 片段擦干你的代码

> 原文：<https://levelup.gitconnected.com/dry-up-your-code-with-graphql-fragments-4d080e4f545e>

![](img/4a672839c72a94ad42826822fce039b4.png)

[Katy Cao](https://unsplash.com/@katycao?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我一直在学习在我的 React 应用程序中使用 GraphQL 和 Apollo。这篇博客文章假设您对这两种技术都有基本的了解，并将专门讨论在使用 Apollo 定义的查询中使用 GraphQL 片段。如果您想了解更多关于在 React 中使用 Apollo 和 GraphQL 的信息，我可以在文章末尾找到相关文档的链接。

# 我的问题

对于我的应用程序，我想让用户以两种方式找到食谱。1)提供一个搜索词，并显示与该词相关的 50 个结果的限制，如果需要，还允许通过结果分页。2)允许用户通过唯一的 ID 搜索特定的配方，并且只返回单个结果。我不再需要考虑限制、分页等等。

在这两种情况下，我都希望每个食谱得到相同类型的结果。从两个查询返回的食谱对象应该具有相同的属性，比如 id、标题、图像、署名等。

查看下面的代码，看看我用来完成这个任务的两个查询:

**用例 1:**

**用例 2:**

我相信你已经注意到了，这里有几行重复的内容。配方上的所有属性在两次查询之间重复。如果有一种方法可以清理这种情况并减少我需要编写的代码量就好了……..

# 我的解决方案

为了一次编写查询属性，我们可以使用一个可以在多个查询中重用的 Apollo/GraphQL 片段。

最简单的定义是:

> "一个 [GraphQL 片段](http://graphql.org/learn/queries/#fragments)是一个共享的查询逻辑."— Apollo 文档(参考基本 GraphQL 概念。)

同样根据阿波罗文档，在阿波罗中片段有两个主要用途:

*   在多个查询、变异或订阅之间共享字段。
*   将您的查询进行分解，以允许您将字段访问与使用它们的位置放在一起。

在我的情况下，我更关心第一点，这就是我在这里要解决的问题，以清理这两个食谱查询。

首先，让我们定义我们的片段。

您可以在第 1 行看到，我正在从`apollo-boost`导入`gql`标记，这样我可以更容易地在 React 中编写查询。然后我定义了一个常数，在这个常数中我定义了这个片段。名称`RECIPE_ATTRIBUTES`指的是这一段`gql`代码，这样我们可以在以后的其他查询中使用它。这不是片段本身，只是一些`gql`代码的容器。

我们标签中的代码片段使用了关键字`fragment`来定义我们想要重用的实际代码段。在这种情况下，我将我的片段命名为`recipeAttributes`。我把`on Recipe`放在这个声明之后，因为这是这些属性所属的类型。

现在我已经定义了我的片段，我需要将它添加到我的查询中，并替换重复的代码:

刚刚变得如此之短！我引入了我之前定义的`RECIPE_ATTRIBUTES`常量，并将其插入到查询之后，但仍然在`gql`标记内。然后，我能够在现有的查询中引用我定义为`recipeAttributes`的片段。

这留给我最后的代码:

概括地说，我们刚刚在 Apollo `gql`标签中写了一个 GraphQL 片段，以便封装和重用我们希望食谱如何返回给我们的定义。这有助于我们执行单一责任原则(SRP)。快速回顾一下 SRP:

*“单一责任原则(Single Responsibility Principle，SRP)是五个所谓的坚实原则之一，由 Robert C. Martin 开发并推广，旨在帮助开发人员生成灵活且可维护的代码。简而言之，SRP 认为一个给定的模块或类应该负责程序功能的一个元素，因此只有一个改变的理由。—* [*塞弗林·佩雷斯*](https://medium.com/@severinperez/writing-flexible-code-with-the-single-responsibility-principle-b71c4f3f883f)

这也有助于我们通过 GraphQL 将食谱的单一来源返回到我们的前端。我们已经在一个地方定义了预期的配方属性。

我们不仅有一个单独的变量来保存我们的查询和配方定义，并在一个地方定义了配方结果的结构，而且我们还减少了代码的数量和复杂性！赢赢！

希望这对你和对我一样有帮助！

[](https://graphql.org/learn/) [## GraphQL:一种 API 查询语言。

### GraphQL 在您的 API 中提供了完整的数据描述，使客户能够准确地要求他们…

graphql.org](https://graphql.org/learn/) 

[https://www.apollographql.com/docs/react/](https://www.apollographql.com/docs/react/)

https://www.apollographql.com/docs/react/advanced/fragments

[](https://gitconnected.com/learn/graphql) [## 学习 GraphQL -最佳 GraphQL 教程(2019) | gitconnected

### 11 大 GraphQL 教程-免费学习 GraphQL。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/graphql)