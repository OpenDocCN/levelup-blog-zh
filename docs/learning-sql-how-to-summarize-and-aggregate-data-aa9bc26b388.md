# 学习 SQL:如何汇总和聚集数据

> 原文：<https://levelup.gitconnected.com/learning-sql-how-to-summarize-and-aggregate-data-aa9bc26b388>

![](img/1c3f688f0716059a897edba6809ef085.png)

弗兰基·查马基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我上一篇关于操作符和函数的文章描述了一组*标量*操作，其中一个操作符或函数用于对单个行值进行操作，或者独立于表中存储的数据计算日期或时间。我的下一个系列文章将关注于*聚合*操作，即对多组值进行操作以产生单个值，比如总和或平均值。

这些操作可以分为两组—汇总操作和分组操作。本文将从总结操作开始，然后讨论 SQL 中不同的分组操作。

注意:为了便于讨论，我在表中添加了一个新列 hours_completed。

# 使用聚合函数汇总数据

SQL 包含一组可以用来聚合数据的函数。这些函数包括(expr 表示表达式或 SQL 语句):

*   MIN(expr):返回 expr 中的最小值
*   MAX(expr):返回 expr 中的最大值
*   SUM(expr):返回 expr 中值的总和
*   AVG(expr):返回 expr 中值的平均值
*   COUNT(expr):返回 expr 中非空值的个数
*   COUNT(*):返回表格或集合中的行数

让我们从这些函数中最简单的一个开始——COUNT(*)。下面是一条 SQL 语句，它返回人口统计表中当前的行数:

```
sqlite> select count(*) from demographics;
7
sqlite> select * from demographics;
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts|3.64
1231|Raymond|Williams|Sheridan|1985-12-07|Classics|2.43
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology|3.27
2143|Jonathan|Childs|Hot Springs|1988-05-13|Film and Sound|3.52
3124|Meredith|Stanton|Maumelle|1985-06-03|English|3.72
2341|Jessica|Wise|North Little Rock|1984-08-01|English|3.55
4213|Mindy|Hodges|North Little Rock|1984-12-23|Economics|3.71
```

我包含了所有行的列表，只是为了验证 COUNT 函数是否正常工作。

以下是使用另一版本 COUNT 的示例，以及支持的“select all where”语句:

```
sqlite> select count(major) from demographics where major = "English";
2
sqlite> select * from demographics where major = "English";
3124|Meredith|Stanton|Maumelle|1985-06-03|English|3.72
2341|Jessica|Wise|North Little Rock|1984-08-01|English|3.55
```

接下来我要介绍的汇总函数是 MAX 和 MIN。这些函数接受一个表达式(一列),并分别从被查询的表的列中返回最大值和最小值。

以下是两个函数的示例，其中我查询了人口统计表中的最高和最低平均绩点:

```
sqlite> select last_name, major, max(gpa) from demographics;
Stanton|English|3.72
sqlite> select last_name, major, min(gpa) from demographics;
Williams|Classics|2.43
```

我要讲的下一个函数是 SUM。该函数将查找表中指定列的总值。为了使用这个函数，我想向人口统计表中添加一个新列— hours_completed。

为此，我发出 ALTER 命令将该列添加到表中。我将在以后的文章中更完整地介绍这个命令，但是现在这里是我发出的命令:

```
alter table demographics add column hours_completed int;
```

接下来，我发出了几个更新命令(我还将在以后的文章中详细介绍):

```
sqlite> update demographics set hours_completed = 45 
where last_name = "Fehrenbach";
sqlite> update demographics set hours_completed = 32 
where last_name = "Williams";
sqlite> update demographics set hours_completed = 43 
where last_name = "Martin";
sqlite> update demographics set hours_completed = 27 
where last_name = "Childs";
sqlite> update demographics set hours_completed = 53 
where last_name = "Stanton";
sqlite> update demographics set hours_completed = 14 
where last_name = "Wise";
sqlite> update demographics set hours_completed = 24 
where last_name = "Hodges";
```

现在我准备使用 SUM 命令来查看表中的学生完成了多少小时。以下是使用函数的查询:

```
sqlite> select 'Total Hours Completed: ', sum(hours_completed) 
from demographics;
Total Hours Completed: |238
```

现在，我们可以使用下一个聚合函数 AVG 来确定学生完成的平均小时数:

```
sqlite> select 'Average number of hours completed: ', avg(hours_completed) from demographics;
Average number of hours completed: |34.0
```

# 组合汇总函数

您可以在一条语句中组合两个或多个汇总函数。下面是一个示例，其中最小值、最大值和 AVG 函数用于汇总人口统计表的这些方面:

```
sqlite> select min(gpa), max(gpa), avg(gpa) from demographics;
2.43|3.72|3.357
```

# 分组数据

可以使用 GROUP BY 子句将数据分组在一起。例如，显示每个学生按专业分组的平均绩点，以查看哪个专业组的平均绩点最高。

以下是执行此任务的查询及其结果行:

```
sqlite> select major, avg(gpa) from demographics group by major;
Classics|2.77
Economics|3.63
English|3.635
Film and Sound|3.52
Graphic Arts|3.355
Information Technology|3.27
Philosophy|3.99
```

下一个示例查找完成时间最多的学生，并按其家乡分组:

```
sqlite> select last_name, hometown, max(hours_completed) 
from demographics group by hometown;
Martin|Greenbrier|43
Childs|Hot Springs|27
Fehrenbach|Little Rock|45
Stanton|Maumelle|53
Oliver|North Little Rock|33
Williams|Sheridan|32
```

还可以在分组查询中使用 WHERE 子句，如下例所示:

```
sqlite> select last_name, major, gpa 
from demographics where gpa > 3.49 group by major;
Hodges|Economics|3.71
Stanton|English|3.72
Childs|Film and Sound|3.52
Fehrenbach|Graphic Arts|3.64
Williams|Philosophy|3.99
```

我可以使用 HAVING 子句编写与上面相同的查询。这是如何做到的:

```
sqlite> select last_name, major, gpa 
from demographics 
group by gpa having gpa > 3.49;
Childs|Film and Sound|3.52
Wise|English|3.55
Fehrenbach|Graphic Arts|3.64
Hodges|Economics|3.71
Stanton|English|3.72
Williams|Philosophy|3.99
```

下面是另一个例子，我根据完成的小时数过滤数据:

```
sqlite> select last_name, major 
from demographics 
group by major having hours_completed > 30;
Williams|Classics
Stanton|English
Fehrenbach|Graphic Arts
Martin|Information Technology
Williams|Philosophy
```

至此，我已经介绍了初级 SQL 程序员需要了解的主要聚合和汇总函数。在我的下一篇文章中，我将继续讨论 SQL 编程中一个非常重要的主题——连接。

感谢您阅读这篇关于 SQL 编程的文章。请对本文提出您的意见和建议，或者发送电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)。