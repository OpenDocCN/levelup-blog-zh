# 我如何使用 MongoDB 触发器

> 原文：<https://levelup.gitconnected.com/how-i-use-mongodb-triggers-1bc466ddb48e>

![](img/cc3e82d8a0929f4028ba51824d94f266.png)

在这篇文章中，我将展示我在我的 [MongoDB](https://www.mongodb.com/) 触发器中使用的函数。我在我的许多个人项目中使用 MongoDB，由于它们主要用于测试和组合目的，我需要一种方法，通过只留下我想要的数据来每天重置 DB。 [MongoDB 触发器](https://docs.mongodb.com/realm/triggers/)在这方面非常出色，你可以通过遵循[这个指南](https://docs.mongodb.com/realm/triggers/)在你的 MongoDB 账户上轻松设置它们，这里我就不重复了。

这里我想展示我在触发器中使用的函数，因为我花了一段时间才想出一个可行的解决方案，所以我希望我能帮助其他可能遇到我同样问题的人。

上面的代码是一个[预定触发器](https://docs.mongodb.com/realm/triggers/scheduled-triggers/)的函数。该函数首先在 DB 中找到我想要保留的用户，名为 *testuser* (第 4 行)，然后利用 user **_id** 删除不属于 *testuser* 的博客(第 13 行)。注意第 13 行使用了 [**$ne**](https://docs.mongodb.com/manual/reference/operator/query/ne/) ，代表“不相等”。重复相同的逻辑来删除不需要的注释(第 28 行)。之后，为了从博客文档(存储在 blog.comments 数组中)中删除不需要的评论引用，我首先获取所有左边的博客文档(第 24 行)和所有左边的评论文档(第 25 行)，然后我将返回的 [FindCursors](https://mongodb.github.io/node-mongodb-native/4.0//classes/findcursor.html) 保存为数组(第 26 a & 27 行)，只过滤博客文档中现有评论的评论引用(第 30 行)，最后[更新](https://docs.mongodb.com/drivers/node/current/usage-examples/updateOne/)博客文档(第 31–35 行)。