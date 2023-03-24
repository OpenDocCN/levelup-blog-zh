# 设计开发人员友好的 REST APIs 的最佳实践

> 原文：<https://levelup.gitconnected.com/best-practices-for-building-developer-friendly-rest-apis-e6a419fcbd38>

## 软件体系结构

## 如何设计资源、请求和响应，使用版本控制策略，应用安全实践，以及提供优秀的开发人员体验。

![](img/e72180192ea876cef59fb30657856354.png)

阿尔瓦罗·雷耶斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

REST APIs 可能是通过互联网集成两个应用程序的最简单的方法。大多数 web 开发人员已经集成或构建了它们。

我写这篇指南是为了帮助开发人员设计和构建更容易集成和维护的 REST APIs。它包括我从同事那里借鉴的技巧，我从书籍和文章中学到的想法，以及我自己使用 REST APIs 的一些经验。因此，您可能会发现这些最佳实践有点固执己见。

本指南从 REST 资源开始，然后研究请求和响应，然后是版本控制、安全性，最后是开发人员体验。这里的所有实践都是与平台和语言无关的。

# REST API 资源

“资源”的概念是 REST 的基石。将它们看作实体的集合或单个实体是很方便的。

## 在资源名称中使用名词

命名 REST API 资源时，使用名词，避免使用动词。

例如:

*   `/post`对于返回博客帖子集合的资源，
*   `/me`为返回当前用户信息的资源。

有时，REST API 需要提供产生一些输出的功能，而不实际公开任何实体，例如翻译或货币转换。您仍然可以在这样的资源名称中使用名词。例如，`/translation`表示从一种语言到另一种语言的翻译。或者`/amount`用于在货币之间转换金额的资源。

我相信总是可以用名词替换资源名称中的动词。稍后我会分享一些实现这一目标的方法。

## 避免深度嵌套的资源

有时资源必须嵌套。例如，代表博客帖子评论的嵌套资源可以是`/posts/r83fj3/comments`。使用嵌套资源时，最好避免深度嵌套。在大多数情况下，示例中的两个级别就足够了。

## 使用非顺序资源 id

最好对资源使用非顺序的(可能是随机的)id。这将保护您的数据免受枚举攻击，如果您发现您的两个资源必须合并为一个，同时保留实体的 id，这也将使合并数据集这样的事情变得更简单。UUIDs 或随机字符串就足够了。比如`/posts/r83fj3`。

# 要求

## 用适当的 HTTP 动词表示 CRUD 动作

典型的 REST API 资源支持创建、读取、更新和删除操作。让我们来看看使用`/posts`资源管理博客文章的规范方法。

开发人员应该对这些操作使用这些 HTTP 请求:

*   `POST example.com/posts/` —创建新的博客文章
*   `GET example.com/posts/r83fj3` —通过 id 获取博客文章
*   `PATCH example.com/posts/r83fj3` —根据博客文章的 id 更新其特定属性
*   `PUT example.com/posts/r83fj3` —用 id 替换博客文章
*   `DELETE example.com/posts/r83fj3` —删除博客文章

请注意修补和上传请求之间的区别。

## 避免自定义(非 CRUD)操作

一些资源可能需要支持不能很好地映射到 HTTP 动词的动作。例如，一篇博文可能需要*发布*和*未发布*。

一种流行但非 RESTful 的方法是使用如下 URL:

*   `POST /posts/d92hf73/publish`
*   `POST/posts/d92hf73/unpublish`

这违背了 REST 原则，因为`/posts/d92hf73/publish`不是资源的 URL。让我们看看如何以 RESTful 方式添加发布和取消发布博客文章的支持。

一种 RESTful 但不是特别优雅的方法是使用如下的`PATCH`请求:

`PATCH example.com/posts/d92hf73`带`{ “status”: “published” }`或`{ “status”: “unpublished” }`等参数。

另一种方法是引入一个嵌套资源，并将其命名为`commands`或`actions`，那么请求看起来会像这样:

