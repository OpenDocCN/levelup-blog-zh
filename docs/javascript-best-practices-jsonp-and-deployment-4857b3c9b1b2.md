# JavaScript 最佳实践— JSONP 和部署

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-jsonp-and-deployment-4857b3c9b1b2>

![](img/d33bd098f595e56089b6235a488ded06.png)

[帕万·卡万](https://unsplash.com/@pawankawan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

为了组织我们的代码，我们应该使用一些基本的设计模式。在本文中，我们将研究如何使用 JSONP 和部署策略。

# JSONP

JSONP 是带填充的 JSON。我们可以用这个代替 JSON 来绕过同域浏览器策略。

我们应该小心使用，因为我们正在从第三方网站加载数据。

使用 JSONP，我们将 JSON 封装在一个函数调用中。

JSONP 请求提供了函数名。

例如，JSONP 请求 URL 可能看起来像:

```
http://example.org/getdata?callback=myHandler
```

其中,`myHandler`是我们的应用程序中的函数名，一旦发出请求，这个函数就会被调用。

URL 被加载到 script 元素中，一旦加载完成，它将调用`myHandler`。

# 部署 JavaScript

当我们部署 JavaScript 代码时，我们需要考虑一些事情。

我们必须确保组合脚本，使它们紧凑。

空格、长名字等。都被删除并替换为更短的空格和名称。

组合脚本是我们部署之前的又一个步骤。

我们可能会失去一些缓存优势。因为包中的一个小变化而使缓存无效是不好的。

此外，我们必须为这个包提供一些版本控制模式，这样我们才不会忘记它们。

# 缩小和压缩

缩小和压缩代码很重要。包越小，用户需要下载的代码就越少。

这意味着用户可以更快地使用我们的应用程序。

那对用户来说肯定是好的。

与原始代码相比，缩小和压缩可以减少多达 50%的大小。

我们可以用 gzip 压缩来压缩我们的包。我们的 web 服务器可以配置为提供 gzipped 包，而不是纯文本。

这也将使我们的尺寸减小。

压缩平均会使我们的文件变小 70%。

通过缩小和压缩，用户只需下载原始代码大小 15%的文件。

# 过期标题

我们的缓存应该在一段时间后过期。这样用户就不用手动刷新才能看到最新的变化。

我们可以将到期头更改为我们满意的值。

如果我们将缓存设置为 long，那么我们必须在每次部署时重命名文件，这样缓存就会自动失效。

# 使用 CDN

CDN 代表内容交付网络。这些是付费服务，让我们在世界各地的不同数据中心分发文件副本。

这样，由于位置接近，文件将更快地提供给用户。

我们可以链接到 cdn 上的库文件并使用它们。

# 装载策略

如果我们在应用程序中加载文件，那么我们可以用几种方式加载它们。

对于简单的脚本，我们可以使用内联脚本:

```
<script>
  console.log("hello");
</script>
```

或者我们可以从外部资源加载它们:

```
<script src="external.js"></script>
```

我们可以使用一些属性以不同的方式加载脚本。

我们可以使用`defer`指令让我们的脚本以一种不会阻止我们的浏览器运行其他代码的方式下载。

# 脚本元素的位置

脚本元素应该作为一个包来加载，这样我们就不需要按顺序加载很多文件。

我们拥有的脚本标签越多，我们就越有可能打乱顺序。

因此，我们应该确保通过捆绑来减少脚本标签的数量。

![](img/c0f58113e12e84d3af1813d834747955.png)

照片由 [pooya ramezani](https://unsplash.com/@pooya_ramezani?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# HTTP 分块

我们也可以把我们的页面分块，这样它们就可以分块加载了。

我们可以将所有的 JavaScript 移到 head 标签中，而加载主体的其余部分可以在以后加载。

# 动态脚本元素

我们可以在脚本标签中使用`defer`或`async`属性。

`defer`异步但按顺序加载它们。

`async`异步加载它们，但可能不按顺序。

我们还可以通过编写以下代码来动态创建脚本标记:

```
const script = document.createElement("script");
script.src = "foo.js"; document.documentElement.firstChild.appendChild(script);
```

这将创建一个脚本标签，我们可以将它附加到 head 标签或其他任何地方。

# 结论

我们可以通过缩小和压缩来减小脚本包的大小。

这样，我们的代码可以少于原始代码的一半。

我们还可以使用`defer`和`async`指令来加载脚本文件。

我们可以即时创建脚本来延迟加载。

JSONP 允许我们加载 JSON，而没有相同的域限制。