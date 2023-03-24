# 如何有效地使用 GraphQL 指令

> 原文：<https://levelup.gitconnected.com/how-to-use-graphql-directives-efficiently-fc30c63b5bbe>

![](img/d5b964b2f2742942324aff2e90c7382b.png)

图片由 [Tigran Hambardzumyan](https://unsplash.com/@tigranh47) 通过 [Unsplash](https://unsplash.com/photos/IMHjExaajFY) 拍摄

# 介绍

GraphQL 是过去几年软件世界中最神奇的工具之一。这是出于许多原因:它的强类型模式，避免了过度蚀刻或欠取，是服务器端和客户端的便利工具，组成多 API ( [缝合](https://www.graphql-tools.com/docs/schema-stitching/stitch-combining-schemas))，以及伟大的社区。

指令是 GraphQL 最强大的特性之一，它使您能够增强和扩展 API。

# 什么是指令？

该指令是一个修饰 GraphQL 模式的一部分以扩展其功能的函数。例如下面例子中的`@UpperCase()`:

简单地说，这个`@UpperCase`指令，顾名思义，将把用户名大写，然后返回它。

# 指令类型

有两种类型的指令:

*   **模式指令。**
*   **查询指令。**

让我们从内置指令中了解一下它们之间的区别。

到目前为止，有四个被认可的内置指令:

*   `@deprecated(reason: String)`将模式的一部分标记为不推荐使用，原因可选(模式指令)。

*   `@specifiedBy(url: String!)`它提供了一个标量规范 URL，用于指定定制标量类型的行为(模式指令)。

`@skip(if: Boolean!)`如果为真，GraphQL 服务器将忽略该字段，并且不会解析它(查询指令)。

`@include(if: Boolean!)`如果为假，GraphQL 服务器将忽略该字段，并且不会解析它(查询指令)。

从前面的例子中，您可能会注意到:

*   **模式指令**在模式本身中定义，在构建模式时运行，由模式设计者使用。
*   **查询指令**在查询中使用，并在解析时运行，由最终用户使用。

# 查询指令与字段参数

从前面的例子中，你可能会问为什么我们应该使用指令，而我们可以根据字段参数在解析器中执行大写逻辑？这个问题会引导我们理清彼此的利弊。

不幸的是，不同的 GraphQL 服务器、客户机和工具处理 GraphQL 指令的方式不同，对它们的支持程度也不同，这就造成了它们之间的冲突。

例如，当从缓存中查询同一个字段时，[中继](https://relay.dev/)不会使用查询指令。

看看下面的例子。此查询首次运行，然后被缓存:

缓存后，使用`@UpperCase`指令第二次运行相同的查询:

第二个查询应该返回“HELLO WORLD ”,但是，Relay 返回与第一个查询相同的响应，该响应存在于缓存中，即使我们使用了完全被忽略的`@UpperCase`指令。

从前面的例子中，您注意到由于 GraphQL 提供者的不同处理方式，依赖查询指令是不一致的，因此， [GraphQL 工具](https://www.graphql-tools.com/) [不鼓励使用查询指令](https://www.graphql-tools.com/docs/schema-directives#what-about-query-directives):

> 但是，一般来说，模式作者应该考虑尽可能使用字段参数，而不是查询指令。

另一方面，使用指令有一些优点:

*   通过提高代码的可重用性、可读性和模块化，你的代码将会更加整洁，这符合 [DRY 原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。
*   尊重[单一责任原则](https://en.wikipedia.org/wiki/Single-responsibility_principle)。
*   如果您希望您的客户在不接触您的代码的情况下用新的功能扩展您的 GraphQL API，您可以依靠指令来完成这个任务。

所以总结一下这一点，为了安全起见，正如 GraphQL Tools 所建议的，您应该使用字段参数而不是查询指令。所以前面的例子应该是:

或者，只有当您知道自己在做什么时，才可以使用查询指令！

# 指令用例

基本上，使用自定义指令有很多可能性。让我们创建一些实例，并将其应用于同一个查询`post`。

> 您可以找到完整的代码👉[此处](https://github.com/Mohamed-Mayallo/graphql-directives)。

首先，我们将定义这个模式:

**1。**让我们实现大写指令，我们称之为`@upper`:

首先，我们需要定义[指令位置](https://spec.graphql.org/October2021/#sec-Type-System.Directives)。

其次，我们需要定义 directive transformer 函数，该函数负责将 directive 逻辑应用于具有该指令的每个字段。

第三，我们需要通过应用指令逻辑来转换模式。

第四，我们可以将其应用于`title`字段，如下所示:

就这样，现在您可以在任何字符串字段使用`@upper`指令。

**2。让我们实现一个从第三方 API 加载这篇文章的指令，我们称之为`@rest(url: String!)`**

让我们继续同样的步骤:

现在我们可以将它应用于`post`查询，如下所示:

**3。**让我们实现另一个指令，可选地格式化帖子`createdAt`字段的日期，并将其命名为`@date(format: String = "mm/dd/yyyy")`:

现在，我们可以将该指令应用于`createdAt`字段，如下所示:

**4。**让我们实现一个授权访问这篇文章的指令，姑且称之为`@auth(role: String!)`:

就这样，现在指令可以应用了:

尝试检查`OPERATOR`以验证指示效果。

**5。**如果我们想为文章自动生成一个 UUID，我们可以创建`@uuid`指令:

您可能注意到我们使用了`OBJECT`而不是`FIELD_DEFINITION`，因为这个指令将应用于 GraphQL 类型，如`Post`类型。

那么我们可以将其应用于`Post`类型，如下所示:

6。此外，我们可以实现一个指令来验证一个字符串长度，就像帖子的`body`字段一样。姑且称之为`@length(min: Int, max: Int)`:

现在，应用它

尝试设置`min: 1000`并检查有效性。

# 结论

简单地说，指令是一个非常好的工具，可以用来增强您的 GraphQL API。在本文中，我们实现了一些指令用例，只是为了向您展示指令的威力，因此，您可以实现适合自己情况的用例。

# 资源

*   [图表质量规格](https://spec.graphql.org/October2021/#sec-Type-System.Directives)
*   [模式指令](https://www.graphql-tools.com/docs/schema-directives)
*   [指令](https://www.apollographql.com/docs/apollo-server/schema/directives/)
*   [GraphQL 指令被低估](https://blog.logrocket.com/graphql-directives-are-underrated/)
*   [graph QL 中的字段参数与指令](https://blog.logrocket.com/field-arguments-vs-directives-graphql/)

*最初发表于*[*https://blog.mayallo.com*](https://blog.mayallo.com/how-to-use-graphql-directives-efficiently)*。*

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)