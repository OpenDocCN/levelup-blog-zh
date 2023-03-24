# MongoDB:处理来自异步事件的并发更新

> 原文：<https://levelup.gitconnected.com/mongodb-handling-parallel-writes-fa7744565313>

## 当新数据以异步和无序方式进入时，为每个属性使用时间戳来处理对相同属性的并发更新

![](img/eebc76e2c6675a035fcaf77b6d04dd06.png)

照片由[努尔·尤尼斯](https://unsplash.com/@nooryounis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文 [MongoDB:使用原子操作维护数据完整性](/mongodb-maintain-data-integrity-with-atomic-operations-7c40d505b41e)中，我们了解了 [$set](https://docs.mongodb.com/manual/reference/operator/update/set/) 、 [$setOnInsert](https://docs.mongodb.com/manual/reference/operator/update/setOnInsert/) 、 [$push](https://docs.mongodb.com/manual/reference/operator/update/push/#up._S_push) 、 [$pull](https://docs.mongodb.com/manual/reference/operator/update/pull/#up._S_pull) 和 [$inc](https://docs.mongodb.com/manual/reference/operator/update/inc/) 操作符，并了解了在处理单个文档的并行更新时，它们对于确保数据的准确性至关重要。

上面的操作符和其他类似的原子操作符足以处理这样的场景，但是只有当我们为每个更新设置不同的属性时，如下所示:

展示了 MongoDB 中的文档在检索整个文档、修改和保存它时如何变得不正确。

但是仅仅这些操作符还不足以满足我的团队的需求，我们可能会并行地、无序地处理对相同属性的更新。这可能会导致在源中较早产生的数据覆盖我们用较晚的数据执行的更新。

## 我们如何决定哪些更新应该进入我们的 MongoDB 文档？

答案是，我们为在文档中设置的每个属性存储一个`**last_modified**`时间戳。这里的技巧是时间戳必须来自更新数据的**源**，因为服务器时间是我们*处理**消息的时间，而不是它产生的时间。*

*这方面的一个例子如下:*

*将“a”的值设置为 4，但也存储发布通知时来自源的时间戳*

*使用这种方法，根据数据的直接来源，我们现在知道我们编写的每个属性最后更新的时间。*

## *现在我们知道了我们编写的每个属性最后一次修改的时间，这对我们有什么帮助呢？*

*很高兴你问了。那么，我们可以使用这些信息来确定我们想要丢弃哪些更新(因为已经保存了相同属性的较新值)，以及我们可以安全地用哪些更新覆盖现有数据。*

*看一看以下内容:*

*展示了如何使用时间戳来选择只更新具有旧属性值的文档*

*注意我们是如何试图找到一个文档，或者没有我们想要设置的属性，或者有属性但是它的`**last_modified**` 时间早于我们当前的更新？*

*如果我们找不到这样的文档，我们的更新永远不会被应用。这意味着我们只将**最近的更新写入我们的属性:***

*显示我们将不更新属性“a ”,因为时间戳比“a”的当前 last_modified 值旧*

## *时间戳拯救行动*

*正如我们所看到的，这种方法是处理 MongoDB 文档中相同属性的并发写入的一种方便而强大的方式。利用这一点，我们可以确保只有后来对属性的更新才被写入 DB。其他更新都扔了。*

*![](img/b99e762774e5800cd290ea3364198826.png)*

## *一个问题:源系统上的时间变化*

*我们需要为源系统上的时间变化做好准备，如果发生这种情况，我们的时间戳就会失效。这可以通过使用另一个原子操作符 [**$unset**](https://docs.mongodb.com/manual/reference/operator/update/unset/) 来完成。以下查询演示了这一点:*

*当源系统的时间发生变化时，从文档中删除最后修改的属性*

*因此，现在我们不再搜索小于或等于通知发布时间的时间戳，而是需要搜索不存在于`last_modified`中的属性:*

*展示了如何选择仅更新 last_modified 较旧或不存在的文档*

## *最后一件事—检索时省略 last_modified*

*我们可能不想在检索对象时返回`last_modified`属性。这可以通过简单地使用以下查询来省略:*

*我们可以使用以下查询省略 last_modified*

*目前就这些。希望您和我们一样觉得这很有用。*

*干杯！*

## *参考*

1.  *MongoDB [更新操作符](https://docs.mongodb.com/manual/reference/operator/update/)*
2.  *MongoDB[$未设置](https://docs.mongodb.com/manual/reference/operator/update/unset/)*
3.  *MongoDB [投影](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#with-a-projection)*