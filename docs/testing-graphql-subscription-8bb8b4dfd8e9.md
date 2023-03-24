# 测试 GraphQL 订阅

> 原文：<https://levelup.gitconnected.com/testing-graphql-subscription-8bb8b4dfd8e9>

![](img/87b6d1015ccc56a9d32873ba6c28068e.png)

本帖是我上一篇关于测试 Gql [的帖子的延续 https://level up . git connected . com/how-I-save-a-number-hours-week-on-Testing-graph QL-in-nest-js-typescript-1 AFD 8 CEE ACF 7](/how-i-save-a-few-hours-each-week-on-testing-graphql-in-nest-js-typescript-1afd8ceeacf7)。

在链接的文章中，我首先创建了一个用 Nestjs 编写的小型 graphql 服务器。今天，我将对我之前的例子进行扩展，所以如果您之前没有阅读过，那么在深入研究这个例子之前先看一看。

## 问题是

在我的上一篇文章中，我们发现了如何自动化与样板代码相关的工作，这是编写 GraphQL 查询和变异所必需的。

但是！GraphQL 还提供了一种使用订阅与客户端实时通信的方式。以前的代码不支持这种通信方式。

Websockets，因为这是用于订阅的传输协议，所以与 HTTP 调用完全不同。等待响应要困难得多，因为通常的响应只是某个其他操作之后的事件，有时是由不同的用户发起的。

我们需要采取其他方式，而不仅仅是使用承诺。

## 这个例子

首先，我们看一下当我们在解析器中声明订阅时，前面的代码会生成什么。在我们推出自动化订阅的解决方案之前，这一点非常重要。

我将从安装所需的包开始，以允许我们的服务器使用订阅。

```
yarn add graphql-subscriptions graphql-ws ws
```

第二步是通过编辑 GraphQLModule 声明来配置服务器。

要使用订阅，我们需要声明 PubSub。传输层，在这种情况下，我们将使用由**提供的内存中的 graphql-subscriptions** 包和订阅标识符。多亏了它们，我们可以确定哪个消息与哪个订阅相关。

现在，在创建了设置订阅所需的所有内容之后，我可以声明一个:

为了检查订阅是否有效，我需要发布一条消息，就像订阅名称所说的那样，在创建一个用户之后。为此，我将对以前的实现稍作修改:

现在是检验它是否有效的时候了。我可以用操场做，但是我会跳过这一步。我想通过运行测试用例来测试我们的订阅是否自动工作！

## 上一篇文章之后的可用资源

正如我们之前决定的那样，首先我们将看看`gqlq`会产生什么。为此，首先我需要运行前一篇文章中的脚本:`test:generate-quries`。

结果，我收到了两件重要的东西。第一次打字在`test/gql/queries.ts`中可用。在函数`getSdk`中，我订阅了`userCreated`，参数看起来有希望，但返回类型是承诺！所以不会有太大帮助。但是拥有一个可以使用的类型化参数是一个很好的标志！

第二个文件在`test/qgl/subscriptions/userCreated.gql`中。这里我有我们的查询定义，如果我在任何 GraphQL 客户端传递它，我们只需单击 run 并等待来自服务器的数据。

有了这两样东西，我就可以用泛型来变魔术，把响应转换成回调。为什么回调？我们可以很容易地传递一个 spy，检查调用了哪些参数，并对正确的结果做出预期。

## 解决方案

在花了一些时间尝试确定 SDK 中的哪个方法是订阅后，我发现了一个有点不同的解决方案。使用预先测试脚本，我将`test/gql/subscriptions`中可用的`index.js`文件转换为 typescript，其中生成的代码导出所有可用的订阅查询，稍后使用`keyof typeof`我得到了一个不错的订阅方法列表。

/scripts/generate-subscriptions-query-type . js

并在 package.json 中注册它:

```
"test:generate-subscriptions-as-ts": "node scripts/generate-subscriptions-queries-type.js test/gql/subscriptions",
"test:generate-queries": "gqlg --schemaFilePath ./src/schema.gql --destDirPath ./test/gql && yarn test:generate-subscriptions-as-ts && graphql-codegen --config codegen.yml"
```

现在，当我们运行`test:generate-queries`时，我们也得到了一个带有 GraphQL 查询的 typescript 文件。

下一步是将现有生成的 SDK 类型解析到我们自己的订阅接口。这是由下面的代码完成的。我将跳过细节，如果你对它如何工作感兴趣，你可以查看一些关于泛型类型的好文章或者在评论中询问。

现在，当你进入我们的测试文件时，你会看到一个有点不同的订阅界面，可能在 SessionFactory 中有一个错误，因为我们还没有完成这个界面；)

所以在下一步，我们将实现它！

简而言之，我使用`ws`实现创建了一个 GraphQL 客户端，以便有机会传递消息头。一些无聊的配置和接下来等待的事件`connected`。当事件发生时，代码准备向服务器发送订阅请求。

接下来，我只是基于查询和先前创建的客户端实现了一个先前声明的接口。

你可以深入细节，有问题可以在评论里问，但也就这些了！我们准备测试了！

## 试验

现在我可以添加一些测试用例。此外，我声明了`sleep`函数，以确保在测试用例结束之前接收到来自服务器的响应。

## 摘要

如您所见，您可以使用现有的解决方案创建许多有用的东西！相信对你的日常工作会有一点帮助，下期文章再见！

资源库:[https://github . com/adziok/nestjs-graph QL-testing/tree/gql-subscription](https://github.com/adziok/nestjs-graphql-testing/tree/gql-subscription)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)