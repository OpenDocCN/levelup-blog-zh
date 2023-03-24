# 使用 JavaScript 自动转换到本地时间客户端

> 原文：<https://levelup.gitconnected.com/automatic-conversion-to-local-time-client-side-with-javascript-1b7c27e8b6f2>

![](img/eab9e1aaab3fd32c2fd3c1cc89cbbfe8.png)

这一次我们来个速战速决。只是我越来越多地用来提高网站可用性的一小段 JavaScript 代码。

HTML 5 的`<time>`标签是对该语言的一个更好的补充。在一片毫无意义的冗余和概念的海洋中，它是为数不多的闪亮宝石之一。

最重要的是包含了`datetime`属性，它允许您为机器解析指定一个合适的完整时间，同时内容可以更加“用户友好”。

```
<time datetime="2020-03-27T15:44:00.000Z">
 27 March 2020 07:44 PST
</time>
```

但是，如果我们能让这个标签的内容根据用户的当地时间自动调整，那不是很好吗？而不需要他们报账或宣布当地时间？听起来不太可能，对吧？

多亏了那个`datetime`属性和`Date`对象的`toLocaleString`方法，我们才能做到！

# JavaScript 拯救世界

通常你会听到我抱怨 JavaScript 的使用，以及 HTML 和 CSS 如何在没有它的情况下做这么多事情。但是我的意思并不是说 JavaScript 是邪恶的，或者不应该被使用，而是把它放在你的裤子里，在需要的时候使用它，并增强用户体验。它不应该成为你解决所有问题的方法。

正如我在上一篇文章中概述的，好的 JavaScript 应该增强已经工作的页面，而不是提供功能的唯一手段。就在这里，我们有一个很好的例子。该页面是可用的，并且向用户呈现有用的信息。我们所说的只是调整时间标签的内容以反映当地时间。

这很简单:

```
for (let time of document.getElementsByTagName('time')) {
  time.textContent = new Date(
    time.getAttribute('datetime')
  ).toLocaleString();
}
```

在你的

# 现场演示

这里有一个简单的例子:

# 未来

随着 ECMAScript 2021 最终带来了正确的日期格式，我们可能很快就能够对输出进行更多的格式化和调整。当准备好用于生产时，我可能会使用一个可选的数据属性来声明输出的格式。