# 在 AWS S3 托管 Next.js 应用程序(带路由)

> 原文：<https://levelup.gitconnected.com/hosting-a-next-js-app-with-routing-on-aws-s3-fb1aa8adf120>

最近，我们在 [Alpe Audio](https://www.alpeaudio.com) 推出了一项新的服务 [Example Finder](https://examplefinder.alpeaudio.com) ，作为我们努力向外部公开更多内容创作引擎的一部分。

使用 Next.js 作为 React 应用程序的首选框架，我们会遇到在应用程序内部而不是从主页访问 URL 的问题。
当刷新或发送链接到类似`/page1`的页面时，你会得到一个 **403 错误**。

这对可用性(发送给朋友特定的页面)和 SEO (google 不能索引内部页面)都非常不利。

我发现 Mark Biek 的这个[很棒的指南](https://via.studio/journal/hosting-a-reactjs-app-with-routing-on-aws-s3)解释了如何为 react js 应用程序修复它。因为我们用的是 Next.js，而且自从他写了以后事情发生了一些变化，我们不得不做一些调整。我写这个是为了以后内部和外部参考。

假设你已经有一个 react.js 网站托管在 s3 上，你需要先在 **S3 桶- >属性- >静态网站托管- >重定向规则**中更改 S3 路由规则。

新的 s3 控制台将路由规则格式更改为 JSON，因此您需要添加以下内容(不要忘记替换**myhost.com**):

在出现 403 或 404 错误的情况下，这些规则会添加`#!/`作为 URL 的前缀。这解决了 react-router 的问题，现在它将加载正确的页面。

现在，我们想从 url 中删除这个前缀，以获得更简洁的解决方案。你需要编辑你的`_app.js`并添加:

# CloudFront 用户

如果您使用 CloudFront，那么**重定向规则**将不起作用，因为服务直接访问 bucket。

为了解决这个问题，将 CloudFront 原点改为静态托管 URL，而不是 bucket。

![](img/ed0570b0a6778147c0dbe91f43cdf77b.png)

照片由[米切尔罗](https://unsplash.com/@mitchel3uo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