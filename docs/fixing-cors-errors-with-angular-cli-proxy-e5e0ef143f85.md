# 使用 Angular CLI 代理修复 CORS 错误

> 原文：<https://levelup.gitconnected.com/fixing-cors-errors-with-angular-cli-proxy-e5e0ef143f85>

![](img/7bd225a5298316538c8ca59737346b37.png)

资料来源:Memegenerator.net

在本地主机上构建时尝试访问 API 端点可能会造成创伤，尤其是当出现错误"*请求的资源"*上不存在' Access-Control-Allow-Origin '头时。别担心这不是你的错，这个错误是由于大多数浏览器执行的**同源策略**造成的。

> **同源策略**是一个关键的安全机制，它限制从一个[源](https://developer.mozilla.org/en-US/docs/Glossary/origin)加载的文档或脚本如何与另一个源的资源交互。— [来源](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

你得到这个错误的原因是因为你正在向一个不同于你当前所在的源发出请求。例如，当您试图从`https://api.spotify.com/v1`访问资源时，您的应用程序正在`http://localhost:4200`上运行。`localhost`和`api.spotify.com`是两个不同的起源，它们也分别运行在不同的协议`http`和`https`上，这是一个**跨起源请求，**这足以让你的浏览器对你无视它的规则感到愤怒。如果服务器在它的头中指定了你的来源，你也不会面临这个挑战。

# 我们能做些什么呢？

![](img/df77c81583b9552492ce97b6a92a4848.png)

照片由[Olav Ahrens rtne](https://unsplash.com/@olav_ahrens?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 使用代理

您可能并不总是能够访问服务器上运行的代码。在这种情况下，您可以做的是为您的 angular 应用程序设置一个代理。该代理基本上试图欺骗浏览器，它通过假装在相同的源上有 API 来做到这一点，而实际上它正在从另一个源访问资源。这样，浏览器将能够相信**同源策略**被遵守，从而消除 CORS 错误。

我们如何实现这一点，让我们马上开始。

## 第一步:

创建一个 **proxy.conf.json** 文件并将下面的代码复制到其中。

这里需要注意几件事:

1.  对象的属性指定代理的路由，嵌套对象指定配置。因此，为了发出请求，url 将是`[http://yourhost/api](http://yourhost/api)`(对于我的项目，将是`[http://localhost:4200/api](http://localhost:4200/api).)` [)。](http://localhost:4200/api).)
2.  嵌套对象的属性指定了您试图访问的 API url，在本例中是 spotify.com。
3.  `“secure”`属性用于指定目标是通过 http 还是 https 被服务。它当前被设置为 true，因为 spotify.com 端点是通过 https 提供服务的。
4.  `“changeOrigin”`属性指定目标位于不同于应用程序当前运行所在的另一个域中。在这种情况下，它被设置为`true`，因为应用在`localhost`上运行，而目标在`spotify`上。如果它们都在`localhost`上，那么它将是假的。
5.  `"pathRewrite"`属性允许你修改应用程序如何与目标交互。如果没有`“pathRewrite”`属性，对代理 url `http://localhost:4200/api`的每个调用都将对应于`[https://api.spotify.com/v1](https://api.spotify.com/v1)/api`。如果 spotify 有一个`api`端点，这不会是一个问题，但由于实际上他们不提供这样的端点，这将导致一个错误。因此，用`“^/api”: “”`设置`"pathRewrite"`嵌套对象有助于移除 url 末尾不需要的“api”。这反过来确保调用`[http://localhost:4200/api](http://localhost:4200/api)`将对应于`[https://api.spotify.com/v1](https://api.spotify.com/v1)`。

## 第二步:

使用以下两种方法之一将应用程序设置为使用代理:

1.  通过运行`ng serve --proxy-config proxy.conf.json`，确保在您的基本目录下创建了`proxy.conf.json`文件。
2.  另一个选项是修改`angular.json`文件，并将下面几行代码添加到`architect`配置对象中。

**一些需要注意的事情**:
serve 属性已经在 angular.json 文件中预先配置好了，您唯一需要添加的内容在第 5 行。

一旦添加完毕，运行`ng serve`来启动你的应用程序。

## **关于生产环境**

我发现在 Nginx 上创建代理服务器更容易。不幸的是，Nginx 服务器的配置超出了本文的范围，我会在另一篇文章中讨论。

## 其他方法

如果您可以访问运行端点的服务器，您可以简单地在您的头中添加`Access-Control-Allow-Origin: *`来允许所有的源，或者您可以添加`Access-Control-Allow-Origin: http://localhost:4200`来只允许来自本地主机的请求。

![](img/005ba2e822b768adee63eac1dece75ae.png)

[Alex](https://unsplash.com/@alx_andru?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

***延伸阅读***

[同源政策](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

[跨产地请求共享(CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

[Angular —代理到后端服务器](https://angular.io/guide/build#proxying-to-a-backend-server)

[门禁允许原点](http://Access-Control-Allow-Origin)

请在下面的评论区留下您的评论、问题或反馈，我会尽快回复。

此外，如果你做到了这一步，请继续前进，并给予一两次掌声。那会让我非常开心。:).