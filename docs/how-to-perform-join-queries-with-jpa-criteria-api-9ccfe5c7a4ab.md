# 如何使用 JPA Criteria API 执行连接查询

> 原文：<https://levelup.gitconnected.com/how-to-perform-join-queries-with-jpa-criteria-api-9ccfe5c7a4ab>

## 在 Spring Boot 的条件连接查询中选择多个实体

![](img/51375635c1328deec23960e2d963b2c6.png)

照片由 [Tirza van Dijk](https://unsplash.com/@tirzavandijk?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JPA 标准查询基于 JPA [标准 API](https://docs.jboss.org/hibernate/orm/3.2/api/org/hibernate/Criteria.html) ，它允许您在 Spring Boot 中构建类型安全的查询。如果您将它们与 JPA 元模型类一起使用，这将变得更加容易，JPA 元模型类可以自动生成[。](https://codeburst.io/criteria-queries-and-jpa-metamodel-with-spring-boot-and-kotlin-9c82be54d626)

JPA Criteria API 提供了几个特性，但是也有一些缺陷。然而，在几个连接中使用 JPA 标准查询有点棘手。特别是，如果您必须执行多个连接，并希望选择多个实体。

因此，让我们学习一下如何使用 JPA Criteria API 执行`JOIN`查询的所有知识。

# 语境

让我们考虑以下场景:

*   `Author`实体与`Book`实体有多对多的关联。详细来说，作者实体的`books`属性映射了这个关联。类似地，`Book`实体也有一个`authors`属性。
*   `Book`实体与流派实体有多对多的关联。详细地说，`Book`实体的`genres`属性映射了这个关联。类似地，`Genre`实体也有一个`books`属性。

所以，一个作者有很多书，每本书都有很多体裁。或者换句话说，一本书有很多作者，很多体裁。

# 使用 JPA 标准 API 的多连接查询

假设您想要检索由特定作者撰写的特定流派的所有书籍。您可以通过以下标准查询获得它们:

如您所见，JPA `[Join](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/Join.html)`类允许您用 Criteria API 定义连接查询。注意，`join()`方法也可以接受一个`[JoinType](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/JoinType.html)`参数。

使用`JoinType`指定想要执行的连接类型。可能的值有:

*   `[INNER](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/JoinType.html#INNER)`:用于内部连接。

```
// performing an INNER JOIN
root.join("authors", JoinType.INNER);
```

*   `[LEFT](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/JoinType.html#LEFT)`:用于左外连接。

```
// performing a LEFT JOIN
root.join("authors", JoinType.LEFT);
```

*   `[RIGHT](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/JoinType.html#RIGHT)`:用于右外连接。

```
// performing a RIGHT JOIN
root.join("authors", JoinType.RIGHT);
```

默认情况下，`join()`方法执行内部连接。

# 使用 JPA Criteria API 在连接查询中选择多个实体

现在，让我们来看看如何检索由特定作者所写的特定类型的所有书籍的书籍和类型信息。您可以通过以下标准查询来实现这一点:

在这种情况下，您必须将`[CriteriaQuery](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/CriteriaQuery.html)`初始化为`[Tuple](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/Tuple.html)`查询。这个特殊的数据结构允许您检索一个`[multiselect()](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/criteria/CriteriaQuery.html#multiselect(java.util.List))`查询的结果。

具体来说，您可以从`bookGenreTuples`列表的一个元素中提取`Book`和`Genre`对象，如下所示:

注意，产生的`Tuple`存储实体对象的顺序与它们在上面的`multiselect()`方法中指定的顺序相同。

瞧啊！您将在 Spring Boot 学习如何使用 JPA Criteria API 执行连接查询！

# 结论

在本文中，您学习了如何编写 JPA 标准查询，该查询涉及许多连接子句并选择多个实体。如果没有 JPA 标准 API，所有这些都是不可能的。

在这里，您学习了如何使用 JPA Criteria API 在 Spring Boot 中定义简单的连接查询，以及使用多选逻辑定义更复杂的查询。这可能有点棘手，通过这篇文章，您学会了如何掌握 JPA 中的条件连接查询。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。