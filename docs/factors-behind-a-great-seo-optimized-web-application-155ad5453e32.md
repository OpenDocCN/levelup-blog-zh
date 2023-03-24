# 伟大的 SEO 优化 Web 应用程序背后的因素

> 原文：<https://levelup.gitconnected.com/factors-behind-a-great-seo-optimized-web-application-155ad5453e32>

## 了解哪些因素决定了应用程序的 SEO 性能

![](img/e1f9fa3d4af9d08172f46f05399a33c2.png)

[Firmbee.com](https://unsplash.com/@firmbee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/search-engine?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

你是一名网络开发人员，你建立了一个很棒的网站，向世界展示一些东西。

但是很遗憾，没人来你的网站:/。如今，当人们在网上冲浪时，他们非常依赖像谷歌这样的搜索引擎来找到一些东西。

而且很多时候，人们只关心排名靠前的搜索结果。所以如果你的网站不在谷歌的第一页，那么没有人会注意到你。有人说

> 隐藏尸体的最佳地点是谷歌搜索结果的第二页

所以今天，我们将看看哪些因素影响你的网站在谷歌搜索引擎上的排名。

# 基础

当你把你的网站放到网上后，没有人会发现它。首先，你必须在谷歌的搜索控制台上注册你的网站。

[](https://search.google.com/) [## 谷歌

### 搜索世界信息，包括网页、图像、视频等。谷歌有许多特殊的功能来帮助…

search.google.com](https://search.google.com/) 

然后谷歌就会意识到你网站的存在。一段时间后，一个机器人(名为 GoogleBot)将访问您的网站，收集各种页面的信息。

在信息被收集后，谷歌将使用其臭名昭著的算法根据各种指标来确定你网站的排名。

所以你有责任让谷歌容易地找到你的网站是关于什么的。

# 图像的 Alt 属性

所以你有一个优秀的网站，有很多吸引用户的精彩图片。你非常确定你的用户会喜欢这些。

```
<img src = "some_url" />
```

但是谷歌机器人根本不在乎你的图像，因为它们根本看不到。那你是做什么的？

您必须为每个图像标签提供一个`alt`属性。它应该解释图像是怎么回事。

```
<img src="some_url" alt ="Profile photo of Mohammad Faisal" />
```

因此，当谷歌机器人来到你的页面时，它们会读取`alt`属性，并试图理解你网站的内容。

# 正确的元标签

一个 SEO 友好网站的另一个重要部分是使用合适的`meta`标签。那么什么是元标签呢？

有时你的页面内容不足以理解整个画面。例如，如果你的页面是关于一首歌的，你应该把关于作者、演出、价格等附加信息放在哪里？？

或者，你如何确保当你的页面在社交媒体上被分享时，它会有正确的预览？

Meta 标签是一种简洁的方式来总结你的网页内容并提供一些额外的信息。

例如，`NextJS`您可以像下面这样添加元标签

索引。TSX

因此，当谷歌机器人来到你的页面时，他们会试图阅读这些元标签，以了解更多关于页面内容的信息。

# 表演

另一个关键指标是应用程序的性能。这意味着你的页面加载的速度有多快。因为 google bot 等待页面响应的时间有特定的限制。

因此，如果你的网站超级慢，那么谷歌机器人会在索引任何东西之前离开页面，这对你来说不是好消息。

你可以免费(大部分)测量你的网站的性能

[](https://javascript.plainenglish.io/6-free-websites-to-test-the-performance-of-your-web-application-e649f92cf978) [## 6 个免费网站来测试您的 Web 应用程序的性能

### 发现问题并免费学习最佳实践！

javascript.plainenglish.io](https://javascript.plainenglish.io/6-free-websites-to-test-the-performance-of-your-web-application-e649f92cf978) 

所以在把你的网站放到网上之前，先测试一下，看看网站是否有明显的问题。

# 移动友好性

移动友好是搜索引擎优化的另一个关键因素。因为大多数人在搜索时都使用移动设备。

这就是为什么谷歌采取了[移动优先的索引](https://developers.google.com/search/blog/2016/11/mobile-first-indexing)策略来为用户提供更好的结果。

你可以通过下面的链接检查你的网站是否适合手机。只需将链接粘贴到任何页面上，Google 就会为您生成一个结果。

 [## 手机友好测试-谷歌搜索控制台

### 你的网页对手机友好吗？测试访问者在移动设备上使用您的页面的难易程度。只需输入一个页面 URL…

search.google.com](https://search.google.com/test/mobile-friendly) 

在把你的网站公之于众之前，一定要检查一下。

# SSL 证书

如今，安全性是一个大问题。人们对安全有了更多的认识和了解。而且谷歌在索引任何网站的时候也是很认真的。

```
[http://yourwebsite.com](http://yourwebsite.com).  <---- insecure[https://yourwebsite.com](https://yourwebsite.com)  <----- secure
```

注意在 URL 的开头如果有`https`，那么它的意思就是`http secure`。所以你应该总是在`https`上提供你的内容

提高网站安全性的一个简单方法是为您的网站使用 SSL 证书。一些免费服务也可以做到这一点(如果你不想花钱的话)，比如`Let’s Encrypt`就是一个出色的免费服务。

[](https://letsencrypt.org/) [## 让我们加密

### 让我们加密是一个免费的，自动化的，开放的认证机构，由非营利性的互联网安全…

letsencrypt.org](https://letsencrypt.org/) 

警告是证书必须每三个月更新一次。但是这个过程很简单。

所以先对你的网站进行安全分析。

# 实际内容

我把这作为最后一点，因为无论你用元标签或图片做什么，它都是最重要的内容，所以你必须在你的页面上有好的有价值的内容，这样人们才会来你的网站。

否则，其他一切都没有意义。但不幸的是，我不能告诉你在你的网站上放什么。这取决于你:P

# 最后的想法

搜索引擎优化是你的网站非常重要的一部分，所以你不能忽视它。这些是您可以用来提高应用程序 SEO 性能的一些主要工具。但你应该深入挖掘。

祝您愉快！:D

**通过**[**LinkedIn**与我联系](https://www.linkedin.com/in/56faisal/)