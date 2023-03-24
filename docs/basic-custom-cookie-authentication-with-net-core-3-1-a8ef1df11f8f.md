# 基本自定义 Cookie 身份验证。网络核心 3.1

> 原文：<https://levelup.gitconnected.com/basic-custom-cookie-authentication-with-net-core-3-1-a8ef1df11f8f>

向. NET core 3.1 应用程序添加简单的自定义基于 cookie 的身份验证

![](img/a0d1b6b6f943a6509ad485b6620c1051.png)

照片由 [Randalyn Hill](https://unsplash.com/@randalynhill?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我订婚了！你不常在博客/教程上看到关于如何开发代码的开头，但是你看到了。

我这样说是因为它提供了本教程来源的背景。我和我的未婚夫想要一个网站，让客人可以选择他们的膳食选择。预制网站有很多选择(有些甚至是免费的)，但我决定自己制作。

这个想法是有一个密码保护的网站。然而，代替普通密码，这意味着客人必须输入他们的详细信息(姓名等。)，我们想要一个邀请特定的密码，然后使该网站能够识别客人并直接存储他们的 RSVP。

最终，我将能够构建一个“组织者”界面来查看所有的响应，很可能是由 GraphQL API 驱动的！(我有一个关于如何创建. NET GraphQL API 的教程[,如果你想学习如何做的话！)](https://mattlaw.dev/blog/graph-ql-with-net/)

为了实现这一点，我不想通过电子邮件来识别用户，然后检查提供的密码；我既不知道每个人的电子邮件，也不想输入它们。所以“开箱即用”的身份验证。网核是不够的。

因此，我们现在可以进入教程的核心部分…自定义 cookie 身份验证。网芯！

假设你已经有了某种形式的. NET 核心 web 应用程序，我将直接跳到重要的部分。

在`Startup.cs`中，您需要包含添加认证(特别是基于 cookie 的认证)的代码。

要编译这段代码，您还需要包含“Microsoft。AspNetCore . authentic ation . cookies " n 获取包并将其添加到文件顶部的 usings 中。

这是做什么的？嗯:

*   在`ConfigureServices`中它讲述了。NET 包含处理请求的认证服务，并且应该使用`CookieAuthentication`方案。
*   然后`AddCookie`调用允许您设置自己的参数，如`LoginPath`。默认情况下，登录路径将带您到`/Accont/Login`，并带有针对`ReturnUrl`的查询参数。
*   我想自定义这一点，因为在这个网站上没有真正的“帐户”的概念。相反，一切都与“邀请”联系在一起。
*   在`Configure`里面增加了两行:`app.UseAuthentication()` & `app.UseAuthorization()`。这些将实际的中间件添加到请求管道中，请求管道自己处理请求。

要测试这一点，只需将`[Authorize]`属性添加到您的一个控制器动作中，它会将您重定向到您上面配置的登录路径(很可能是 404，因为它还不存在)。

接下来，我们需要处理登录！

为此，我创建了一个名为“AccessController”的新控制器。它有两个动作处理程序:`public IActionResult Index()`和`public IActionResult Index(string passphrase)`。

无参数处理程序添加了一个 HttpGet 属性(这样它只能在 Get 请求时访问), HttpPost 属性添加到了另一个。

GET 处理程序很简单:它返回一个包含单个输入(针对“密码短语”)的表单视图。

POST 处理程序稍微复杂一些:

*   它调用一个存储库(我使用的是存储库模式)来使用密码检索一个`Invite`。
*   然后，如果返回一个邀请，它会创建一个新的`ClaimsPrincipal`，包含该邀请的一些基本细节。
*   最后，它“登录”用户，并将他们重定向回主页(这是您将使用`ReturnUrl`参数的地方，但是由于此时用户只能去一个地方，这是后来的改进)

这一切看起来像什么？

就是这样！简单吧？ID 是根据声明主体存储的，因此我可以使用它在任何后续请求中快速方便地检索邀请。

作为额外的收获，要做到这一点，您可以简单地在控制器内的任何处理程序方法中使用`HttpContext.User.Claims.FirstOrDefault(x => x.Type == ClaimTypes.Email)`。

接下来将是我如何设置。NET core 和用于处理我的前端构建的 [ParcelJS](https://parceljs.org/) (提示:这是一个简短的构建，因为它非常简单就可以工作)。

*原发布于*[*https://mattlaw . dev*](https://mattlaw.dev/blog/basic-custom-cookie-authentication-with-net-core-3-1/)*。*