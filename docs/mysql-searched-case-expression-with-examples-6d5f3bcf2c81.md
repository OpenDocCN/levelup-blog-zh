# MySQL 搜索案例表达式—带示例

> 原文：<https://levelup.gitconnected.com/mysql-searched-case-expression-with-examples-6d5f3bcf2c81>

在编程代码(条件逻辑)的许多决策阶段，有时执行取决于几个不同的因素。多重条件测试功能强大且具有约束性，通常需要通过一个以上的测试才能继续程序流。对于 MySQL(以及一般的标准 SQL)来说，`CASE`表达式用于`IF` / `THEN` / `ELSE`条件逻辑。

这篇名为 [MySQL 简单的 CASE 表达式——带有示例](https://joshuaotwell.com/mysql-simple-case-expression-with-examples/)的文章涵盖了简单的`CASE`查询，这些查询本质上是等式测试。MySQL Simple `CASE`只是 2 的一个变体，另一个是 MySQL 搜索的`CASE`表达式。一个 MySQL 搜索的`CASE`表达式在每个`WHEN`子句中可以有多个条件测试。这些条件测试可以是`AND` / `OR`比较、`BETWEEN`值范围测试、子查询、`IN()`运算符等等。继续阅读，查看 MySQL 搜索到的`CASE`表达式的例子…

![](img/f9f0ed5a4a18da21e9a2f8d024807c62.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1872665)

使用的操作系统和数据库:

*   Linux 薄荷 20“乌利亚纳”
*   MySQL 8.0.24

> *自我推销:*
> 
> 如果你喜欢这里写的内容，尽一切办法，把这个博客和你最喜欢的帖子分享给其他可能从中受益或喜欢它的人。既然咖啡是我最喜欢的饮料，如果你愿意，你甚至可以给我买一杯！

正如在关于简单的`CASE`表达式的帖子中，我使用这个表和数据进行示例查询:

## MySQL 搜索的 CASE 表达式:与简单 CASE 表达式相比的语法和相似性

语法上，MySQL 搜索的`CASE`表达式类似于简单的`CASE`，除了没有列或表达式像简单的`CASE`一样紧跟在`CASE`关键字之后。除此之外，两者之间的*结构*是相同的。然而，它们远远不是一回事。关键的区别在于搜索的`CASE`允许在任何`WHEN`子句中进行多重比较。

虽然下面的示例查询是任意的，可能没有太大的意义，但是我的目标是提供一个关于如何使用搜索到的`CASE`表达式的基本理解。如果我说这些是你可以使用 Searched `CASE`的唯一方式，那将是我的失职，因为它的用途远远超出了我在这篇博文中所能涵盖的范围。

被搜索的`CASE`表达式可能比简单的`CASE`更复杂，在`WHEN`子句中有多个不同类型的条件:

在简单的`CASE`只允许相等比较的情况下，请注意在搜索的`CASE`示例中，出现了`AND`逻辑运算符，允许评估多个条件。

## MySQL 搜索案例表达式:多重条件和比较

在下一个查询中，注意在某种意义上出现了大于(>)和小于(

Consider making a small donation on my behalf as I continue to provide valuable content here on my blog. Thank you!

![](img/98545a780b73cb7dddce37f09bf95b2e.png)

You can even use the 【 operator for Searched 【 expression comparisons in any 【 clause. I have changed the above query and now included 【 instead of the 【 logical operator as before:

## MySQL Searched CASE Expression: Dynamic-like queries

Searched 【 can make queries *动态*)。或者 SQL 可以是动态的。同样，这些例子并不那么有用，但是会让你思考如何使用 Searched。在逐行的基础上，想象对于任何超过 45 岁的人，你都想在上面加 5。不管出于什么原因，也许你让你的朋友过了 50 岁的年纪！

这再容易不过了。只需在行值计算中使用 Searched `CASE`即可将适用值动态添加到“年龄”列值中:

在上面的查询结果中，请注意，对于条件表达式`age &gt; 45`为真的任何年龄值，值 5 被添加到“年龄”列值中。如果不是，返回的`CASE`表达式值没有变化，因为本质上是通过`ELSE`子句将 0(零)加到“年龄”值上，产生当前年龄列值。在这种情况下，搜索`CASE`的功能非常强大。

正如我在帖子中已经说过的，使用 Searched `CASE`提供的例子完全是随意的，可能没有*真实世界*的用途，但应该会让你了解 Searched `CASE`是如何工作的，以及你可以用它完成一些事情。

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问 [Portfolio-Projects 页面](https://wp.me/P28ctb-3KD)查看我为客户完成的博文/技术写作。

既然你这么喜欢我的工作，给我拿杯咖啡喝吧！

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等……)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

作为一名 SQL 开发人员和博客写手，Josh Otwell 有着学习和成长的热情。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。提供的大部分示例(如果不是全部的话)是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。意见是我自己的

*原载于 2021 年 5 月 12 日 https://joshuaotwell.com*[](https://joshuaotwell.com/mysql-searched-case-expression-with-examples/)**。**