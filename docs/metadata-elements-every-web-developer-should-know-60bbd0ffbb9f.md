# 每个 Web 开发人员都应该知道的元数据元素

> 原文：<https://levelup.gitconnected.com/metadata-elements-every-web-developer-should-know-60bbd0ffbb9f>

## 它有什么魔力？

![](img/5d7e21a3c926ba3464e8a997720a3d45.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为 HTML 的“元数据”，`<meta>`标签经常被开发者忽略。本文为您详细介绍。

# 什么是`<meta>`？

`<meta>` HTML 元素表示不能由其他 HTML 元相关元素表示的[元数据](https://developer.mozilla.org/en-US/docs/Glossary/Metadata)，如`<title>`、`<link>`或`<script>`。

虽然它没有显示在网页上，但它也为 web 应用程序提供了许多有用的功能。比如 SEO、移动视口设置、间隔刷新等。

所以下面我就介绍三种常见的`<meta>`类型:

# 1.`charset`属性

该属性声明文档的字符编码。它的唯一有效值是不区分大小写的字符串**“utf-8”**。并且`<meta>`元素必须在文档的前 1024 个字节内。

```
<meta charset="utf-8">
```

# 2.`name`和`content`属性

该属性以名称-值对的形式提供文档元数据。

以下是一些实际例子:

# 3.`http-equiv`和`content`属性

定义 pragma 指令。该属性被命名为`http-equiv(alent)`，因为所有允许的值都是特定 HTTP 头的名称。

以下是一些实际例子:

这里我用`<meta http-equiv="Refresh" content="5" />`做了一个有趣的演示。它可以在`5s`之后自动刷新页面，不需要使用任何 JavaScript，这在某些场景下很有用。

今天就到这里。我是 Zachary，我将继续输出与 web 开发相关的故事。如果你喜欢这样的故事，想支持我，请考虑成为 [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。