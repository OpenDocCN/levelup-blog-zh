# 使用 Auth0 为桌面撰写 OAuth

> 原文：<https://levelup.gitconnected.com/oauth-in-compose-for-desktop-with-auth0-9990075606a1>

![](img/fd424b38444084d367fef167d538f09e.png)

在桌面应用程序中使用 OAuth 似乎不是最流行的用例，但是我们在这里。我目前正在移植一个 Android 应用程序，以便与 Compose for Desktop 一起构建。除了 Auth0 之外，几乎所有东西都有多平台选择。这就引出了今天的文章。

问题:我们想登录 Auth0，最好支持 OIDC，这样我们可以使用谷歌或脸书连接器等。基本步骤是:将用户发送到 Auth0 托管的网页，当用户登录时侦听重定向，从 Auth0 请求访问令牌。简单吧？

对于这个例子，我使用的是 Compose for Desktop。这里没有太多特定于 Compose 的内容，所以只需做很少的修改，它就可以用于任何 Java UI 工具包。这个例子是用 Kotlin 编写的，但是如果您从使用 Ktor 客户机切换到 Java HTTP 客户机，它可以很容易地移植到 Java。

事不宜迟，第一步:打开浏览器进入登录页面。

这里有相当多的东西需要打开。首先，我们按照 Auth0 在其指南中指定的方式创建一个验证器和挑战码。我不打算解释这些方法，只是提到您需要添加对 Apache Commons 编解码器的依赖，它们使用两个不同的 Base64 模块。行“Desktop.getDesktop()。browse()"在用户的默认浏览器中打开指定的 URL。

第二步:我们等待来自浏览器的 HTTP 请求。

在上一步中，我使用“http://localhost:5789/callback”作为重定向 URI。在这一步中，我们监听浏览器以“code”作为参数重定向到该 URL。我们必须解析查询字符串(可以在 GitHub 存储库中找到)以获得 Auth0 返回的代码。重定向 URI 需要在 Auth0 中的允许回拨 URIs 列表中。您可以将它更改为您想要的任何 URI，但是您需要一种将其绑定到您的应用程序的方法。

最后，我们需要将代码发送回 Auth0 以获取我们的访问令牌。

这里我使用 Ktor 客户机发送一个访问令牌的请求。我们需要传递我们在步骤 1 中创建的验证器和在步骤 2 中收到的代码。客户端 id 和重定向 URI 也是必需的参数。

你可以在 https://github.com/sproctor/AuthCompose 找到一个工作样本。您需要有一个 Auth0 租户和客户端设置，并在 Main.kt 中填写适当的详细信息。