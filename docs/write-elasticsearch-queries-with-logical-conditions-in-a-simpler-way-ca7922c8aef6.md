# 以更简单的方式编写带有逻辑条件的弹性搜索查询

> 原文：<https://levelup.gitconnected.com/write-elasticsearch-queries-with-logical-conditions-in-a-simpler-way-ca7922c8aef6>

## Elasticsearch 中的 query_string 查询入门

![](img/5cec3b68e0e62732e6e7c198197f756f.png)

图片由 PIRO4D 提供

当涉及到诸如 NOT、AND、OR 等布尔运算时，我们通常使用带有`must`、`should`、`must_not`子句的`bool`查询。是的，`bool`查询功能非常强大，可以用来执行所有类型的高级搜索。然而，对于具有基本 NOT、AND 和 or 条件的简单搜索，使用`bool`查询有点矫枉过正，因为您需要编写大量样板代码。这就是`query_string`查询的用武之地，因为它有更简单的语法。

如果您想跟随本文并自己运行代码片段，请按照本文[中的演示设置环境、创建索引并导入数据。否则，您可以继续查看下面的例子，这些例子很容易理解。我们将比较不同条件下`bool`和`query_string`查询的用法。](https://medium.com/p/b5bb01ed3824)

## 非操作

让我们找到那些品牌不是惠普的笔记本电脑。使用`bool`查询，代码如下:

如您所见，我们需要使用`bool` / `must_not`样板代码(和许多括号)来编写一个带有简单 NOT 条件的查询。使用`query_string` 查询，语法更简单，也更灵活。以下所有方法都将起作用，并给出相同的结果:

*   要搜索的字段可以用`[default_field](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-top-level-params)` [或](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-top-level-params) `[fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-top-level-params)`参数指定，也可以直接在查询中指定。当在查询中指定它时，它应该后跟一个冒号，布尔条件应该放在括号中。
*   用`query_string`以更简单的方式指定布尔条件。例如，您可以使用`NOT`或感叹号`!`来指定一个 NOT 条件。当 AND 或 or 条件存在时，这一点会更加突出。

## 与操作

让我们找到品牌为 HP 且名称中有 EliteBook 的笔记本电脑:

嗯，这里有点失控，因为您需要非常小心嵌套查询，不要错过任何方括号或圆括号。同样，使用`query_string`要简单得多:

哇，简单多了，不是吗？没有样板代码和令人眼花缭乱的括号疯狂嵌套。

请注意，如果查询有逻辑条件，您需要将它放在括号中。举个例子，就拿品牌是 HP 但不是 EliteBook 的笔记本电脑来说吧:

非常简单易读。如果您使用`bool`查询，您将需要使用`must`和`must_not`子句，这将变得更加复杂:

## 或操作

为了完整起见，让我们检查 or 条件，并查找品牌为 Dell 或名称中有 EliteBook 的笔记本电脑(仅用于演示😃).对于`bool`查询，您需要使用`should`子句:

有了`query_string`查询，就简单多了:

我想你已经明白了使用`query_string`比使用`bool`查询要简单得多。我们刚刚讨论了`query_string`查询的本质，在[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)中可以找到很多其他有用的特性。但是，您只需要在需要使用更高级的功能时学习它们。

尽管`query_string`对于简单的查询来说非常方便，但是对于[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-nested)中提到的嵌套文档来说并不方便，也不推荐使用。对于嵌套文档的查询，最好使用`nested`查询，在[这篇文章](https://lynn-kwong.medium.com/learn-advanced-crud-and-search-queries-for-nested-objects-in-elasticsearch-from-practical-examples-7aebc1408d6f)中有更详细的演示。

相关帖子:

*   [如何用 Python 中的 Bulk API 索引 Elasticsearch 文档](https://medium.com/p/b5bb01ed3824)
*   [从实际例子中学习 Elasticsearch 中嵌套对象的高级 CRUD 和搜索查询](https://lynn-kwong.medium.com/learn-advanced-crud-and-search-queries-for-nested-objects-in-elasticsearch-from-practical-examples-7aebc1408d6f)