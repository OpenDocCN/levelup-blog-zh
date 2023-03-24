# 设计开发人员友好的 RESTful APIs

> 原文：<https://levelup.gitconnected.com/design-developers-friendly-restful-apis-d7a79b7d47a0>

![](img/1653709651c4f1efaceae387f6ea0798.png)

# 我们用 API 做什么？

我们生活在 API 的时代，在这个时代，我们为数据支付的费用超过了为石油支付的费用。为了将数据库中的大量数据显示在显示器上，它需要通过一个服务器，借助 API，该服务器可以在互联网上访问这些数据。您只需将正确的*请求*和正确的*查询*和*方法*发送到正确的*端点*，就可以获得您想要的数据。这些数据可以是任何东西，从口袋妖怪的名字和类型，到天气预报，甚至是体育博彩和政治及商业新闻。

现在，如果两个人想交流，他们应该有共同的语言，以避免变成两个模仿智人。更别说我们是会说话的机器了。通过互联网的通信遵循无数的协议，其中之一是 HTTP，该协议用于以 HTTP 请求和响应的形式交换数据。RESTful APIs，当今 API 的“标准”,是建立在 HTTP 和 JSON 之上的。

# 为什么我们应该遵循 API 设计模式？

如果你是一个全栈开发人员，独自开发一个小规模的应用程序，这对你来说会很奇怪。但是在这个行业中，几乎不可能找到既做前端又做后端开发的人，至少不会在同一个项目中。这意味着你可以找到两个人，来自两个遥远的大陆，为同一个项目工作。一个是*创建*API 并处理即将到来的请求，而另一个是*消费*API 并*发送*请求。当然，如果他们没有一些共同的工作模式，这是不可能的。

# RESTful API 设计模式

我们将讨论一个后端开发人员应该做些什么来使他的 API 易于理解并加速开发过程。此外，它将有助于使您的 API 寿命更长，并被来自世界各地的更多开发人员使用。

## URI 格式

*   **用正斜杠** (/) **分隔符** 这是用来表示资源之间的层次关系
*   **不要使用正斜杠** 避免在你的 API 末尾使用正斜杠，因为这只会引起混乱，没有任何重要意义。

```
[http://xx.yy.zz/shapes/polygons/](http://xx.yy.zz/shapes/polygons/)
```

*   **使用连字符** (-) **避免下划线** (_)连字符提高了 URI 名称、路径和片段的可读性，有助于客户轻松浏览和解读。您应该避免在路径中使用下划线，因为在呈现视觉提示时，由于计算机字体的原因，字符可能会部分模糊或隐藏。此外，还有被谷歌搜索引擎忽略的风险。
*   坚持小写字母
    RFC 3986 将 URIs 定义为区分大小写，除了方案和主机组件。这就是为什么你应该优先考虑小写的用法。另外，请注意 I(大写 I)和 L(小写 L)非常相似。这在很大程度上被黑客用来欺骗人们进入虚假网站。

```
[http://xx.yy.zz/shapes/polygons](http://xx.yy.zz/shapes/polygons)
HTTP://XX.YY.ZZ/shapes/polygons // same as previous[http://xx.yy.zz/shapes/polygons](http://xx.yy.zz/shapes/polygons)
HTTP://XX.YY.ZZ/SHAPES/Polygons // different than previous
```

## 小心使用 HTTP 方法

HTTP 方法有它们的名字是有原因的。您应该使用:

*   **获取**，以防我们读取数据
*   **POST** 以防我们创建新数据
*   如果我们用一个新的资源来更新我们数据中的整个资源，放入
*   ****修补**以防我们部分更新资源**
*   ****删除**以防我们删除现有数据**

## ****制定好你的端点****

**开发人员往往对如何命名他们的端点感到非常困惑。这里有一些你在使用 REST 时应该遵守的规则。**

*   ****包含版本号** 现在看起来可能不那么明显，但是总有一天，你会觉得你的 API 结构无法处理你的系统，无法再向上扩展。这就是你要认真考虑从头开始构建一切的地方:新的模型、新的实体、新的控制器、新的端点……如此大的迁移将需要你的 API 的新版本。这就是为什么在端点的开头包含版本号是明智的。这样，您仍然能够在扩展时处理遗留请求，并且用您的 API 构建的系统不会崩溃。**
*   ****使用一致的子域名****

**如果您希望开发人员使用和测试您的 API，应该向他们提供一致的子域:**

```
api.amazing-app.com // for api requests
dev.amazing-app.com // for development environment
```

*   **使用名词而不是动词。让 HTTP 方法做动作
    请求方法是有意义的，应该给出请求所需动作的想法。**

```
GET http://[api.example.com/v1/users](http://www.example.com/api/v1/users) // DO
GET http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/get-users](http://www.example.com/api/v1/users/get-users) // DONTPOST http://[api.example.com/v1/users](http://www.example.com/api/v1/users) // DO
POST http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/create-user](http://www.example.com/api/v1/users/create-user) // DONTPUT http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/:id](http://www.example.com/api/v1/users/:id) // DO
PUT http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/update-user/:id](http://www.example.com/api/v1/users/update-user/:id) // DONTDELETE http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/:id](http://www.example.com/api/v1/users/:id) // DO
DELETE http://[api](http://www.example.com/api/v1/users)[.example.com/v1/users/delete-user/:id](http://www.example.com/api/v1/users/delete-user/:id) // DONTDELETE [http://api.example.com/v1/users/](http://api.example.com/v1/users/:id)1234 // DO
GET [http://api.example.com/v1/delete-user?id=1234](http://api.example.com/v1/delete-user?id=1234) // DONT
POST [http://api.example.com/v1/1234/delete](http://api.example.com/v1/1234/delete) // DONT
```

**文档名称使用单数名词，集合和存储使用复数名称。**

```
http:/​/api.example.com/​v1/​profiles/customers
[http://api.example.com/v1/profiles/customers/memberstatus](http://api.example.com/v1/profiles/customers/memberstatus)
http://api.example.com/v1/profiles/customers/​memberstatus/​preferences
```

**控制器资源可以使用动词或动词短语。**

```
http://api.example.com/v1/profiles/customers/​memberstatus/reset
http://api.example.com/v1/profiles/customers/​memberstatus/activate
```

**这将使你的 API 一致，易于使用和发挥。这只是让你开始。关于*授权、* *请求参数和查询、*等等，还有很多要看的。但那是以后的事了**