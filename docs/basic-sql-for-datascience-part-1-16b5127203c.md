# 数据科学基础 SQL

> 原文：<https://levelup.gitconnected.com/basic-sql-for-datascience-part-1-16b5127203c>

SQL 代表结构化查询语言。它是一种用于与 [*关系数据库*](https://en.wikipedia.org/wiki/Relational_database) 一起工作的查询语言，这意味着我们使用这种语言与存储在关系数据库中的数据进行交互。

![](img/a8a6eb4a6b6dcbc03d85fedf916173eb.png)

由[万花筒](https://unsplash.com/@kaleidico?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/structure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

SQL 有不同的平台，如

*   Sqlite
*   一种数据库系统
*   关系型数据库

等等。,

今天，我将与大家分享一些基本的 SQL 命令，这些命令对于一名数据科学家在使用数据库时非常重要。

如果你点击了上面提供的链接，你应该知道什么是关系数据库…如果你没有，不用担心，我会用简单的话来说。

> 关系数据库是表的集合。

表格只是一组行和列，就像电子表格一样，它只代表一种类型的实体。

需要记住的一点是，一行也被称为*记录*，一列也被称为*字段*。

最后但同样重要的是，*查询*就是从数据库表中请求数据。正如我之前所说，这是数据科学家的一项基本技能。

现在我们知道了一些基本术语，让我们继续学习 SQL 中使用的命令。

# 挑选

该命令用于从表中选择数据。

**语法** : `SELECT [col_name] FROM [table_name];`

请记住，SQL 命令不区分大小写，但习惯上使用大写字母，我们也使用分号(；)来结束查询。

即使用小写字母书写，这个查询也能很好地工作，但是正如我告诉你的，这是一个约定。

现在，我们要做的是指定我们需要其数据的列名以及它所属的表名。

例如，假设我们有一个包含`name`、`birthdate`和`age`列的`people`表。

现在，如果我们需要年龄列中的数据，我们可以使用 SELECT 命令，如下所示:

`SELECT age FROM people;`

这将返回 people 表的 age 列中的所有数据。

如果我们想选择所有的列，那么我们可以使用`*`

`SELECT * FROM people;`

这将选择表中出现每一列。

# 限制

顾名思义，这是用来限制我们得到的结果。语法如下:

语法:`LIMIT [no. of results you want to limit to]`

在前面的例子中，我们选择了每一列，如果它返回大量的数据会怎么样呢？我们真的不会一直需要那么多。如果我们只是试图理解数据，那么我们可以通过查看一些记录(行)来做到这一点。这就是`limit`有用的地方。

`SELECT * FROM people LIMIT 10;`

你可以写在一行或多行，它会工作相同。在我看来，如果查询是一个复杂的查询，那么把它写在多行上是最好的，因为它确保了可理解性。

`SELECT *
FROM people
LIMIT 10;`

这将只返回`people`表的`10 rows`。

# 明显的

这用于选择所有唯一值。

例如，我们只想在 people 表中选择唯一的名称。

`SELECT DISTINCT name FROM people;`

# 过滤

这是 SQL 的一个基本主题。大多数时候我们会过滤数据，知道如何过滤数据很重要。不要担心这很容易，你只需要知道你想要过滤数据的条件。

为了过滤数据，我们使用`WHERE`命令。这不是一个独立的命令，这意味着如果您就这样使用它，您将会得到一个错误。它总是在选择您的数据后使用。

有不同的操作符可用于`WHERE`命令。
`=``<` `>``≤``≥`。有趣的是，SQL 有两种不等于。`<>`和`!=.` 如果你想知道为什么那么[点击](https://www.sqlshack.com/sql-not-equal-operator/)这里。

你可以使用它们中的任何一个，但是我建议使用`<>.`

现在来看过滤的例子，让我们取一个名为`cricket`的新表。(如果你不知道板球是什么，它是一项类似棒球的运动。我们在板球中也使用球棒和球🏏)

`cricket`表中有板球运动员的信息。`name`、`age`、`50s`、`100s`、`is_batsmen`、`is_bowler`。

现在我们要提取至少有五次 50 分的球员(他们至少有五次得分 50 分。)

我们可以这样做:

`SELECT name, 50s
FROM cricket
WHERE 50s ≥ 5;`

这将返回至少获得五个 50 分的玩家的`name`、`50s`列。

现在我们再举一个例子。我们想要年龄在 19 到 29 岁之间的运动员的信息。我们能做什么？

这里我们有不止一个条件。当我们有多个条件并且它们都应该为真时，我们可以使用`and`关键字。

`SELECT name, age
FROM cricket
WHERE age ≥ 19 and age ≤ 25;`

当基于多个条件但不是所有条件都满足时，如果我们愿意，我们可以使用`or`关键字。

`SELECT name, age
FROM cricket
WHERE age = 19 or age = 25;`

以上返回年龄为 19 或 25 的行。

我们也可以同时使用`and`和`or`关键字。

`SELECT name, age
FROM cricket
WHERE (age = 19 or age = 25)
and (50s > 5 or 50 < 10);`

如你所见，我在分离条件时使用了`()`。你也这样做是很重要的。否则，由于 SQL 的优先规则，我们可能得不到我们想要的结果。

> 每个人都是强盗，直到他们不得不使用上百个语句🤣🤣🤣

比如在一些合法的情况下，你真的应该使用很多 where 语句。这将是一个问题。这就是命令`BETWEEN`与离合器一起出现的地方😎

# 在...之间

`BETWEEN`是一个关键字/命令，用于过滤特定范围内的值`BETWEEN`包含在内。

![](img/8308818608a815d1b8ff842033aedde1.png)

由[克拉丽斯·迈耶](https://unsplash.com/@clarissemeyer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/between?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

语法:

`BETWEEN [start] to [end];`

假设我们有一个名为 film 的表，其中包含与各种电影相关的所有细节。现在，我想选择 1994 年和 2000 年之间的`title`列的值。

我不用写 6 个`WHERE`语句，只需用一行关键字`BETWEEN`就可以完成任务。

`SELECT title
FROM films
WHERE release_year
BETWEEN 1994 AND 2000;`

我们可以使用`in`关键字来获取与元组中的值相匹配的值。

示例:

`SELECT name
FROM kids
WHERE age in (2,4,6,8,10);`

# 空和非空

在 SQL 中，NULL 表示缺少或未知的值。我们可以使用`IS NULL`表达式来检查空值

示例:

`SELECT COUNT(*)
FROM people
WHERE birthdate IS NULL;`

为了过滤掉非空值，我们可以使用`IS NOT NULL.`

示例:

`SELECT COUNT(*)
FROM people
WHERE birthdate IS NOT NULL;`

# 喜欢和不喜欢

`LIKE`运算符可与`WHERE`子句一起使用，以搜索列中的模式。

我们可以使用通配符作为其他值的占位符。

![](img/5e2ee5755ce86f8d3fad170e826283f7.png)

照片由[詹·西奥多](https://unsplash.com/@jentheodore?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/wild-card?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通配符有两种类型:

*   `%`(百分点)
*   `_`(下划线)

`%`在我们想要保存多个字符时使用，而 _ 在我们只想保存单个字符时使用。

示例:

`SELECT name
FROM people
WHERE name LIKE ‘A%’;`

上面的例子选择了所有以字母 a 开头的数据。

`SELECT name
FROM people
WHERE name LIKE ‘K_RTHIK’;`

以上给出了 KARTHIK 等结果。,

我们可以使用`NOT LIKE`来查找与您指定的模式不匹配的记录。

# 聚合函数

这些是执行特定计算的函数。这包括函数

*   `AVG()`
*   `MAX()`
*   `MIN()`
*   `SUM()`
*   `COUNT()`

如果你看了这些函数还不明白，我就告诉你。

*   `Average of`
*   `Maximum of`
*   `Minimum of`
*   `Sum of`
*   `Count of`

我希望你能像他们一样。我只举几个例子。

`SELECT AVG(budget)
FROM films;`

返回预算列的平均值。

`SELECT MAX(budget)
FROM films;`

返回预算列中的最大值。

`SELECT MIN(budget)
FROM films;`

返回预算列中的最小值。

`SELECT SUM(budget)
FROM films;`

返回预算列中所有值的总和。

`SELECT COUNT(budget)
FROM films;`

返回预算列中出现的值的计数。

这些集合函数也可以与 WHERE 子句一起使用。

`SELECT release_year
FROM films
WHERE COUNT(title) > 10;`

除了使用集合函数，我们还可以使用符号+、-、*、/等执行算术运算。,

示例:

`SELECT (4*3);`

上面的结果是 12。

需要记住的一点是，如果使用整数，SQL 会返回一个整数。如果您希望结果是 float，请确保至少使用一个浮点数。

示例:

`SELECT (4/3);`

以上结果 1。如果你想在你的结果中有小数，你可以这样做。

`SELECT (4.0/3.0);`

这将返回 1.33。

# 错认假频伪信号

如果你看过[死亡笔记](https://en.wikipedia.org/wiki/Death_Note)那么你应该知道 ***基拉*** 知道一个人的名字，对杀死他们有多重要。我们有这样的例子，一个人用化名，以避免被杀。

![](img/c8a4b47de8b81c9c334856183ef372af.png)

[萨汉德·巴巴里](https://unsplash.com/@sahandbabali?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/death-note?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这种情况下，**别名**意味着有一个假名，用于特殊目的或任何其他原因。同样适用，不同的是为了更好的理解，我们给了一个别名。

我们举个例子。

`SELECT MAX(budget), MAX(duration)
FROM films;`

上面将返回 max 作为两列名称的结果，这可能会引起混淆。这就是别名有用的时候。为了更好地理解，我们可以临时命名这些列。

`SELECT MAX(budget) as max_budget, MAX(duration) as max_duration
FROM films;`

这里*最大预算*和*最大持续时间*是别名。

# 以...排序

顾名思义，我们用这个来对结果进行排序，可以是升序也可以是降序。

默认情况下，结果按升序排序。

我们使用`DESC`关键字进行降序排序。

示例:

`SELECT title, realease_year
FROM films
ORDER BY title DESC;`

结果按标题列降序排列。

我们还可以同时对多列进行排序。它将按指定的第一列排序，然后按下一列排序，依此类推。

示例:

对于这个例子，我们将采用一个名为`people`的表，其中包含人员信息。

`SELECT birthdate, name
FROM people
ORDER BY birthdate, name;`

# 分组依据

这允许按一列或多列对结果进行分组。这是在我们汇总结果时完成的。

示例:

`SELECT sex, COUNT(*)
FROM people
GROUP BY sex;`

这将选择表中每条记录的性别和计数，并按性别列对结果进行分组。

该子句通常与`COUNT(), MAX()`这样的聚合函数一起使用

这个从句用在`from`从句之后。

# 拥有

在 SQL 中，我们不能将`where`与聚合函数一起使用。如果我们想要基于聚合函数的结果进行过滤，我们需要使用`HAVING`子句。

让我给你看一个例子。

`SELECT release_year
FROM films
GROUP BY release_year
HAVING COUNT(title) > 10;`

上述查询是有效的。

`SELECT release_year
FROM films
GROUP BY release_year
WHERE COUNT(title) > 10;`

现在，这是无效的。

我们不能将聚合函数与`WHERE`子句一起使用，因为它可以过滤单独的行。`HAVING`可以和它们一起工作，因为它是用来过滤组的。

`WHERE`子句执行行操作，而`HAVING`子句执行列操作。

# 摘要

通过这个博客，我们了解到以下事情。

*   什么是 SQL，它代表什么？
*   用在什么地方？
*   基本术语。
*   SQL 中使用的命令。

老实说，这只是最基本的:冰山一角。我们仍然有先进的概念，如加入两个表，创建您的表和更多。

我希望你发现这是有用的，并且至少能够学到一点点。如果你喜欢我的工作，那么别忘了在 [Medium](https://karthikbhandary2.medium.com/) 和 [YouTube](https://www.youtube.com/channel/UCKplT0-YqAQdCq6Xajcq5Tw) 上关注我，获取更多关于生产力、自我提升、编码和技术的内容。

同时，你为什么不看看我最近的作品:

[](/joins-with-pandas-dataframes-bd077a6ec20d) [## 与熊猫数据框架连接

### 连接是数据科学家应该知道的关键概念之一。当一个数据科学家在工作时，它通常会…

levelup.gitconnected.com](/joins-with-pandas-dataframes-bd077a6ec20d) [](https://pub.towardsai.net/soar-your-bet-in-data-science-using-unix-cmds-607bc34d7f70) [## 使用 Unix Cmds 在数据科学上大展身手

### 让你成为一个厉害的数据科学家

pub.towardsai.net](https://pub.towardsai.net/soar-your-bet-in-data-science-using-unix-cmds-607bc34d7f70)