`POST example.com/posts/d92hf73/commands { “name”: “publish” }`

`POST example.com/posts/d92hf73/commands { “name”: “unpublish” }`

这是一种更 RESTful 的、也更具可扩展性的方法，因为它允许在不改变接口的情况下向 API 添加更多的 URL。

第三种方法是引入表示“发布”概念的嵌套资源，比如“publication”。我认为这是这三个中最好的选择。

*   `POST example.com/posts/d92hf73/publication`通过创建出版物来发布博客文章。
*   `DELETE example.com/posts/d92hf73/publication`通过删除出版物来取消发布博客文章。

## 正确使用 HTTP 动词

根据 REST 原则，请求的含义应该用 HTTP 动词来表示。让我们看看它们的意思和它们表达的动作:

*   `POST example.com/posts`创造新资源
*   `GET example.com/posts/d92hf73`检索资源
*   `PATCH example.com/posts/d92hf73`T20 更新指定的资源属性
*   `PUT example.com/posts/d92hf73` *替换*资源
*   `DELETE example.com/posts/d92hf73`删除资源
*   `GET example.com/posts`检索资源集合

GET 请求应该是幂等的，它们永远不应该改变资源的状态。此外，为了 API 的最佳性能，GET 请求也应该是可缓存的。我将在下面详细讨论缓存。

`PUT`和`DELETE`请求也应该是等幂的。

`POST`和`PATCH`不是等幂的——它们改变资源的状态。

## 考虑使用 ETag 标题

向 API 发出`UPDATE`请求时，资源可能已经被修改，并且`PUT`或`PATCH`请求可能会覆盖它。

有一种方法可以用`ETag`割台抓住那个箱子。`ETag`响应 HTTP 头是资源的特定版本的标识符。这个头中的值是在服务器端为响应计算的，它可以是资源的散列。

当重新加载同一资源以验证是否需要重新提取该资源时，或者当更新该资源以验证该资源在提取和发送回之间没有改变时，可以将该值发送到服务器。

在第一种情况下，该值在`If-None-Match`请求头中被发回，如果该值与资源的`ETag`相同，API 可以用空主体的`304 Not Modified`进行响应。如果在服务器上构建资源的成本很高，这将很有帮助。

在第二种情况下，API 应该检查`ETag`值是否仍然相同，否则返回`412 Precondition Failed`错误响应。这允许捕捉在另一个请求接收到`POST`请求之前资源已经被修改的情况，并防止覆盖数据。

# 反应

我们已经讨论了资源、请求和动词。现在让我们看看成功和失败请求的响应结构。

当返回一个资源时，最好使用简单的 JSON 结构，即:

```
{
  "id": "d92hf73",
  "title": "Best Practices for Public REST APIs",
  "body": "Foo bar...",
  "createdAt": "2020-08-30 11:17:09 UTC"
}
```

这是一个非常简单的结构——它只包含描述资源的属性。如果我们想给这个 JSON 对象添加更多的信息呢？

如果您熟悉 REST 架构的 HATEOAS 组件，那么您可能希望包含到相关实体的链接以及可以在资源上执行的命令。假设我们可以发布或删除一个帖子，并且一个帖子有评论和作者。

有两种方法可以做到这一点:

```
{
  "id": "d92hf73",
  "title": "Best Practices for Public REST APIs",
  "body": "Foo bar...",
  "createdAt": "2020-08-30 11:17:09 UTC",
  "commentsUrl": "https://example.com/posts/d92hf73/comments",
  "authorUrl": "https://example.com/author/f9f3ks0",
  "publishUrl": "https://example.com/posts/d92hf73/publication",
  "deleteUrl": "https://example.com/posts/d92hf73"
}
```

另一种方法是使用“信封”:

