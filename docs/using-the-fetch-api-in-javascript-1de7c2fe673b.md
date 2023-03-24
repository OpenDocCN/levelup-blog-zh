# 在 JavaScript 中使用 Fetch API

> 原文：<https://levelup.gitconnected.com/using-the-fetch-api-in-javascript-1de7c2fe673b>

## 什么是获取 API，如何使用它

![](img/a378b07984d8e0c30ba6070ba13593e5.png)

来自 [Brixiv](https://www.pexels.com/@zante) @ [像素](https://www.pexels.com/photo/field-summer-animal-dog-6527911/)

## **什么是 fetch API？**

现代软件开发中一些最常见的任务包括与 API 通信。大多数编程语言都有一个内置的方法或包，使它们能够向 API 发出请求。获取 API 是 JavaScript 实现这一点的方式。Fetch 允许您用相当简单的语法发出 GET、PUT、PATCH、POST 和 DELETE 请求。

## **简单的 GET 请求示例:**

当您想要从 API 中检索数据时，您将使用 GET 请求。下面是一个简单的 GET 请求示例。我们将使用免费的 API [JSON 占位符](https://jsonplaceholder.typicode.com/)。

**注意:如果您尝试在您的终端内用** `**node**` **运行** `**fetch**` **，您可能会得到一个** `**fetch is not defined**` **错误。这是因为默认情况下，fetch 由浏览器的 JavaScript 引擎解释，而不是由 NodeJS 解释。要解决这个问题，使用** [**这个**](https://stackoverflow.com/questions/48433783/referenceerror-fetch-is-not-defined) **堆栈溢出。**

## **解剖的要求**

获取请求的基本结构是`fetch().then().then().`

`fetch()`部分接受将要点击的 API URL 的一个参数。

带有`res => res.json()`的第一个`.then()`将以 json 格式返回 API 响应的主体。

第二个`.then()`是放置应该如何处理返回数据的逻辑的地方。在这种情况下，我们只是控制台记录它。但是，您可以将数据分配给变量或 DOM 元素。

上面的 fetch 请求应该返回一些 JSON 数据，其中包含一个伪 todo 项。

## **带有标题的 GET 请求示例**

在许多情况下，您可能需要通过 GET 请求发送一个身份验证令牌。在这些情况下，向请求添加头部是必要的。报头可以以对象的形式添加到请求的`fetch`部分。

## **删除请求示例**

删除请求的结构非常类似于 GET。您只需要指定您将使用删除请求，因为 fetch 默认使用 GET。

这个请求将返回一个空的 JSON 对象，因为请求的对象不再存在。

在需要进行身份验证来删除对象的情况下，可以像在 GET 请求中那样传递消息头。

## **发布、修补和上传请求的示例**

POST、PATCH 和 PUT 请求都非常相似。唯一的区别是您需要指定要发送的数据，并将其转换为 JSON。

## **请求中的错误**

通过将一个`.catch()`承诺链接到请求的末尾，可以很容易地处理请求时的错误。

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev) 

如果你喜欢这篇博文，并且觉得它很有用，可以考虑为它鼓掌，并在 Medium 上关注我。此外，考虑使用我的推荐链接在此注册 Medium [。只要你还是会员，我就能得到一点回扣。如果你愿意，你也可以在这里给我买杯咖啡。非常感谢！](https://tarricsookdeo.medium.com/subscribe)