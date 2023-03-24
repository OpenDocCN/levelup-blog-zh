# PostgreSQL 中的横向特性

> 原文：<https://levelup.gitconnected.com/the-lateral-feature-in-postgresql-487126c7faab>

## 从 PostgreSQL 9.3 开始就有了

![](img/10f42ba45bd732afa39449365ea39e3d.png)

照片由来自 [Pexels](https://www.pexels.com/photo/software-engineer-standing-beside-server-racks-1181354/) 的 Christina Morillo 拍摄

首先，`LATERAL`不是 PostgreSQL 世界中的新特性。这个功能从 2013 年就有了。

那一年的 PostgreSQL 9.3 的[版本](https://www.postgresql.org/docs/9.3/release-9-3.html)包含了这个简单但漂亮的特性来增强子查询和表连接。但是尽管这个功能很棒，但它并没有被大肆宣传。

读到或偶然发现它的开发人员和数据库管理员意识到了它的便利性，并立即使用了它。

如果你不知道 PostgreSQL `LATERAL`，并且你是第一次听说它，那么继续读下去，这篇文章当然适合你。

在本文中，您将了解横向特性以及它如何增强您的 SQL 查询。

# 什么是横向？

`LATERAL`子句是一个神奇的关键字，它支持子查询和连接中的表行数据的交叉引用。

PostgreSQL 9.3 引入了对子查询、连接和函数调用中的表数据的交叉引用的支持。在此之前，试图交叉引用如下所示的数据将会抛出一条错误消息。

上面的查询显示了在`LEFT`连接子查询中对`users`表的列`id`的交叉引用。PostgreSQL 通常不支持此操作，因此会出现下面的错误消息

```
ERROR:  invalid reference to FROM-clause entry for table "u" LINE 9:  and user_id = u.id HINT:  There is an entry for table "u", but it cannot be referenced from this part of the query. SQL state: 42P01 Character: 184
```

`LATERAL`关键字是我们如何支持跨表和连接的数据交叉引用。

让我们看看如何使用`LATERAL`来解决上述查询中的交叉引用问题

# 外侧怎么用？

在具有多个表引用的查询中，`LATERAL`关键字出现在`FROM`子句之后。我们稍后会看到一个例子。

现在，在表连接的情况下，`LATERAL`关键字必须出现在我们的`JOIN`子句之后。同样的情况也适用于所有其他形式的表连接。

要修复上面失败的查询，我们只需确定放置`LATERAL`关键字的正确位置，然后我们将在`LEFT`联接子查询中启用对`users`表列的交叉引用支持。

简单的修复看起来是这样的

修复查询的结果如下所示

# 可读查询

除了支持表和连接之间的数据交叉引用，`LATERAL`还改进了查询结构和可读性。

我的意思是。比较第一个查询和第二个查询。虽然两个查询获得的结果相同，但第二个查询似乎比第一个查询结构良好，易于理解。

现在，上面使用`LATERAL`关键字的查询的一个可读性更好的版本如下所示

请注意，我们如何通过使用`LATERAL`将查询的权重推到底部，使我们清楚地看到上面从子查询的具体表`users`和派生表`last_visited`中选择的列。

关键字`LATERAL`是一个非常有用的特性，对于了解它的人来说很方便。

现在你也知道了。

*感谢您的阅读。我希望这篇文章对你有用。如果有，请花一点时间到* [*订阅*](https://medium.com/subscribe/@ofelix03) *到* [*我的个人资料*](https://medium.com/subscribe/@ofelix03) *在我每次发表新文章时接收通知。*

还有一件事，如果你能支持我的写作，我将不胜感激。通过 [*加盟媒介*](https://medium.com/membership/@ofelix03) *与我的* [*推荐链接*](https://medium.com/membership/@ofelix03) *，我从你的会员费中获得一小笔佣金，这有助于我投入时间和精力来撰写和发表这些有用的文章。*

[](https://ofelix03.medium.com/membership) [## 通过我的推荐链接加入媒体-费利克斯·奥托

### 阅读费利克斯·奥托(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

ofelix03.medium.com](https://ofelix03.medium.com/membership)