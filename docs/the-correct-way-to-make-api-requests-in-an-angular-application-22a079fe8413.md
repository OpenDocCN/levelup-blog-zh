# 在 Angular 应用程序中发出 API 请求的正确方式

> 原文：<https://levelup.gitconnected.com/the-correct-way-to-make-api-requests-in-an-angular-application-22a079fe8413>

## 有角的

## 角度拦截器，HttpClient vs HttpBackend，捕捉错误，召回 API，防止 XSRF 攻击。

![](img/c94983372ce3f614e380b30d1d1ba49d.png)

彼得·塞夫科维奇在 [Unsplash](https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本文中，我将讨论如何在 Angular 应用程序中执行请求。

1.  使用拦截器来修饰请求
2.  HttpClient vs HttpBackend
3.  其他方法

【https://betterfullstack.com 查看 [*更多类似内容*](https://betterfullstack.com)

我写这篇文章是基于我多年从事前端工作的经验(在 Angular 工作了 4 年)。如果我做错了，请随时纠正我。

# 使用拦截器来修饰请求

当我们使用 API 时，HTTP 拦截是一个主要的特性。使用拦截，您可以声明*拦截器*，这些拦截器检查并转换从您的应用程序到服务器的 HTTP 请求，以及从服务器返回到应用程序的响应。

**简单来说:**

在现实世界中，用户登录后，发送到后端的每个 API 都需要在头中添加授权，以验证用户身份验证和授权。

示例 1:使用拦截器向请求添加一个 JWT

使用拦截器将 jwt 从状态添加到请求中

示例 2:使用拦截器来控制应用程序上的微调器

使用拦截器控制应用程序上的微调器

示例 3:使用拦截器缓存请求

使用拦截器缓存请求

你可以做更多与请求相关的事情:

*   在 API 失败时显示一个模态。
*   根据响应后的状态代码添加更多逻辑
*   覆盖请求 URL 或请求正文

拦截器会影响你所有的请求。那么如何在 Angular 中定制请求并禁用拦截器呢？让我们来看看

# HttpClient vs HttpBackend

我观察到许多开发人员只在 Angular 项目中使用 [HttpClient](https://angular.io/api/common/http/HttpClient) ，每次他们需要向后端发送请求。这是不正确的。

为什么？

因为我们通过 HttpClient 发送的请求总是要经过 Angular 的拦截器。为了解决这个问题，一些开发人员将编写更多的代码来处理这个问题，我称之为“欺骗代码”来禁用拦截器。一些开发人员不会对请求使用拦截器，而是给每个请求添加一个头。

我们的代码将充满代码气味和技术债务。开发人员必须通过使用上面两个选项中的一个来做更多的工作。

如果你正在读这篇文章，你就是解决这种情况的正确方法。

**解决方案**:同时使用 [HttpClient](https://angular.io/guide/http#http-interceptors) 和[http backen](https://angular.io/api/common/http/HttpBackend)来发送请求。

这里唯一不同的是 [HttpBackend](https://angular.io/api/common/http/HttpBackend) 将请求直接发送到后端，而不经过拦截器链。

示例 HttpService:

使用 httpclient 和 httpbackend 的示例 http 服务

您将做出决定，哪个通过拦截器，哪个不通过。

一些需要不同头或者添加额外信息的特定 API，可以使用 [HttpBackend](https://angular.io/api/common/http/HttpBackend) 手动定制。

# 其他方法

我使用这个标题是因为这些都是应用程序中可能有也可能没有的小用例。

**失败后如何调用 API？**

重试失败的请求最多 3 次

如果请求失败，使用`retry()`添加尝试请求的时间。

**如何捕捉错误？**

我建议您在拦截器的`next()`方法中捕捉一个错误。

如何在拦截器上捕捉错误的示例

**如何防范** [**XSRF 攻击**](https://en.wikipedia.org/wiki/Cross-site_request_forgery) **？**

当执行 HTTP 请求时，拦截器从 cookie 中读取一个令牌，默认为`XSRF-TOKEN`，并将其设置为 HTTP 头`X-XSRF-TOKEN`。因为只有在您的域上运行的代码才能读取 cookie，所以后端可以确定 HTTP 请求来自您的客户端应用程序，而不是攻击者。

如果您的后端服务对 XSRF 令牌 cookie 或头使用不同的名称，使用`[HttpClientXsrfModule.withOptions()](https://angular.io/api/common/http/HttpClientXsrfModule#withOptions)`来覆盖。

使用`[HttpClientXsrfModule](https://angular.io/api/common/http/HttpClientXsrfModule#withOptions) to overrite XSRF token`

# 结论

本文包含了我们在使用 Angular 应用程序的 API 时需要的所有信息，包括:

*   使用拦截器来管理请求和响应
*   如何使用 HttpClient 和 HttpBackend
*   如何在请求失败时重新调用请求、捕捉错误并防止 XSRF 攻击。

我希望这篇文章对你有用！你可以在[媒体](https://medium.com/@transonhoang?source=post_page---------------------------)上关注我。我也在[推特](https://twitter.com/transonhoang)上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

[](https://betterfullstack.com/stories/) [## 故事-更好的全栈

### 关于 JavaScript、Python 和 Wordpress 的有用文章，有助于开发人员减少开发时间并提高…

betterfullstack.com](https://betterfullstack.com/stories/)