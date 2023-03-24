# MySQL WHERE 子句相等比较运算符

> 原文：<https://levelup.gitconnected.com/mysql-where-clause-equality-comparison-operator-f8f182fd3231>

我们并不总是需要表中的所有行作为最终结果集的一部分。您可以使用一个(或多个)比较操作符来过滤带有`WHERE`子句条件的行。在这篇文章中，我们来看看等式比较运算符(=)…

当你[订阅 ***OpenLampTech*** 时事通讯](http://openlamptech.substack.com)时，就能收到一本我的电子书*《给每个人的 10 个 MySQL 技巧》*。

![](img/e861e28fbc5a306f14159a3a5f82e9f1.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1207270) 的 [kalhh](https://pixabay.com/users/kalhh-86169/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1207270)

## 相等比较运算符

要检查任何列值是否等于其他值，可以使用相等比较运算符(=)。

这通常(但不总是)是以下形式:

```
column_name = some_value
```

## 推荐阅读

请访问这些文章，了解有关 MySQL 的更多信息:

*   [SELECT 子句查询— MySQL 初学者基础系列。](https://joshuaotwell.com/select-clause-queries-mysql-beginner-basics-series/)
*   [用 WHERE 子句限制行数— MySQL 初学者系列](https://joshuaotwell.com/limit-rows-with-the-where-clause-mysql-beginner-series/)

请务必订阅博客，获取更多关于 MySQL 的文章。感谢您的阅读。

在 ***OpenLampTech*** 时事通讯中[投放可负担得起的分类广告，让您的品牌、产品或服务得到应有的关注。谢谢大家的支持！](https://ko-fi.com/s/7dfe9ce108)

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问[投资组合-项目页面](https://wp.me/P28ctb-3KD)查看我为客户完成的博客帖子/技术写作。

我喜欢一杯热咖啡！

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

[作为一名 SQL 开发人员和博客写手，Josh Otwell](https://joshuaotwell.com/about/) 热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。所提供的大多数(如果不是全部)示例都是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

***有什么可以帮你的*** ？

*   免费 [MySQL 查询语法口头禅 PDF](https://ko-fi.com/s/3631fc7d00) 备忘单。记住这个咒语的查询语法顺序。
*   你想开一个博客吗？我用 WordPress 写博客。让我们都在提供的计划上省钱。
*   通过在 ***OpenLampTech*** 时事通讯中投放可负担得起的分类广告，让您的品牌、产品或服务获得其所需的关注度。
*   需要托管你的下一个网络应用程序或 WordPress 网站吗？我使用并强烈推荐 [Hostinger](https://www.hostg.xyz/aff_c?offer_id=6&aff_id=94641) 。他们有很好的价格和服务。
*   作为一名自学成才的开发人员，我逐渐认识到的 5 个事实
*   今天就在我的 [Kofi 商店](https://ko-fi.com/joshlovescoffee#)发现优质的 MySQL 学习资料吧！

***披露*** :本帖部分服务和产品链接为附属链接。在没有额外费用给你，你应该通过点击其中一个购买，我会收到佣金。

当您[订阅 ***OpenLampTech*** 时事通讯](http://openlamptech.substack.com)时，将收到一本我的电子书*《给每个人的 10 个 MySQL 技巧】****绝对免费*** 。

通过在 ***OpenLampTech*** 时事通讯中投放可负担得起的分类广告，让您的品牌、产品或服务获得所需的关注。谢谢大家的支持！

*原载于 2022 年 6 月 15 日 https://joshuaotwell.com*[](https://joshuaotwell.com/mysql-where-clause-equality-comparison-operator/)**。**