```
{
  "data": {
    "id": "d92hf73",
    "title": "Best Practices for Public REST APIs",
    "body": "Foo bar...",
    "createdAt": "2020-08-30 11:17:09 UTC"
  },
  "links": {
    "commentsUrl": "https://example.com/posts/d92hf73/comments",
    "authorUrl": "https://example.com/author/f9f3ks0",
    "publishUrl": "https://example.com/posts/d92hf73/publication",
    "deleteUrl": "https://example.com/posts/d92hf73"
  }
}
```

对我来说，第一个选项看起来更简洁，因为它没有引入额外的嵌套和与资源无关的结构。

在响应中包含链接允许 API 客户端开发人员避免在他们的代码中构造 URL。这也允许 API 开发者在不破坏 API 客户端的情况下更改 URL。至少是那些依赖 API 返回的链接的用户。

另一个有趣的实践是在响应中包含资源的类型和到它自身的链接。在这种情况下，响应可能如下所示:

```
{
  "id": "d92hf73",
  "self": "https://example.com/posts/d92hf73",
  "type": "Post",
  "title": "Best Practices for Public REST APIs",
  "body": "Foo bar...",
  "createdAt": "2020-08-30 11:17:09 UTC",
  "commentsUrl": "https://example.com/posts/d92hf73/comments",
  "authorUrl": "https://example.com/author/f9f3ks0",
  "publishUrl": "https://example.com/posts/d92hf73/publication",
  "deleteUrl": "https://example.com/posts/d92hf73"
}
```

响应 JSON 中的键的大小写应该总是驼峰式的。因为它是 Javascript 的标准，而 JSON 来自 Javascript 世界。

# 收集回应

集合响应可以使用信封来包装集合项目。这里有一个例子，博客文章在`data`键下返回。本例中的`links`键包含指向下一页和上一页文章的 URL。

`GET example.com/api/posts?page=2&limit=10`

```
{
  "data": [{
    "id": "d92hf73",
    "title": "Best Practices for Public REST APIs"
  }, {
    "id": "hwp8a2j",
    "title": "REST vs GraphQL"
  }],
  "links": {
    "nextUrl": "[d92hf73](example.com/posts?page=3&limit=10)",
    "prevUrl": "example.com/posts"
  }
}
```

集合响应不必使用信封。在这种情况下，API 可以在 HTTP 响应头中返回分页 URL，如下例所示:

`GET example.com/api/posts?page=2&limit=10`

```
[
  {
    "id": "d92hf73",
    "title": "Best Practices for Public REST APIs"
  },
  {
    "id": "hwp8a2j",
    "title": "REST vs GraphQL"
  }
]
```

HTTP 响应标头:

`Link: <https://example.com/api/posts?page=1>; rel=”prev”, <https://example.com/api/posts?page=3>; rel=”next”, <https://example.com/api/posts?page=34>; rel=”last”, <https://example.com/api/posts>; rel=”first”`

## 考虑使用 JSON:API

如果您更愿意使用现有的标准来格式化 API 中的响应，那么您可能希望使用 JSON:API。它相对流行，并提供或多或少的标准响应结构。另一方面，它相当冗长，维护起来可能很耗时。

