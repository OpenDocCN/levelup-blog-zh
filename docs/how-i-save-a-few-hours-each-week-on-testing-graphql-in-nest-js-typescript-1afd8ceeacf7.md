# 我如何在 Nest.js (typescript)中每周节省几个小时来测试 Graphql

> 原文：<https://levelup.gitconnected.com/how-i-save-a-few-hours-each-week-on-testing-graphql-in-nest-js-typescript-1afd8ceeacf7>

![](img/10c0d34e46c9f0be14c4d5f0f28097c9.png)

智能测试节省时间。[https://pixabay.com/images/id-1274699/](https://pixabay.com/images/id-1274699/)

在这篇文章中，我将向您展示如何自动化 graphql 测试过程。我们将从手工编写的查询转移到由类型支持的完全自动生成的查询/变化

## 问题是

在一些项目中，我们在 Nest.js 中使用 GraphQL 获得了很多乐趣。但有一件事一直困扰着我们……当我们想要使用我们的 GQL 模式作为契约来测试端到端的任何功能时，我们需要做大量的手工工作。它看起来像这样:

1.  编写查询/变异
2.  为测试目的创建一个新的普通字符串查询
3.  创建一个将使用此查询的函数
4.  通过 supertest 使用此功能

也许看起来没那么糟，但是相信我。当代码库增长时，这 4 步过程是最无聊的事情之一…

但这不是终点！当您更改代码库中影响现有模式的某些内容时，您还需要记住在查询定义和测试中更改它。

此外，我们从应用程序源导入输入类型，以便至少有一些 typescript 支持，创建与每个测试的额外耦合。

## 故事

每次当这个问题出现时，我都在想我们如何自动化这个过程。我在互联网上搜索，考虑一些带有类型提示的查询生成器，等等…但是我找不到任何可以解决这个问题的方法。

有一天，当我加入另一个尚未创建 e2e 测试设置的项目时，我决定再试一次。我查看了主要在前端使用的可用工具，并将它们组合在一起以实现我的目标。

## 解决方案

我找到了一个满足我所有要求的 GraphQL 客户端:`graphql-request`由`@graphql-codegen`支持。所以我开始尝试生成查询，最终我找到了解决方案，它以一种简单明了的方式给了我所有可用的查询。

我将展示一个完整的例子，用 GraphQL 创建一个基于 Nest.js 的示例应用程序。如果您熟悉这个堆栈，可以跳过这一步，继续本文的下一部分。

# **示例应用**

我们将从使用`@nestjs/cli`创建一个空巢. js 项目开始。如果你还没有这个包，你可以在这里找到如何得到它[https://docs.nestjs.com/first-steps](https://docs.nestjs.com/first-steps)。您也可以将本教程与其他工具一起使用。您需要的只是 Typescript 和生成的 GraphQL 模式。

```
nest new graphql-testing-example
```

生成应用程序后，我们可以稍微清理一下 src 文件夹。删除`app.controller.ts app.controller.spec.ts`和`app.module.ts`中相应的注册。

现在我们需要安装所需的依赖项:

```
yarn add [@nestjs/graphql](http://twitter.com/nestjs/graphql) [@nestjs/apollo](http://twitter.com/nestjs/apollo) graphql apollo-server-express
```

现在是时候在我们的应用程序中注册 GraphQLModule 了:

在这段代码中，声明`autoSchemaFile`很重要。稍后我们将使用它来基于模式生成类型。

现在是时候创建一些代码了，出于示例的目的，我们将创建 2 个文件并调整`app.service.ts`。

`gql-dtos.ts` —为了简化示例，我们将在一个文件中创建所有的类。

`app.service.ts` —我们将在这里创建我们的“业务逻辑”。这将非常简单——我们将允许我们的用户对用户进行基本的 CRUD 操作。

`app.resolver.ts` —我们的 GraphQL 解析器，基于这个文件 Nest.js 将生成模式，稍后将处理我们的请求。

当我们运行:`yarn start:dev`我们将有一个工作的应用程序！现在我们可以进入这篇文章的核心部分。GraphQL 测试！

# GraphQL 测试工具

## 先决条件

我们需要从安装所需的包开始。

```
yarn add -D gql-generator [@graphql](http://twitter.com/graphql)-codegen/cli [@graphql](http://twitter.com/graphql)-codegen/introspection [@graphql](http://twitter.com/graphql)-codegen/typescript [@graphql](http://twitter.com/graphql)-codegen/typescript-graphql-request [@graphql](http://twitter.com/graphql)-codegen/typescript-operations [@types/node-fetch](http://twitter.com/types/node-fetch)
```

也许我会解释为什么有这么多的包。

`gql-generator`用于根据`schema.gql`生成原子对象和输入。

`@graphql-codegen/*`包用于将之前生成的文件转换成`graphql-request`的类型和 Sdk。

`@types/node-fetch`我们将使用它来基于`supertest`键入我们自己的`fetch`实现。

现在，当我们拥有所有需要的包时，我们可以将它们组合在一起以实现我们的目标:节省时间！

## 履行

我们将从创建 SessionFactory 开始。我们的工厂将为我们生成新的会话，在我们的示例中，它不会有太大用处。但是当你在测试期间需要基于两个不同的用户与应用程序交互时，这将允许我们这样做。

现在我们可以深入到这段代码中，它只创建了一个工厂方法，该方法将返回我们的 Sdk 实例。每个 Sdk 实例都是独立的，因此，举例来说，每个实例可以有不同的 cookies 或标题。同样，在这个方法中，您可以实现某种用户流。例如，如果您的应用程序有用户和管理员，您可以创建 2 个工厂方法。每个方法都将调用您的 API 来接收基于类型的授权令牌，并返回准备好授权请求的 Sdk。

`supertestFetch`是一个 supertest 代理实例，通过抽象来实现`fetch`接口。这是因为`graphql-request`在引擎盖下使用 fetch。

当我们在示例中使用 Nest.js 时，我们将创建 util 来抽象公共代码。

我们将在这个位置创建一个 SessionFactory，并返回现有的实例。它将在每次测试中节省几行代码；)

## 使用

现在是表演时间了！

很难展示它有多强大。正如您在上面的例子中看到的，`Sdk`包含了您的后端提供的所有可用的查询和变异。我还建议下载存储库并尝试更改输入、突变，观察当您重新生成 Sdk 时，什么类型的脚本会告诉您哪里出错了。

# 摘要

在本文中，我向您展示了如何测试 GraphQL。希望你喜欢这个教程，它会改变你的生活，就像我一样！另外:

*   也许你知道如何在 node.js 中测试 GraphQL 的其他方法？
*   有什么改进的办法吗？

把它们留在评论区吧！

链接到存储库:[https://github.com/adziok/nestjs-graphql-testing](https://github.com/adziok/nestjs-graphql-testing)