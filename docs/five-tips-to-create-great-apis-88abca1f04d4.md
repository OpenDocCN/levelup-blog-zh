# 创建优秀 API 的五个技巧

> 原文：<https://levelup.gitconnected.com/five-tips-to-create-great-apis-88abca1f04d4>

## API 最佳实践概述

![](img/fc96d53b64a02aa9fbc8a2fdef449f64.png)

照片由[乔丹·哈里森](https://unsplash.com/@jordanharrison?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

编写 API 端点代码时，让用户能够轻松访问它们是非常重要的。这就是为什么我决定写下一些每个人都应该遵循的最佳实践。

# 1.使用 JSON

如果我只能给你一条建议，那就是这条。在创建 API 时，应该总是在请求和响应的主体中使用 JSON。

这是大多数 API 所做的，也是你的用户(很可能)期望发送和接收的。事实上，JSON 是一种广泛使用的格式，任何人都很容易理解。

虽然 JSON 最初是为 JavaScript 创建的(这个名字的意思是**JavaScript object Notation**)，但是现在大多数语言都可以解析 JSON 对象。下面是一个 JSON 的例子:

```
{
  "title": "exmaple",
  "author": "John Doe",
  "date": "30 August"
}
```

即使没用过 JSON，也很清楚这个对象包含了什么信息。这在调试您的 API 时非常有用(例如，如果您正在使用 *Postman* )。

如果您以前从未见过 JSON，那么您只需花很少的时间就能理解如何使用它。如您所见，非常类似于 JavaScript 对象(或 Python 字典)。它由括号内的 kay 值对列表组成。每个键和值都用双引号括起来，并用逗号分隔。

# 2.使用标准错误代码

当 API 遇到错误时，您应该返回正确的错误代码。有许多值是标准的，所以您不需要创建自己的代码。

HTTP 响应代码都是三位数，根据第一个数字分为五类:

*   `1xx`:响应包含一些信息；
*   `2xx`:请求执行成功；
*   `3xx`:请求被重定向；
*   `4xx`:出现客户端错误；
*   `5xx`:出现了服务器错误。

最常见的代码是:

*   `200` **Ok:** 一般成功响应；
*   `201` **已创建:**根据用户的要求，创建了一些东西；
*   `401` **未授权**:因用户未通过认证(或认证失败)而发生错误；
*   `403` **禁止**:用户通过认证，但不允许访问该资源；
*   `404` **找不到:**找不到请求的端点/资源；
*   `500` **内部服务器错误:**服务器一般性错误；
*   `501` **未实现:**被访问的端点尚未实现。

关于所有代码的完整列表，你应该查看维基百科[*。*](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

通过像其他人一样使用相同的代码，您将使您的 API 更易于访问。

此外，当出现错误时，您还应该返回一个对象，连同错误代码，以及关于该问题的更多信息。至少，该对象应该包含一条带有错误描述的消息。

例如，如果您有一个尚未实现的端点，您可以返回下面的 JSON 对象(以及一个代码`501`):

```
{
  "message": "This endpoint has not yet been implemented",
  "date": "03/01/2022",
  "time": "17:22"
}
```

请记住在程序中保持 error 对象的格式一致，这样用户就可以预测在出现错误时会收到什么(因为这种情况很少发生)。

# 3.命名规格

当给端点命名时，记住不要在其中包含动词。事实上，所执行的动作类型应该完全由请求类型(GET、POST 等)来指定。端点的路径应该只告诉你*用户想要访问什么* *资源*，而不是*他们想要执行什么动作*。

为了更清楚地说明这一点，假设我们正在为待办事项列表创建一个 API。从其获取列表的端点应该**而不是**被称为`fetchList`。名字应该就`todolist`吧。这样，您也可以使用相同的 URL 向列表中添加新元素:

*   对`todolist`获取列表的 GET 请求
*   向`todolist`添加新元素的 POST 请求。

如果名字是`fetchList`，这是不可能的，你需要两个不同的端点:

*   `fetchList`处理 GET 请求
*   `addElement`处理 POSt 请求。

很明显，这会使您的 API 变得更大更混乱(什么是对`addElement`的 GET 请求，或者对`fetchList`的 POST 请求)？).

# 4.嵌套端点

在命名 API 的端点时，将它们嵌套在一起通常是有意义的。每当你决定这样做的时候，你应该总是遵循一个清晰的逻辑，这样就可以立刻清楚为什么某个 URL 会出现在选择的位置。

例如，对于上一段中的待办事项列表，我们创建了一个`/todolist`端点，用户可以从中获取所有元素或添加新的元素。那么明智的做法是添加一个嵌套的 URL `/todolist/:id`来只获取具有指定 id 的元素。

另一方面，添加一个`/todolist/users`端点是没有意义的。直觉上，大多数人只会搜索一个`/users` URL，因为用户不是待办事项列表中的一个子类别。

最后，向所有端点添加顶层嵌套可能会很有用，它指定了 API 的版本。例如，我们会有:

*   `/v1/todolist`、`/v1/todolist/:id`和`/v1/users`；
*   `/v2/todolist`、`/v2/todolist/:id`和`/v2/users`；
*   诸如此类…

以这种方式，当创建 API 的新版本时，我们保留旧的端点可用，并且我们不会破坏使用旧版本的人的代码。

# 5.过滤

通常会使用 API 从数据库中获取数据。通常，用户只想要它的一个子集，但是如果 API 不能过滤后端的数据，我们将需要返回大量(大部分)无用的数据。

这可能会大大降低前端的速度，尤其是在数据量非常大或者互联网速度很慢的情况下。对此的一个解决方案是允许用户向请求添加一些查询，以便我们只在响应中发送用户需要的内容。

如果您想了解更多关于 API 查询的知识，我推荐下面这篇文章:

[](https://rapidapi.com/blog/api-glossary/parameters/query/) [## 什么是查询参数(在 API 术语中)| API 词汇表

### 你有没有在搜索引擎上输入一个地址，结果却是它返回一个又长又复杂的东西，还带有等号…

rapidapi.com](https://rapidapi.com/blog/api-glossary/parameters/query/) 

# 结论

我希望这篇文章是有用的，并且你将在你的下一个项目中实现我的建议。以下是一些可能对您有用的资源:

*   什么是 JSON(维基百科):[https://it.wikipedia.org/wiki/JavaScript_Object_Notation](https://it.wikipedia.org/wiki/JavaScript_Object_Notation)
*   HTTP 响应代码:[https://www . digital ocean . com/community/tutorials/how-to-trouble shooting-common-HTTP-error-codes](https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-http-error-codes)
*   查询参数:[https://rapidapi.com/blog/api-glossary/parameters/query/](https://rapidapi.com/blog/api-glossary/parameters/query/)

谢谢你看完！