参见:[https://jsonapi.org/](https://jsonapi.org/)

## 对空响应使用有意义的 HTTP 响应代码

`POST`创建资源的请求可能不返回任何内容，而是使用`201 Created` HTTP 状态并设置`Location` HTTP 响应头来告诉 API 客户端如何检索资源。

在一些 API`POST`、`PATCH`、`PUT`中，请求可能不返回数据，因为请求处理是异步完成的。在这种情况下，请求可能会返回`202 Accepted` HTTP 状态，以指示请求已经排队等待处理。

当`POST`、`PATCH`、`PUT`、`DELETE`请求在语义上不需要返回任何响应时，应该用`204 No content` HTTP 响应状态进行响应。

## 使用丰富的错误响应

错误响应不仅应该表示错误信息，还应该使用正确的 HTTP 状态代码。如果 API 客户端发送的数据导致错误，则代码应该是 4xx 代码之一。如果错误是由服务器端的问题引起的，则应该是 5xx 代码之一。

错误响应应该包含足够的信息来为 API 客户端、API 客户端开发人员以及在某些情况下为 API 客户端用户描述错误。错误响应可能如下所示:

`POST example.com/api/posts`

```
{
  "errorCode": 123,
  "errorMessage": "Blog post title is too long",
  "developerMessage": "title cannot be longer than 255 characters",
  "info": "https://docs.example.com/api/errors/123"
}
```

有时，在处理 API 请求时可能会出现许多错误，例如，在验证 POST 请求主体中发送的数据时。在这种情况下，错误响应可能如下所示:

`POST example.com/api/posts`

```
[
  {
    "errorCode": 123,
    "errorType": "validationError",
    "errorMessage": "Blog post title is too long",
    "developerMessage": "title cannot be longer than 255 characters",
    "info": "https://docs.example.com/api/errors/123"
  },
  {
    "errorCode": 123,
    "errorType": "validationError",
    "errorMessage": "Blog post body is too short",
    "developerMessage": "body cannot be empty",
    "info": "https://docs.example.com/api/errors/123"
  }
]
```

API 开发人员通常从这样的错误响应开始:

`POST example.com/api/posts`

`Error message here.`

但是后来它们迁移到`{ "error": "Error message here" }`，然后可能是`[{ "error": "Error message here" }, { "error": "Another error message here" }]`。因此，从一开始就支持响应中的几个错误可能是个好主意。

# API 版本控制

最好以不需要版本控制的方式来设计和构建 REST API。换句话说，API 开发者应该不惜一切代价避免破坏性的改变。如果你必须引入大规模的突破性变化，那么你就要发布一个新的 API。如果你不得不改变一个特定的资源，以至于它变得与当前版本不兼容，也许那应该是一个新的资源？

但是，如果您决定引入版本控制，这里有两个策略:

*   版本作为 URL 的一部分，
*   请求标头中的版本。

## 作为 URL 一部分的版本

可以指定版本:

*   **在领域层面**，即`api2.example.com/posts`。优点是版本之间尽可能“远”,并且两个版本可以完全不同地实现。这种方法是最接近 API 无版本控制的方法。从 REST 的角度来看，一件好事是资源的 URL 保持“纯净”，它们不需要包含任何像“v1”这样的片段。他们只指向资源。
*   **作为路径片段**，即`example.com/api/v3/posts`。这种方式的优点和上一种差不多，只是网址看起来不是特别“纯粹”。但从实用主义的角度来看，这并不总是一个问题。
*   **作为请求参数**，即`example.com/api/posts?version=3`。这样做的好处是，在没有明确指定版本的情况下，API 开发人员可以选择自动回退到最新或最早的受支持版本。一方面，迁移会“自动”发生，另一方面，如果 API 开发者引入了突破性的改变，可能会破坏 API 客户端。

## 请求标头中的版本

API 客户端还可以使用以下命令指定所需的 API 版本:

*   **自定义 HTTP 请求头**像`Accepts-version: 23`、`Accepts-version: 23`
*   **标准请求头**，如`Accept: application/vnd.example.v23+json`或`Accept: application/json; version=23`

如果 API 使用缓存，那么确保在缓存响应时考虑请求头的值是很重要的。

## 仅使用主要版本号

只使用主要版本号，如 v11，不要使用次要版本，如 v2.2。

## 如果没有指定版本，请使用最新版本

如果没有指定版本，请使用最新的版本，因为如果 API 客户端开发人员在其 API 客户端进行调用时发现错误，将会提示他们指定所需的版本。默认情况下使用最老的版本，没有办法自动告诉 API 客户端开发人员有新版本可用。

## 在所有 API 资源中使用一致的版本。

即，用于博客 API 的`/posts`和`/me`资源应该总是具有相同的支持版本，即使只有一些资源可能在版本之间发生了变化。

## 使用失效响应标题通知资源寿命终止

使用`Sunset`响应头来表明某个版本的资源将被弃用。另一种方法是随机返回`410 Gone` HTTP 响应代码，但是这可能会意外地破坏一些客户端。

# 证明

最佳实践是使用 OAuth2。它被广泛使用，许多开发人员都很熟悉它。不要发明自己的认证机制。通过 OAuth，你可以禁止一个特定的客户端访问你的 API 和特定的应用程序。

但是在某些情况下，您可能允许 API 客户端使用访问令牌。在这种情况下，API 应该允许每个用户有许多令牌。否则，客户不可能在没有任何中断/停机的情况下滚动他们的令牌。

# API 安全性

显然，所有 API 流量都应该通过 HTTPS 发送，这样就可以在传输过程中加密。

## 使用速率限制和节流

使用每个 API 客户端的请求速率限制来检测发出过多请求并可能关闭 API 服务的客户端是很重要的。

一个 API 应该支持 429 响应代码和标题，比如`Retry-After`,以便在请求被重试时提供信息。支持`X-RateLimit-Limit`、`X-RateLimit-Remaining`和`X-RateLimit-Reset`这样的头文件也很棒。希望`RateLimit-*`标题将很快成为标准。

## 考虑每天或每月的请求数量配额

API 应该对每个 API 客户端每月或每天的请求数量有配额。这允许控制单个客户端对 API 的访问，并防止使用太多的计算资源来服务请求。

最好不要尝试自己实现速率限制和配额，而使用第三方 API 网关产品。

# 开发者体验

API 是为其他开发者使用而构建的。所以 API 开发者应该让客户端开发者更容易开始使用他们的 API。

## 保持一致性

API 中的一切都需要一致:命名、错误表示、id、错误代码、状态代码、标题，一切都应该是可预测的。

## 提供高质量的开发人员文档

流行语言的文档、例子、客户端库应该尽可能地免费提供。看到一个需要开发者注册才能访问文档的 API 是非常奇怪的。

文档应该在高层次上描述 API，解释每个资源是什么，列出支持的请求以及成功的响应和错误。API 产生的每种类型的输出都应该被记录下来，并且应该有看起来真实的例子。

确保你的 API 文档是最新的。理想情况下，您应该能够从 API 源代码中生成它们。

对于开发人员来说，开始使用 API 的过程应该是非常快速和容易的。也许您可以引入交互式文档，或者编写快速入门指南，或者录制视频演示？

## 为 API 客户端开发者提供支持

开发人员在使用您的 API 时可能会使用各种语言，因此向他们提供使用不同语言的 API 的示例将是一件非常棒的事情。

更好的是为他们提供 GitHub 上的开源库，他们可以在自己的项目中使用。

监控这些 GitHub 回购是一个好主意，看看开发者是否打开 PRs 或问题。

监视像 Stack Overflow 这样的问答网站也是一个好主意，看看人们是否对你的 API 提出了问题。客户开发人员联系 API 开发人员的支持电子邮件地址可能会很有帮助。

如果你能在 GitHub，或者 Stack Overflow，或者你自己的网站上围绕你的 API 创建一个开发者社区，那将是非常棒的，也将允许 API 客户端开发者互相支持。

## 考虑提供开发者工具

考虑为 API 用户提供关于他们如何使用 API 的见解:他们提出了什么请求，他们得到了多少成功和失败的请求，他们离当前计划的极限有多近。它可能不适用于每一个 API，但是对于一些调试目的可能非常有用。

差不多就是这样。我们已经讨论了设计资源和请求、成功和错误响应、版本控制、API 安全性和开发人员体验的最佳实践。我希望您发现这个小指南很有用，并且能够在设计和构建您的下一个 REST API 时应用它们。

本指南基于我在 2019 年 11 月墨尔本 API Meetup 上的演讲。你可以在这里找到幻灯片[。](https://www.slideshare.net/AndrewGridnev/best-practices-for-public-rest-apis)