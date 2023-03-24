# MySQL WHERE 子句不等式比较运算符

> 原文：<https://levelup.gitconnected.com/mysql-where-clause-inequality-comparison-operators-d1cb6ba45e98>

正如您希望使用相等比较运算符(=)过滤在`SELECT`查询中返回的数据行一样，您也可以创建一个条件过滤器来测试两个值是否不相等。在下面的文章中了解更多。

> 当你[订阅 ***OpenLampTech*** 时事通讯](http://openlamptech.substack.com)时，就能收到一本我的电子书*《给每个人的 10 个 MySQL 技巧》*。

![](img/a781d3e9273e63ac5ec7715eda7eb423.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1013752)

在 MySQL 中，有两个“不等于”比较运算符可用:

*   `<>`
*   `!=`

📰通过在 ***OpenLampTech*** 时事通讯中投放可负担得起的分类广告，让您的品牌、产品或服务获得所需的关注。谢谢大家的支持！

尽管这两个比较操作符的意思相同，工作方式也完全相同，但我将使用`<>`操作符，因为它受到 SQL 标准的支持，也在其他 SQL 方言中使用。

通常，不等式条件比较筛选器如下所示:

```
some_column <> some_value
```

## 相似阅读

享受这些 MySQL 初学者友好的文章:

*   [MySQL WHERE 子句等式比较运算符](https://joshuaotwell.com/mysql-where-clause-equality-comparison-operator/)
*   [MySQL SELECT 和 WHERE 子句列存在](https://joshuaotwell.com/mysql-select-and-where-clause-column-existence/)
*   [SELECT 子句查询— MySQL 初学者基础系列。](https://joshuaotwell.com/select-clause-queries-mysql-beginner-basics-series/)

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问 [Portfolio-Projects 页面](https://wp.me/P28ctb-3KD)查看我为客户完成的博客帖子/技术写作。

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看[数字猫头鹰的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等……)

请务必访问我的最佳博客文章的收集页面。

[作为一名 SQL 开发人员和博客写手，Josh Otwell](https://joshuaotwell.com/about/) 热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。所提供的大多数(如果不是全部)示例都是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

***有什么可以帮你的*** ？

***披露*** :本帖部分服务和产品链接为附属链接。在没有额外费用给你，你应该通过点击其中一个购买，我会收到佣金。

当你[订阅 ***OpenLampTech*** 时事通讯](http://openlamptech.substack.com)的时候，就可以收到一本我的电子书*《给每个人的 10 个 MySQL 技巧】。*

📰通过在 ***OpenLampTech*** 时事通讯中投放价格合理的分类广告，让您的品牌、产品或服务得到应有的关注[。谢谢大家的支持！](https://ko-fi.com/s/7dfe9ce108)

*原载于 2022 年 8 月 3 日 https://joshuaotwell.com**的* [*。*](https://joshuaotwell.com/mysql-where-clause-inequality-comparison-operators/)