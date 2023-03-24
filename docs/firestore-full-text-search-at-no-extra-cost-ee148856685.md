# Firestore 全文搜索免费

> 原文：<https://levelup.gitconnected.com/firestore-full-text-search-at-no-extra-cost-ee148856685>

![](img/27f16dbd714f2d5ab33509e8cc236a12.png)

照片由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/search?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在本文中，我将介绍一种无需额外服务就能进行全文搜索的方法。您将避免额外的成本，同时使您的用户能够进行实时的部分匹配搜索，并有一些小的限制。最后现场演示。

很多时候，你会希望你的用户能够搜索你的数据。firebase 推荐的[方法](https://firebase.google.com/docs/firestore/solutions/search)是使用像 Algolia 这样的外部服务。这种解决方案的问题是，您必须处理额外的成本和维护。

# 赞成的意见

1.  不需要外部搜索服务。
2.  不需要同步数据。
3.  可以使用 firebase 标准对搜索进行分页。
4.  适用于任何数据格式。
5.  支持部分搜索中间字符串。
6.  可以与其他查询结合使用，以构建高级过滤。
7.  超级快

# 缺点/限制

让我们继续下去，消除这些限制，这样您就可以看到这个解决方案是否符合您的需求。

1.  所有查询都以默认顺序返回。比如你不能“按价格排序”。潜在的反模式可以用来以期望的顺序返回搜索结果**只有**如果你关心排序。
2.  用户必须键入至少 3 个字符才能注册搜索。
3.  总的可搜索字符串不能超过一个相当合理的限制(建议 500，但使用您的最佳判断)。这意味着你不能拥有一篇完整的论文，也不能在里面搜索。这意味着更多的标题，描述等…
4.  搜索词必须连续。
    例如，`**“ere no”**`将匹配`**“Is there no one else”**`，但是搜索`**“there one”**`将不会返回这种实现的命中结果，因为单词是分裂的。
5.  出口成本稍微高一点，这取决于您允许搜索多大的字符串。

# 添加记录实现

下面是使用三元模型创建搜索增强记录的实现。

trigram 命令是这里的魔法。运行此命令会产生以下结果:

```
triGram('Hello World')
['hel', 'ell', 'llo', 'lo ', 'o w', ' wo', 'wor', 'orl', 'rld']
```

您创建的每条记录都将三元模型结果存储为一个对象。要进行搜索查询，需要对搜索字符串运行三元模型，然后确保搜索字符串中的所有三元模型都出现在上面的数组中。例如，对于搜索字符串`worl`，生成以下三元模型:

```
triGram('worl') => ['wor', 'orl']
```

由于在上面的三元模型图中找到了`wor` 和`orl`，我们假设这个字符串搜索字符串匹配。然而，这种匹配行为也会导致误报。

例如，如果您的数据包含`Work Orlando`，那么`worl`也会匹配，而这不是您想要的。通常用户会改进他们的搜索，所以这一点都不会困扰我。

# 如何进行搜索

既然您已经用搜索元标记了记录，那么您可能想要运行您的第一个查询。我不会在这篇文章中重复分页，因为它太长了。我将在以后的一篇关于数据通用分页的文章中继续讨论这个问题。对于您可以尝试的基本实现，请访问以下网站:

[https://demos.vizure.net/app/demos/search](https://demos.vizure.net/app/demos/search)

这里的想法非常简单。我们创建当前搜索词的三元模型，将其作为 where 子句添加到 firestore 查询中，并获得结果。

# 常见问题解答

1.  为什么不用数组来存储你的三元模型，而不是三元模型的对象呢？
    `array-contains`只会在数组中寻找一个三元组。目前没有办法[查询 firestore](https://firebase.google.com/docs/firestore/query-data/queries#array_membership) 中包含数组中指定的许多值的记录。
    `array-contains`也有[的限制](https://firebase.google.com/docs/firestore/query-data/queries#limitations_2)使得它不适合这种方法。
2.  为什么我不能搜索索引数据？
    一旦你创建了一个索引(比如按日期排序)，如果你想在那个索引上有其他的 where 子句，你必须为每个 where 子句创建一个新的索引。因此，现在用户输入的每个搜索字符串都要求您为每个三元模型生成一个索引。这不是有效的工作流。
3.  搜索长度有什么限制？
    fire base 中的索引有[限制](https://firebase.google.com/docs/firestore/query-data/index-overview#indexing_best_practices)信不信由你。你也不想让你的记录太大，这样下载会消耗太多的带宽。
4.  有序数据的反模式是什么？为了支持按日期顺序检索数据，我们必须构造对象键，以便它们按最新的第一顺序返回。这实际上违背了 firestore 关键名称的最佳实践。
    这样做的副作用是您很可能会限制文档的读/写速度。例如，你可能会开始体验到搜索结果变慢，但我无法量化到底有多慢。对我来说，这可能没问题。

# 结论

我在这里介绍了一个非常简单的解决方案，您可以用很少的开销将它添加到几乎任何项目中。它让您的用户能够搜索您的任何数据库，而不必担心在您的 firestore 数据库上添加搜索服务。