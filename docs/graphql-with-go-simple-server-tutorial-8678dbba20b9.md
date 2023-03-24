# GraphQL with Go —具有注册、登录和数据订阅功能的服务器

> 原文：<https://levelup.gitconnected.com/graphql-with-go-simple-server-tutorial-8678dbba20b9>

![](img/eefe011872c0af87defb0bfcf007680f.png)

https://github.com/graph-gophers/graphql-go[的标志](https://github.com/graph-gophers/graphql-go)

NodeJS 中有一个关于 GraphQL 入门的[优秀教程](https://www.howtographql.com/graphql-js/0-introduction/)，但是 Go 入门可能会比较棘手。有一些 GraphQL 实现，但是没有很多指导。因此，在本文中，我们将创建一个(几乎)与上面的 NodeJS 教程相同的服务器，但是使用 Go。

Go 中有许多 GraphQL 实现:出于可读性考虑，我将选择[这个](https://github.com/graph-gophers/graphql-go)。

本教程不会是盲目的复制粘贴跟随；它更像是一系列带注释的片段。在我们进行的过程中，请随意将您的代码分成文件和包。

**必备知识:**了解这些 Go 概念:结构、结构上的方法、指针和通道(用于订阅)。以及如何在你的 go 项目中安装和使用其他包。浏览一下 GraphQL 查询和模式语言也会有所帮助。

## 使用查询和突变读取和写入数据

从以下项目结构开始:

```
hackernews-go/
  main.go
  schema.graphql
```

这是模式；它显示了我们服务器上所有端点的签名。我们服务器唯一的工作就是执行！

现在我们从一个查询开始，它返回一个消息字符串。这个模式语言的语法可以在这里[看到](https://graphql.org/learn/schema/)。

所有解析器(实现模式中声明的操作的 Go 函数)都是这个`RootResolver`结构的方法。

`Info()`函数对应于模式中的`info`查询。名称必须与模式中的内容相匹配，不区分大小写。

它返回一个字符串和一个错误。`graph-gophers`库将获取解析器返回的任何内容，将其打包成 HTTP 响应，并发送给客户端。

这里`graph-gophers`将尝试用你的根解析器和模式构建一个服务器。

现在使用`go run main.go`运行您的服务器，通过在终端中发出 POST 请求来测试您的服务器:

你应该看到`{"data" : {"info" : "this is a thing"}}`还给了你！`graph-gophers`解析我们请求中的查询，并根据[规范](https://graphql.org/learn/serving-over-http/)格式化响应。尝试从我们的 Go resolver 返回一个错误，用`errors.New("some error")`替换`nil`，看看新的响应格式。

关于测试服务器的一个注意事项:发送查询并从服务器获得响应的一个快速方法是使用 Prisma 的 [GraphQL playground](https://github.com/prisma-labs/graphql-playground) 。在操场上，连接到 URL 端点`[http://localhost:8080/graphql](http://localhost:8080/graphql)`，输入如下查询

```
query {
    info
}
```

为了熟悉 GraphQL 查询语言，请看这里的。

或者，您可以像浏览器一样使用 JavaScript 通过 http 发送您自己的查询:有关示例，请查看本教程末尾的附录。

现在让我们添加另一个查询操作，并让它返回一个自定义类型:

扩展模式后，我们现在有了一个链接类型。用户将能够创建和获取这些。

我们的链接有字段；我们需要告诉`graph-gophers`如何解决这些问题。我们添加这个`opts`对象，所以它只返回一个相应的 struct 字段。

我们还将定义一个全局数组来存储链接对象。

Go 类型映射到模式语言类型。这意味着我们可以从解析器中返回它们。

关于从解析器返回对象和指针的注意事项:如果模式中的字段可为空，则对应的 Go 数据类型必须是指针。因为在模式中，`feed`的返回类型是`[Link!]!`(即列表本身及其内容都不能为空)，所以我们能够返回对象而不是指针。

使用上面定义的选项在我们的`parseSchema`函数中返回结构字段。

现在，类似这样的查询将起作用:

```
query {
   info
   feed {
      id
      description
   }
}
```

添加突变:

扩展架构…

突变解析器

…实施它。输入参数从`args`结构中解包。

现在我们可以像这样添加链接:

```
mutation {
  post(description: "hello", url: "[www.abc.com](http://www.abc.com)") {
    id   
  }
}
```

## 用 jwt 注册和登录

使用注册函数、用户对象和任意 AuthPayload 对象扩展模式，以封装身份验证细节。

用户将在成功注册或登录时收到一个令牌，该令牌可以附加到需要授权的后续请求中。

根据最佳实践，我们将存储密码的散列，而不是直接存储。我们正在使用`golang.org/x/crypto/bcrypt` 库。

在我们的解析器中，我们可以随时停止并向客户端返回一个错误。正如我们已经看到的，`graph-gophers`将格式化错误响应。

你现在可以注册用户！

要生成令牌，我们可以使用如下函数:

使用[这个库](https://github.com/dgrijalva/jwt-go)，我们可以创建一个这样的 JWT。

它将对用户 ID 进行编码。如果令牌与请求一起传递，解析器将能够解码 ID，从而确定请求发送方。

要发送带有请求的令牌，请添加以下标头，用您自己的来自服务器的令牌替换“Bearer”之后的所有内容:

```
"Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJJRCI6IjAtdXNlciJ9.6149FwAsxB7onXVyNzuT0U34PeT0bZyeIZkaJCSzfaQ"
```

为了实现登录功能，我们再次扩展了模式:

登录功能需要检查用户的密码是否与他们在注册时设置的相匹配。

检查所有用户，并将密码与第一个电子邮件匹配的用户进行比较。

您现在可以注册并登录。

我们现在将使用新生成的 JWT 在创建链接时自动添加作者。

首先，我们在模式中添加 author 字段。

我们需要将来自请求的 JWT 传递给我们的解析器。为此，我们需要重构我们的主函数:

解析器通过上下文对象访问任何请求信息，这是一个键值存储，我们需要填充它。Relay 的 ServeHTTP 实现将把这个上下文传递给我们的解析器。

我们需要在创建链接时填写链接的`postedBy`字段；也就是在`post`突变。为了访问`Post`中的令牌，我们需要稍微修改一下实现:

`graph-gophers`向所有解析器传递一个`ctx`参数，我们可以用它来访问我们的令牌。只要您的请求在其“Authorization”头中有一个令牌，服务器现在就应该将它打印到控制台。

我们将从一个单独的函数中的令牌获取用户:

使用`jwt`库解码我们的令牌并提取用户 ID，然后返回一个 ID 匹配的用户。

完整的`Post`实施:

获取作者并将其分配给一个新链接。

现在，如果您`post`一个链接，然后请求一个`feed`，您将能够检查该链接的`postedBy`用户对象。

## 通过 Go 频道订阅数据

GraphQL 支持订阅，允许客户端订阅服务器上的事件。在幕后，订阅使用 WebSocket 连接，而不是我们一直用于查询和变异的 HTTP 请求和响应。我们将对发布新链接的事件进行订阅。

和往常一样，第一步是在模式中声明新的订阅操作:

我们正在定义一个新的操作，它将在服务器上每次创建一个链接时返回一个 Link 对象。

下一步是为我们的服务器添加 WebSocket 支持。这需要对 main 进行重构:

`graph-gophers`提供了一个可以处理 web socket 连接以及 http 的包。

我们用这个新的`wshandler`代替了`handler`。

订阅操作可以按如下方式实现:

我们给我们的`RootResolver`对象添加一个通道。当数据从我们的解析器推送到 it 部门时，`graph-gophers`会将数据转发给客户端。

订阅解析程序应该返回充当客户端数据源的通道。

如果您使用以下内容进行测试:

```
subscription {
  newLink {
    id
  }
}
```

你应该会看到操场上显示的`Listening…`:准备接收数据。当然还不会出现任何数据，因为我们没有向频道推送任何内容！让我们来解决这个问题:

首先，稍微重构 main，这样可以用`make`初始化通道。

.

然后，在 Post 中，一旦你创建了一个链接，就把它推送到`NewLinks`频道。

select 语句的存在使得通道插入操作不会阻塞`Post`的执行。

就是这样！为了在 playground 中进行测试，在一个新的选项卡中添加了一个链接，您应该会看到一些数据出现在您执行订阅的选项卡中。

**重要警告:**这种订阅实现只对一个订阅者有效。这是因为我们的订阅操作返回一个通道；一旦从其中读取了一个值，该值将被传递给第一个订阅者，而不是其他订阅者。为了使多个订阅有效，需要为每个订户创建一个专用通道。创建新链接时，应该将其推送到所有订户通道。这种机制的示例实现可以在[这里](https://github.com/matiasanaya/go-graphql-subscription-example)看到。

## 结论

在本教程中，我们创建了一个服务器，客户端可以在服务器上注册、登录、创建对象、读取对象和订阅事件。一个好的下一步是实现将数据存储在数据库中，而不是保存在内存中。

## 附录—用 JavaScript 测试您的服务器

在浏览器中运行这个(替换您在`fetch`中的端点)来测试您的查询: