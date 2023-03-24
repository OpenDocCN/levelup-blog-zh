# 修复模糊错误:Apache、GZip、ETags 和 Edge Compute

> 原文：<https://levelup.gitconnected.com/fixing-obscure-bugs-apache-gzip-etags-edge-compute-7b5fbb63a57c>

![](img/5394b86f896794874f23ebe75b49917a.png)

最近有人向我介绍了 Apache 的 GZip 模块的一个模糊的性能错误，以及一个使用 [EdgeWorkers](https://www.akamai.com/solutions/edge?utm_source=austingil.com&utm_medium=blog&utm_campaign=austin-gil) 的巧妙解决方案。所以我做了一个视频，还发了一个小博文来介绍它:

我喜欢我的工作的一点是，当我和比我聪明得多的人一起工作时，我从来没有兴趣或机会去做的事情。

例如，虽然 [Apache](https://httpd.apache.org/docs/) 可以说是这个星球上最流行的 HTTP 服务器技术，但它也不是没有缺陷。如果你回顾一下[错误报告](https://bz.apache.org/bugzilla/)，你可能会发现一个叫做“gzip:ed content 上[错误的 ETag”的错误报告，这是亨德里克·诺德斯特龙在 2006 年报告的。](https://bz.apache.org/bugzilla/show_bug.cgi?id=39727)

亨德里克注意到，使用`[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)`模块压缩的实体仍然携带与普通实体相同的 [ETag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag) ，这导致了与 ETag 感知代理缓存的某种不一致。

如果您不熟悉的话，ETags 是 HTTP 响应头，主要用于标识资源的特定版本。ETag 的值通常是一个哈希值，由文件内容或修改日期或者两者的组合生成。

它可能看起来像这样:

```
ETag: 27dc5-556d73fd7fa43
```

如果一个 HTTP 请求包含一个与现有 ETag 响应头匹配的`[If-None-Match](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-None-Match)`头，代理缓存(如 CDN)可以认为文件已经更改但未被修改，并可能以 304“未修改”状态响应。

理论上，这样会更快并节省带宽。

当您在 Apache 中启用 GZip 压缩模块时，它实际上会在这些为 ETags 生成的哈希值的末尾附加一个小小的“-gzip”后缀。

不幸的是，当 Apache 去比较`If-None-Match`头(" 27dc5-556d73fd7fa43-gzip ")和 ETag 值(" 27dc5-556d73fd7fa43 ")时，它没有考虑后缀。

因此，它总是要服务于整个资源负载，这是具有讽刺意味的，因为理论上 ETags 和 GZip 都是节省带宽的方法。

我希望我能够清楚地解释这个问题，但如果我没有，在 [Flameeyes 的博客](https://flameeyes.blog/)上有一篇题为“ [Apache，ETag 和‘未修改’](https://flameeyes.blog/2017/08/22/apache-etag-and-not-modified/)”的精彩博文。它甚至提供了解决方案。

那么，如果问题已经解决了，为什么我今天要谈这个？

这个解决方案要求您修改 Apache 配置。但是作为现在的开发人员，我非常清楚在很多情况下我们自己无法做到这一点。我可能没有权限，或者我可能正在使用第三方服务。

有趣的解决方案出现了。

[Akamai](https://www.akamai.com/) 有一款产品叫做 [EdgeWorkers](https://www.akamai.com/solutions/edge) ，它基本上可以坐在客户端和源服务器之间，运行 [JavaScript](https://austingil.com/category/javascript/) 逻辑。

因此，当客户端发出请求时，EdgeWorker 可以拦截请求，并在请求到达源服务器的途中对其进行修改。或者当源服务器做出响应时，Akamai EdgeWorker 可以拦截该响应，并在它被传回客户端之前对其进行修改。

理论上，不能访问 Apache 配置的开发人员仍然可以打临时补丁，直到解决方案在 Apache 配置级别得到解决。

它可能看起来像这样:

```
// Modify origin responses
export function onOriginResponse(request, response) {
  // Grab the server name
  const serverName = response.getHeader('Server')[0];
  // Grab the ETag header
  const etag1 = response.getHeader('ETag')[0];
  // Check if it's an Apache server and the ETag ends with '-gzip'
  if (serverName.startsWith('Apache') && etag1.endsWith('-gzip')) {
    // If so, strip out the '-gzip' suffix
    response.setHeader('ETag', etag1.replace('-gzip', ''));
  }
}
```

我意识到这可能实际上不适用于很多人，但我想分享它，因为它在模糊的错误、深厚的技术知识和需要修复的几行代码之间取得了良好的平衡。特别是当你包括 Akamai EdgeWorkers 部分时，这只是一个我以前从未想到过的很酷、很优雅的方法。

所以希望你也觉得它有趣，即使它与你今天没有直接关系。

非常感谢您的阅读。如果你喜欢这篇文章，请[分享它](https://twitter.com/share?via=heyAustinGil)。这是支持我的最好方式之一。你也可以[注册我的时事通讯](https://austingil.com/newsletter/)或者[在 Twitter 上关注我](https://twitter.com/heyAustinGil)如果你想知道什么时候有新文章发表。

*原载于*[](https://austingil.com/apache-gzip-etags-edge-compute/)**。**

# *分级编码*

*感谢您成为我们社区的一员！在你离开之前:*

*   *👏为故事鼓掌，跟着作者走👉*
*   *📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容*
*   *🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)*

*🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)*