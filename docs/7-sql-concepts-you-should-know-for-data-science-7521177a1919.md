# 数据科学中你应该知道的 7 个 SQL 概念

> 原文：<https://levelup.gitconnected.com/7-sql-concepts-you-should-know-for-data-science-7521177a1919>

![](img/b540793da1eeed133becb3e8bc4f98b7.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你有没有发现自己在数据库中挖掘，试图提取你需要的信息，但感觉你只是在抓皮毛？这就是 SQL 的用武之地。

SQL 或结构化查询语言是一种强大的工具，它允许您与数据库进行通信，并以结构化和高效的方式提取您需要的数据。作为一名数据科学家，知道如何有效地使用 SQL 对于访问和分析数据至关重要。

在本文中，我们将讨论七个基本的 SQL 概念，它们将为您使用数据库打下坚实的基础。无论您是刚刚开始您的数据科学之旅，还是希望重温您的技能，这些概念都将为您的成功奠定基础。

所以让我们开始吧！

# 选择指令

*   SELECT 语句是从数据库中检索数据的基本工具。它允许您指定想要检索的列，或者使用`*`选择所有列。您还可以使用 WHERE 子句和逻辑运算符(如 and、OR 和 NOT)来过滤结果，以创建更复杂的过滤器。
*   例如，假设您想从“客户”表中检索居住在纽约市的所有客户:

```
SELECT * FROM customers WHERE city = 'New York';
```

或者，假设您想要检索纽约州姓氏为“Smith”的客户的特定列:

```
SELECT first_name, last_name, email FROM customers WHERE state = 'NY' AND last_name = 'Smith';
```

您还可以使用 LIMIT 关键字来限制返回的结果数。例如，如果您只想从“订单”表中检索前五个订单:

```
SELECT * FROM orders LIMIT 5;
```

SELECT 语句是在 SQL 中使用数据库的基本部分，掌握它将允许您有效地检索和分析数据。

# 加入

使用数据库时，通常需要合并多个表中的数据。这就是 JOIN 的用武之地。

有两种主要的联接类型:内部联接和外部联接。

*   内部联接根据公共列中的匹配值组合两个或多个表中的行。它只返回在两个表中都找到匹配项的行。

## 下面是一个内部联接的示例:

```
SELECT orders.order_id, customers.first_name, customers.last_name
FROM orders
INNER JOIN customers
ON orders.customer_id = customers.customer_id;
```

该语句检索已经下订单的客户的`order_id`、`first_name`和`last_name`。内部连接基于`customer_id`列，该列同时存在于“订单”和“客户”表中。

*   外部联接合并两个或多个表中的行，包括在公共列中没有匹配值的行。有三种类型的外部联接:左联接、右联接和完全外部联接。
*   LEFT JOIN 返回左表中的所有行，以及右表中任何匹配的行。如果不匹配，空值将显示在右侧表的列中。
*   RIGHT JOIN 返回右表中的所有行，以及左表中任何匹配的行。如果不匹配，空值将显示在左侧表的列中。
*   完全外连接返回两个表中的所有行，不管公共列中是否有匹配项。如果不匹配，将为不匹配的列显示空值。

## 以下是左连接的一个示例:

```
SELECT orders.order_id, customers.first_name, customers.last_name
FROM orders
LEFT JOIN customers
ON orders.customer_id = customers.customer_id;
```

该语句检索所有订单，以及相应客户的`first_name`和`last_name`(如果有的话)。如果订单不存在客户，空值将显示在`first_name`和`last_name`列中。

使用 JOIN 是组合多个表中的数据并执行更复杂查询的有效方法。

# 聚合函数

聚合函数用于对数据执行计算并返回单个结果。常见的聚合函数包括求和、AVG 和计数。

*   下面是一个使用 SUM 函数对“orders”表中的销售额进行合计的示例:

```
SELECT SUM(amount) FROM orders;
```

该语句返回`amount`列中所有值的总和。

*   您也可以使用 AVG 函数来计算一组值的平均值:

```
SELECT AVG(amount) FROM orders;
```

该语句返回`amount`列中所有值的平均值。

COUNT 函数可用于计算表中的行数或列中非空值的数量:

```
SELECT COUNT(*) FROM orders;
```

该语句返回“订单”表中的总行数。

```
SELECT COUNT(amount) FROM orders;
```

该语句返回`amount`列中非空值的数量。

还可以使用 GROUP BY 子句按一列或多列对聚合函数的结果进行分组。例如:

```
SELECT COUNT(*), country FROM customers
GROUP BY country;
```

该语句返回每个国家的客户数量。

使用聚合函数是对数据执行计算并从结果中获得洞察力的有用方法。

# 子查询

子查询用于通过将 SELECT 语句嵌套在另一个 SELECT、FROM、WHERE 或 HAVING 子句中来创建更复杂的查询。

*   以下是在 SELECT 子句中使用子查询的示例:

```
SELECT first_name, last_name
FROM customers
WHERE customer_id = (SELECT customer_id FROM orders WHERE amount > 100);
```

该语句为下了一个`amount`大于 100 的订单的客户检索`first_name`和`last_name`。WHERE 子句中的子查询为这些订单选择`customer_id`，外部查询使用该信息检索相应的客户详细信息。

*   还可以在 FROM 子句中使用子查询来创建临时表，以便在主查询中使用:

```
SELECT * FROM (SELECT * FROM customers WHERE city = 'New York') AS temp;
```

该语句创建一个名为“temp”的临时表，其中包含来自纽约市的所有客户。然后，主查询从这个临时表中选择所有列。

子查询可用于创建更复杂的查询，并从数据库中提取特定数据。

# 联合和联合所有

UNION 和 UNION ALL 用于将多个 SELECT 语句的结果组合成一个结果集。

UNION 组合两个或多个 SELECT 语句的结果，并删除任何重复的行。

*   下面是一个使用 UNION 的例子:

```
SELECT * FROM customers WHERE city = 'New York'
UNION
SELECT * FROM customers WHERE city = 'Los Angeles';
```

该语句检索纽约市和洛杉矶市的所有客户，并删除所有重复的客户。

UNION ALL 组合两个或多个 SELECT 语句的结果，并包含所有行，包括重复行。

*   下面是一个使用 UNION ALL 的示例:

```
SELECT * FROM customers WHERE city = 'New York'
UNION ALL
SELECT * FROM customers WHERE city = 'Los Angeles';
```

该语句检索纽约市和洛杉矶市的所有客户，并包括所有重复的客户。

UNION 和 UNION ALL 对于组合多个查询的结果以及处理组合的数据非常有用。

# 空值

空值表示数据库中缺失或未知的数据。它们不同于零(0)或空字符串，因为它们表示没有值。

您可以使用 IS NULL 和 IS NOT NULL 运算符来筛选查询中的空值。

*   下面是一个使用 IS NULL 的示例:

```
SELECT * FROM customers WHERE last_name IS NULL;
```

该语句检索所有`last_name`为空的客户。

*   下面是一个使用 IS NOT NULL 的示例:

```
SELECT * FROM customers WHERE last_name IS NOT NULL;
```

该语句检索所有`last_name`不为空的客户。

使用数据库时，了解空值很重要，因为它们会影响查询的结果。

恭喜你坚持到这篇文章的结尾！现在，您应该对数据科学的七个基本 SQL 概念有了坚实的基础:选择、连接、聚合函数、子查询、UNION 和 UNION ALL 以及 NULL 值。

掌握这些概念将允许您有效地与数据库通信，并提取您分析所需的数据。但是不要止步于此——对于 SQL，总是有更多的东西需要学习和实践。

如果您喜欢这篇文章，并希望了解更多数据科学内容，请务必关注我的 Medium！我定期发表各种数据相关主题的文章，我很乐意与您同行。

下次再见，查询愉快！