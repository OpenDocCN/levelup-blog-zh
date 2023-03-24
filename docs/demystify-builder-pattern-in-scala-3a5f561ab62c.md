# 揭开 Scala 中构建器模式的神秘面纱

> 原文：<https://levelup.gitconnected.com/demystify-builder-pattern-in-scala-3a5f561ab62c>

![](img/55c83efe802c8d6cb32aceba1261839e.png)

*最初发表于*[*https://edward-huang.com*](https://edward-huang.com/scala/software-development/programming/2020/03/09/demystify-builder-pattern-in-scala/)*。*

当您需要一步一步地构造一个复杂的对象而又不使构造代码复杂化时，构建器模式解决了这个问题。

想象一下设计一个 HTTP 处理程序，其中您需要构造一个 HTTP 客户端，它接收 URL、各种头部，以及一个有效载荷主体，这取决于您想要的请求类型。

```
val postRequestClient = HttpClientRequest(endpoint = "https://someURL.com", method = "POST", header = Map("content-type" -> "application/json"), body = "your json body")

*// send your post request* HttpClient.send(postRequestClient)
```

你可以这样设计上面`HttpClient`的构造函数:

```
class HttpClientRequest(endpoint:String, method:String, header:Map[String, String], body:Option[String])
```

然后，你还需要考虑没有`requestBody`的 get 方法。

```
*// inside HttpClientRequest class* 
*// add another constructor* def this(endPoint:String header:Map[String,String]) {
  HttpClientRequest(endpoint, "GET", header, None)
}
```

可以想象，一步一步初始化许多字段和嵌套对象的费力过程。创建这种初始化的代码极其复杂和冗余——有各种构造函数字段的组合。如果新构造函数参数的大小增加，类中构造函数的几种组合也会增加。

我们将如何解决这个问题？

# 拯救建筑模式

builder 模式建议您将初始化模式从常规构造函数抽象到 builder 类。

`builder`类将这个对象的创建分成一系列步骤。因此，当您想要初始化这个对象时，您需要执行 builder 对象中的一系列步骤。

重要的是，当调用者执行对象创建中的一系列步骤时，他们不需要调用所有的步骤。执行对象所需步骤的必要调用方。

让我们用名为`HttpClientBuilder`的构建器类重构上面的`HttpClient`例子。

注意:Scala 通过使用带有默认参数和命名参数的不可变`case class`为上述问题提供了一个优雅的解决方案。

```
class HttpClientRequest(endpoint:String, method:String, header: Map[String,String], body:Option[String])

object HttpClientRequest {
  def builder(): HttpClientRequestBuilder = HttpClientRequestBuilder("")
}

*// creating method case class* sealed trait Method
case object GET extends method
case object POST extends method
case object PUT extends method
case object DELETE extends method
case object PATCH extends Method

class HttpClientRequestBuilder(endpoint:String, method:Method = GET, header: Map[String,String] = Map.empty[String,String], body:String = "") {
  def withEndPoint(endpoint:String): HttpClientRequestBuilder = copy(endpoint = endpoint)

  def withMethod(method:Method): HttpClientRequestBuilder = copy(method = method)

  def withHeader(headers:Map[String,String]): HttpClientRequestBuilder = copy(header = headers)

  def withBody(body:String): HttpClientRequestBuilder = copy(body = body)

  def build: HttpClientRequest = new HttpClientRequest(endpoint, method, header)
}
```

我创建了一个密封的特征，这样它就把调用者限制在这个方法上。

Scala `case class`已经为您提供了一个内置的方法`copy`，该方法使用参数中指定的不同对象创建当前对象到新对象的深层副本。

HttpClientBuilder 在初始化`HttpClient`时创建一系列步骤。

现在，您可以像这样创建 HttpClient 方法:

```
val postClientRequest = HttpClient.builder.withEndPoint("https://someURL.com")
  .withMethod(POST)
  .withHeader(Map("content-type" -> "application/json"))
  .withBody("json body ... ").build

HttpClient.send(postClientRequest)
```

# 外卖食品

构建器模式允许您逐步构建一个复杂的对象，这样您就可以通过相同的构建代码创建对象的各种表示。

Builder 模式使用 builder 类将初始化作为一系列要执行的步骤。关于 builder 类最重要的一点是，调用者能够只提供配置对象所需的必要步骤。

Scala 提供了一个不可变的 case 类以及默认实参和命名形参的组合，这与 builder 模式的目的是一样的。

**感谢阅读！如果你喜欢这篇文章，请随意订阅我的时事通讯中的**[](https://edward-huang.com/subscribe/)****来接收关于科技职业的每周文章、有趣的链接和内容！****

**你可以关注我，也可以在[媒体](https://medium.com/@edwardgunawan880)上关注我，以获得更多类似的帖子。**