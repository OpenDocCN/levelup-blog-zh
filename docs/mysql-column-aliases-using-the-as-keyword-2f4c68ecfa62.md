# 使用 AS 关键字的 MySQL 列别名

> 原文：<https://levelup.gitconnected.com/mysql-column-aliases-using-the-as-keyword-2f4c68ecfa62>

无论是运行报告还是以其他可视化方式显示数据，SQL `SELECT`列表达式都应该是有意义和可理解的。为了提供那些有价值的查询结果，SQL 开发人员使用大量可用的函数、*相邻的*列，或者其他对最终用户来说不明显的方法。尽管如此，就可读性而言，列名通常是最糟糕的，因为它采用了长的函数调用名或其他组合表达式。但是，幸运的是，有一个简单的解决方法，那就是使用`AS`关键字来混淆列。尽管`AS`是可选的——在这个特定的上下文中——我宁可选择可读性，在别名`SELECT`列表达式时使用它。

![](img/c6a34f83cf5e8bf949018e30f6575362.png)

来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=441078) 的 [Settergren](https://pixabay.com/users/settergren-345118/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=441078) 的图像

使用的操作系统和数据库:

*   Linux 薄荷 20“乌利亚纳”
*   MySQL 8.0.23

自我推销:

如果你喜欢这里写的内容，尽一切办法，把这个博客和你最喜欢的帖子分享给其他可能从中受益或喜欢它的人。既然咖啡是我最喜欢的饮料，如果你愿意，你甚至可以给我买一杯！

对于本帖中的例子，我将使用具有以下结构的“朋友”表:

您可以想象，一个常见的请求可能涉及将' first_name '和' last_name '列显示到一个*全名*类型的输出中。在 MySQL 中一点都不难。使用嵌套的`CONCAT()`函数调用，查询结果如下所示:

你能看看那个列名吗？显示在报告中，用户可能会晕头转向。对此有什么办法呢？使用`AS`关键字并将该列别名为更合适的或显示友好的名称:

好多了不是吗？

说实话，你甚至可以完全省略`AS`关键字，得到同样的结果:

![](img/9dce1516002838ce4df9971fc37b4e98.png)

## 陷阱和额外内容

明确一点，你不能这么做:

`SELECT`当`WHERE`子句执行时，列和表达式尚不可用，因此出现“where 子句中未知的列全名”的错误消息。

但是，您可以在`ORDER BY`子句中使用列别名:

下一次您的`SELECT`列表列名需要更好地命名时，请用(或不用)关键字`AS`作为它们的别名。

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问[投资组合-项目页面](https://wp.me/P28ctb-3KD)，查看我为客户完成的博文/技术写作。

[**我爱喝热咖啡！**](https://ko-fi.com/joshlovescoffee)

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等……)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

[Josh Otwell](https://joshuaotwell.com/about/) 作为一名 SQL 开发人员和博客作者，他热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。所提供的大多数(如果不是全部)示例都是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

*原载于 2021 年 4 月 21 日 https://joshuaotwell.com*[](https://joshuaotwell.com/mysql-column-aliases-using-the-as-keyword/)**。**