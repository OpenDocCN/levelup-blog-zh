# MySQL 简单的 CASE 表达式和例子

> 原文：<https://levelup.gitconnected.com/mysql-simple-case-expression-with-examples-5ad43a7b4c57>

编程逻辑是任何应用程序或软件的基础。没有它，软件不会真正做任何事情。一切都是出于选择。最终，一些真实的或虚假的价值会让事情运转起来。对于标准 SQL 中的`IF` / `THEN` / `ELSE`逻辑，有`CASE`表达式。`CASE`表达式有两种变体:简单和搜索。在这篇文章中，我用示例查询介绍了简单的 MySQL `CASE`表达式。

![](img/20cf7910f5730d6a09406b1848cfcf40.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4567589) 的[卡尼·阿金](https://pixabay.com/users/nika_akin-13521770/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4567589)

使用的操作系统和数据库:

*   Linux 薄荷 20“乌利亚纳”
*   MySQL 8.0.23

自我推销:

如果你喜欢这里写的内容，尽一切办法，把这个博客和你最喜欢的帖子分享给其他可能从中受益或喜欢它的人。既然咖啡是我最喜欢的饮料，如果你愿意，你甚至可以给我买一杯！

# MySQL 简单 CASE 表达式是如何工作的？

一个简单的`CASE`表达式本质上是一个*等式测试*。紧跟在`CASE`关键字之后指定一个列或表达式，然后指定一系列`WHEN` / `THEN`子句。在每个`WHEN` / `THEN`子句中，根据`CASE`后面的列或表达式测试指定的值。当`WHEN`子句中列出的值与简单的`CASE`比较目标匹配时，`THEN`子句部分维护返回的值。

我有一个“朋友”表，其中有一些练习数据，我将在示例查询中使用:

# 简单的 CASE 表达式示例查询

假设对于每个“county”列的缩写，我想显示一个与美国相关的描述性方向(我的国家和位置)。使用简单的`CASE`，这相当容易:

(**信息:**使用`AS`关键字别名化`CASE`表达式是可选的。)

虽然在“国家”列中存在 4 个不同的值，但实际上只有 3 个在`CASE`表达式中有一个`WHEN` / `THEN`子句。通过将“USA”、“CAN”和“MEX”与“country”列进行比较，与这三个值中的任何一个都不匹配的任何其他行都将返回`ELSE`子句中的表达式，“Not a neighboring country”。

# MySQL 简单的 CASE 表达式:ELSE 子句

您可以在`CASE`表达式中省略可选的`ELSE`子句。任何没有匹配值的`WHEN` / `THEN`子句都返回`NULL`:

# MySQL 简单 CASE 表达式:CASE 匹配第一个真表达式

MySQL 简单的`CASE`表达式匹配第一个表达式`TRUE`。如下一个查询所示，对于“CAN”有 2 个可能的`THEN`子句返回值:“Canadian”和“Canada”。请注意，在查询结果中，由于“CAN”值是评估为`TRUE`的第一个匹配项，因此返回“Canadian ”:

有关更多信息，请访问关于[案例表达式](https://dev.mysql.com/doc/refman/8.0/en/flow-control-functions.html)的官方在线文档。

我在这篇文章的开头部分提到有两种形式的`CASE`表达。搜索到的`CASE`表达式*变体*比它的简单版本对应物更强大。我计划在 Searched `CASE`上发布一篇后续博文，所以请务必订阅我的博客，以便在博文发布时收到通知。感谢阅读！！！

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问[投资组合-项目页面](https://wp.me/P28ctb-3KD)查看我为客户完成的博文/技术写作。

[**浓热的咖啡是我绝对喜欢的饮料！**](https://ko-fi.com/joshlovescoffee)

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

[作为一名 SQL 开发人员和博客写手，Josh Otwell](https://joshuaotwell.com/about/) 热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。提供的大部分示例(如果不是全部的话)是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

*原载于 2021 年 4 月 28 日 https://joshuaotwell.com**[*。*](https://joshuaotwell.com/mysql-simple-case-expression-with-examples/)*