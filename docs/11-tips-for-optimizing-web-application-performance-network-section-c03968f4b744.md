# 优化 Web 应用程序性能的 11 个技巧(网络部分)

> 原文：<https://levelup.gitconnected.com/11-tips-for-optimizing-web-application-performance-network-section-c03968f4b744>

## 怎么做比较好？

![](img/19af160d261b9ae9a47a2b3462334fa9.png)

Marc-Olivier Jodoin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

优化 web 应用程序的性能是一项麻烦的工作，因为其中涉及的知识很混乱。本文总结了一些可以在网络部分进行优化的技巧。

# 1.DNS 预取

```
<head>
  <link rel="dns-prefetch" href="[https://fonts.googleapis.com/](https://fonts.googleapis.com/)" />
</head>
```

每当您的 web 应用程序引用跨来源域上的资源时，您应该将`dns-prefetch`提示放在`<head>`元素中。

这是因为当浏览器请求资源时，它会在发出请求之前将域名解析为 IP 地址。这个解析过程就是 DNS 解析。当您的应用程序引用许多不同的跨来源资源时，这会大大降低加载性能。而`dns-prefetch`可以帮助我们屏蔽 DNS 解析延迟。

[MDN](https://developer.mozilla.org/en-US/docs/Web/Performance/dns-prefetch#best_practices) 为我们描述了三种最佳实践:

1.  仅用于跨域资源。这是因为当用户访问你的 app 时，你的网站背后的 IP 已经被解析了。
2.  `dns-prefetch`(和其他资源提示)可以使用 [HTTP 链接字段](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link)指定为 HTTP 头。
3.  考虑将`dns-prefetch`与`preconnect`提示配对。`preconnect`将帮助我们建立到服务器的连接，包括 DNS 解析、建立 TCP 连接、TLS 握手(HTTPS)等。但是要小心，只为那些关键连接启用它，因为如果一个页面连接到许多第三方域，预先解析和连接它们可能会适得其反。

```
<link rel="preconnect" href="[https://fonts.googleapis.com/](https://fonts.googleapis.com/)" **crossorigin** />
<link rel="dns-prefetch" href="[https://fonts.googleapis.com/](https://fonts.googleapis.com/)" />
```

一些资源如字体是以匿名方式加载的。在这种情况下，您应该使用`preconnect`提示来设置`[crossorigin](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin)`属性。如果忽略它，浏览器将只执行 DNS 查找。

# 2.使用链接标签进行预处理

不仅仅是上面提到的`dns-prefetch`和`preconnect`，link 标签还支持很多预处理情况:

## `prefetch`

```
**<!-- Pre-download and cache a resource, usually a non-critical resource. -->
<!-- May be ignored when busy. -->**
<link rel="prefetch" href="[https://www.example.com](https://www.example.com)" />
```

## `preload`

```
**<!-- Pre-download and cache a resource, usually for critical resources. -->
<!-- Not ignored when busy. -->**
<link rel="preload" href="[https://www.example.com](https://www.example.com)" as="font" crossorigin />
```

## `prerender`

```
**<!-- Not only will the resources be downloaded, but also the execution page will be parsed and pre-rendered. -->**
<link rel="prerender" href="[https://www.example.com](https://www.example.com)" />
```

## `modulepreload`

```
**<!-- A JS module is preloaded, and it will not be executed after the download is completed. -->**
<link rel="modulepreload" href="app.js">
```

# 3.脚本标记—异步和延迟

script 元素对于 web 应用程序非常重要，我们可以合理地使用 script 标签的 async 和 defer 属性来减少加载时间。我有一篇文章详述了:

[](/html-script-element-attributes-async-vs-defer-vs-type-module-610b50a79dbd) [## HTML 脚本元素属性:async vs. defer vs. type='module '

### 脚本元素如何影响页面加载？

levelup.gitconnected.com](/html-script-element-attributes-async-vs-defer-vs-type-module-610b50a79dbd) 

# 4.图像优化

如果你的应用程序大量使用图像或图标，优化图像加载可能是最有利可图的。

比如:用打包工具(比如 Webpack)用 base64 内联小图；压缩图像；将多个图标文件集成到一个图像中；选择较好的图像格式，如 WebP 等。；使用 SVG 图标或使用字体图标；对不重要的图像使用延迟加载，等等。

# 5.加拿大

CDN(内容交付网络)是分布在许多位置的一组服务器。这些服务器存储数据的副本，以便将服务器内容返回给最接近最终用户的用户。这对于高流量的情况来说是一个很好的缓冲。

所以可以合理利用 CDN，比如在多个地方部署 CDN 服务器，地理距离会成比例影响延迟；我们还可以将这些静态资源放在 CDN 上，以减轻我们自己的服务器的请求负担等。

# 6.请求域分割

在浏览器中，来自同一来源的 TCP 连接的最大数量被限制为 6。这意味着，如果同时发送 6 个以上的请求时使用 HTTP1.1，第 7 个请求将等到前一个请求被处理后才开始。

所以我们可以合理划分资源，拆分他们的域名。另外，结合上面提到的 CDN，建议静态资源使用无 cookie 的域名。

# 7.HTTP 缓存

使用 HTTP 缓存可以加快获取资源的速度，它可以分为强缓存和协商缓存。详情请看我的这篇文章:

[](https://blog.devgenius.io/web-application-performance-optimization-http-caching-791eeda4509e) [## Web 应用程序性能优化— HTTP 缓存

### HTTP 缓存的最佳实践是什么？

blog.devgenius.io](https://blog.devgenius.io/web-application-performance-optimization-http-caching-791eeda4509e) 

除此之外，我们还可以使用 HTTP2 支持的服务器端推送缓存。

# 8.本地缓存

我们可以将响应内容缓存在内存中，或者存储在本地。我们可以使用浏览器提供的存储 API 来完成所有这些工作。在下面的文章中，我将描述如何使用 blobs 来本地缓存资源:

[](/how-to-use-blob-in-browser-to-cache-ee9577b77daa) [## 如何在浏览器中使用 Blob 对象进行缓存

### 缓存可以大大提高应用程序的性能

levelup.gitconnected.com](/how-to-use-blob-in-browser-to-cache-ee9577b77daa) 

# 9.避免重定向

及时清理资源，避免重定向，可以大大减少我们的请求时间。

# 10.压缩

我们可以用`gzip`、`deflate`、`br`压缩。它们是相似的，存在于`[content-encoding](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Encoding)`响应头中。以`gzip`为例，一种用于文件压缩和解压缩的文件格式。这允许较小的文件进行更快的网络传输。并且在浏览器和 web 服务器中得到广泛支持。例如，下面是 Nginx 的一个简单配置:

```
gzip on;
gzip_min_length 10k;
gzip_types text/plain application/javascript text/css text/javascript;
gzip_http_version 1.1;
gzip_vary on;
```

# 11.代码分割

我们可以利用打包工具的代码拆分来实现按需加载，比如动态导入就允许我们这么做。

# 结论

希望今天的优化点能帮到你。如果有什么我没有提到的，请在评论里分享给我。

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。