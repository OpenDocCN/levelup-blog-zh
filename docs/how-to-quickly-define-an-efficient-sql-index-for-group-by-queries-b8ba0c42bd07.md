# 如何为 GROUP BY 查询快速定义高效的 SQL 索引

> 原文：<https://levelup.gitconnected.com/how-to-quickly-define-an-efficient-sql-index-for-group-by-queries-b8ba0c42bd07>

## `GROUP BY`查询的性能提高了 10 倍

![](img/c1ee23a78d49ded86426e0883b720526.png)

克里斯·利维拉尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`[GROUP BY](https://en.wikipedia.org/wiki/Group_by_(SQL))`查询允许您根据一个或多个列的值将一个表划分成组。它的目的是让您轻松地在数据库级别检索聚合结果，通常与 SQL 聚合函数结合使用，如`COUNT()`、`MAX()`、`MIN()`、`SUM()`或`AVG()`。特别是，请阅读[这篇关于 SQL 如何聚合函数来提高后端性能的](https://arctype.com/blog/sql-aggregate-functions/)真实案例文章。

[](https://arctype.com/blog/sql-aggregate-functions/) [## 使用 SQL 聚合函数提高性能

### 在本文中，您将了解到 SQL 聚合函数是如何以一种简单的方式显著提高您的…

arctype.com](https://arctype.com/blog/sql-aggregate-functions/) 

使用`GROUP BY`的主要问题是涉及它的查询通常很慢，尤其是与只使用`WHERE`的查询相比。幸运的是，通过定义正确的 SQL [索引](https://en.wikipedia.org/wiki/Database_index)，您可以毫不费力地让它们变得快如闪电。

现在让我们深入研究一下如何实现。

## 通过 4 个步骤查询分组的完美索引

正如这里的[所描述的](https://dev.mysql.com/doc/refman/8.0/en/group-by-optimization.html)，优化一个`GROUP BY`查询可能会很棘手，涉及许多操作和调整。这意味着寻找最佳的解决方案来提高查询的性能很容易成为一项艰巨的任务。也就是说，下面的简单步骤应该可以让你取得显著的效果，并且在大多数情况下足够了。

首先，确保重写您的查询，以便在`GROUP BY`子句中使用的列以相同的顺序出现在您的`SELECT`子句的开头。请记住，在定义 SQL 索引时，列顺序至关重要。

然后，按照这些说明中的描述，定义一个包含以下列的索引:

1.  列与它们在`WHERE`子句中出现的顺序相同(如果存在)
2.  剩余的列与它们在`GROUP BY`子句中出现的顺序相同
3.  其余各栏与`ORDER BY`条款中出现的顺序相同(如果存在)
4.  剩余的列与它们在`SELECT`子句中出现的顺序相同

这种四步走的方法源于[这个](https://stackoverflow.com/questions/11631367/speeding-up-group-by-sum-and-avg-queries) [StackOverflow](https://stackoverflow.com/) 答案，并且它帮助我的团队多次将`GROUP BY`查询速度提高了 10 倍。

现在，让我们通过一个例子来看看如何定义它。假设您正在处理以下`GROUP BY` [MySQL](https://en.wikipedia.org/wiki/MySQL) 查询:

```
SELECT city, AVG(age) AS avg_age, school 
FROM fooStudent 
WHERE LENGTH(name) > 5
GROUP BY city, school
```

首先，您必须重写查询，如下所示:

```
SELECT city, school, AVG(age) AS avg_age
FROM fooStudent 
WHERE LENGTH(name) > 5
GROUP BY city, school
```

然后，根据上述 4 条规则，您的性能提升指数定义应该是这样的:

```
CREATE INDEX fooStudent_1 ON fooStudent(name, city, school, age)
```

瞧！您的`GROUP BY`查询速度将比以往任何时候都快。

# 结论

`GROUP BY`是一个强大的语句，但是它会减慢查询速度。随着时间的推移，我和我的团队已经多次使用它，并定义了 SQL 索引来避免`GROUP BY`子句引入的性能问题，尤其是在处理大型表时。具体来说，一个简单的 4 步过程就足以定义一个高效的 SQL 索引，该索引将使您的`GROUP BY`查询速度提高 10 倍，本文就是要介绍这个索引。

谢谢你的阅读！我希望你发现我的故事很有帮助。如果有任何问题、评论或建议，请随时与我联系。