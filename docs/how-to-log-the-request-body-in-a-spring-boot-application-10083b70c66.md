# 如何在 Spring Boot 应用程序中记录请求正文

> 原文：<https://levelup.gitconnected.com/how-to-log-the-request-body-in-a-spring-boot-application-10083b70c66>

![](img/30bc3b3493612527c780568ffa3fcae3.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当您构建一个 Spring Boot 应用程序时，出于审计的目的，您很可能希望记录对应用程序发出的每个`POST`和`PUT`请求的请求体。最好的方法是创建一个`Filter`，并通过过滤器链注册它。它将负责读取请求体并将其记录到标准输出中。

但是在我们开始实现它之前，我们先来分析一下`ServletRequest`是如何处理的:

当我们谈论用 Java 处理内容时——在我们的例子中是请求体，一切都用流来表示。我们可以从一个`InputStream`读取内容，并向一个`OutputStream`写入内容。当您的应用程序收到请求时，会在后台创建一个`InputStream`,然后被读取，内容被 Jackson 反序列化为特定的类型。当您的应用程序发送响应时，响应的数据被写入到一个`OutputStream`中，然后由某个较低级别的实体处理，该实体通过网络将内容发送到客户端。

在这篇文章中，我们只对阅读那些内容感兴趣。我们可以创建一个新的过滤器`LogRequestFilter`，它将读取请求的`InputStream`，并记录请求的`String`表示。

这显示在下面的代码中——使用方法`logPostOrPutRequestBody`我们能够从`HttpServletRequest`中读取`InputStream`并将其记录到标准输出中。

如果您使用注册的过滤器运行应用程序，然后向任何注册的端点发出一个`POST`或`PUT`请求，您将能够在标准输出中看到记录的请求体。

但是，返回的响应是`5xx — Internal Server Error`，如果您研究应用程序的标准输出，您会注意到以下异常:

```
**DefaultHandlerExceptionResolver** : Resolved [org.springframework.http.converter.HttpMessageNotReadableException: Required request body is missing
```

如果您熟悉读取`InputStream`的工作方式，您可能知道这个问题的答案——在过滤器被处理后，请求的输入流将被重新读取，然后内容将被`Jackson`解析。

但是，一个输入流只能被读取一次。

这就是为什么在第二次尝试读取输入流时，我们会看到请求主体丢失的错误。

我们需要一种不同的方法来解决这个问题，为此，我们需要更深入地了解`HttpServletRequest`是如何运作的。

## HttpServletRequest

从`ServletRequest`中读取包含请求体的 InputStream。如果你深入研究`ServletRequest`类，你会注意到它有两个方法:

```
getReader()getInputStream()
```

因此，每当 Jackson 需要读取和反序列化主体时，就会在 ServletRequest 对象上调用`getInputStream`。

出于这个原因，我们需要找到一种方法将包装了 `ServletRequest`的*传递给过滤器链，过滤器链将能够读取当前的输入流，缓存它，并将主体记录到标准输出中，然后在每次调用`getInputStream`时，从主体的缓存字节中返回一个新的`InputStream`。*

我们可以通过创建一个扩展`HttpServletRequestWrapper`的自定义类来实现，如下面的代码片段所示:

在这个新类中，我们确保从请求中读取输入流(在构造函数中这样做也可以)，将其存储在一个局部变量中，并覆盖`getReader()`和`getInputStream()`。

理想情况下，getReader 应该返回一个您的`getInputStream(`实现的 Reader，如下所示:

```
return new BufferedReader(new InputStreamReader(getInputStream()));
```

最后一步是提供您自己的必须返回一个`ServletInputStream`的 `getInputStream()`实现。您可以创建一个自定义类来扩展`ServletInputStream`并覆盖所有这些方法，或者直接在`getInputStream()`方法中返回一个匿名类。

在本例中，我们采用第二种方法，首先从缓存的内容构建一个 InputStream:

```
final ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(bodyInStringFormat.getBytes());
```

然后返回知道如何读取该流的新实例`ServletInputStream`。

在最终确定了`CustomHttpRequestWrapper`类之后，我们对`LogRequestFilter`做了一个快速重构，以使用那个包装器并沿着过滤器链转发它。

这一次，当您向应用程序发出`POST`或`PUT`请求时，您应该不会看到任何错误。此外，请求正文将记录在标准输出中。

感谢你阅读这篇文章，希望你觉得有用。

保重！