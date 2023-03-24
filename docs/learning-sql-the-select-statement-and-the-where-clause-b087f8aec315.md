# 学习 SQL:SELECT 语句和 WHERE 子句

> 原文：<https://levelup.gitconnected.com/learning-sql-the-select-statement-and-the-where-clause-b087f8aec315>

![](img/f17d17f6983c583109bf82fe377e1cea.png)

阿尔瓦罗·雷耶斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我上一篇关于学习 SQL 的文章中，我讨论了如何使用 SELECT 语句查询数据库表。在本文中，我将讨论如何使用 WHERE 子句来细化查询。

# SELECT 语句

快速回顾一下，`SELECT`语句用于从数据库表中检索数据(称为查询)。这里有一个例子:

```
sqlite> select first_name, last_name from demographics;
Cynthia|Fehrenbach
Raymond|Williams
Danny|Martin
Jonathan|Childs
Meredith|Stanton
Jessica|Wise
```

您也可以通过用星号替换列名来指定所有列:

```
sqlite> select * from demographics;
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts
1231|Raymond|Williams|Sheridan|1985-12-07|Classics
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology
2143|Jonathan|Childs|Hot Springs|1988-05-13|Film and Sound
3124|Meredith|Stanton|Maumelle|1985-06-03|English
2341|Jessica|Wise|North Little Rock|1984-08-01|English
```

但是，在许多情况下，您希望将查询返回的数据限制为存储在表中的数据的子集。WHERE 子句可以帮助您细化查询，以符合您想要的限制。

# 带有关系运算符的 WHERE 子句

`WHERE`子句的目的是根据条件或规范请求表中数据的子集。让我们从`WHERE`子句的语法模板开始:

*select [*，columns]from table-name where[条件/规格]；*

让我们从给`WHERE`子句提供一个条件开始。这里有一个例子:

```
sqlite> select * from demographics where last_name = 'Fehrenbach';
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts
```

该条件要求只检索姓氏为“Fehrenbach”的行。

这是另一个例子:

```
sqlite> select * from demographics where major = "English";
3124|Meredith|Stanton|Maumelle|1985-06-03|English
2341|Jessica|Wise|North Little Rock|1984-08-01|English
```

这里的条件只要求专业字段中包含“English”的行，并且返回了两行。

最后两个例子使用了等式条件，但是您也可以使用其他关系运算符。下面是一个使用`<`操作符的例子:

```
sqlite> select first_name, last_name, dob from demographics where dob < '1985';
Danny|Martin|1978-06-12
Jessica|Wise|1984-08-01
```

下面是一个使用`!=`操作符的例子:

```
sqlite> select first_name, last_name, major from demographics where major != "English";
Cynthia|Fehrenbach|Graphic Arts
Raymond|Williams|Classics
Danny|Martin|Information Technology
Jonathan|Childs|Film and Sound
```

# 带有布尔运算符的 WHERE 子句

有时候，一个`WHERE` 子句中只有一个条件是不够的。您可以使用布尔运算符组合两个或多个条件。布尔运算符是 and、or 和 not。

`and`操作符用于返回的行必须同时满足两个条件的查询。在第一个示例中，查询搜索以 M 和 Z 之间的字母开头的姓氏和非英语专业，姓氏按字母顺序排列:

```
sqlite> select * from demographics where last_name > 'L' and major != 'English' order by last_name;
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology
1231|Raymond|Williams|Sheridan|1985-12-07|Classics
```

下面是另一个例子，其中查询搜索 1985 年或之后出生的英语专业学生:

```
sqlite> select first_name, last_name, major from demographics where major = 'English' and dob > '1985';
Meredith|Stanton|English
```

`or`操作符将返回匹配任一条件的查询。下面是一个示例，其中查询搜索学生出生于 1988 年之前或他们的家乡是小石城的行:

```
sqlite> select last_name, dob, hometown from demographics where dob < '1988' or hometown = "Little Rock";
Fehrenbach|1988-02-09|Little Rock
Williams|1985-12-07|Sheridan
Martin|1978-06-12|Greenbrier
Stanton|1985-06-03|Maumelle
Wise|1984-08-01|North Little Rock
```

第一行出生于 1988 年，但她的家乡是小石城，所以它与查询匹配。

`not`操作符简单地否定它后面出现的条件。例如，要查找所有非英语专业的学生，查询可以写成这样:

```
sqlite> select first_name, last_name, major from demographics where not major = "English";
Cynthia|Fehrenbach|Graphic Arts
Raymond|Williams|Classics
Danny|Martin|Information Technology
Jonathan|Childs|Film and Sound
```

编写这样的查询并不比使用！=运算符，但是您应该始终努力使查询易于理解，因为其他人可能会阅读您的查询，或者您可能需要稍后重新访问查询，而以不清楚的方式编写的查询将会花费您更长的时间来理解。

# 相似模式匹配

到目前为止，我执行的大多数查询都涉及到精确的值，比如字符串数据、姓名或专业。在这一节中，我将介绍如何执行匹配数据模式的查询，例如以“M”开头的单词或其中包含“iec”的单词。

为了使用模式，我们必须使用通配符来指定模式。SQL 中使用了两个通配符:`%`和`_`(下划线)。现在让我们花些时间来看看这些通配符是如何工作的。

`%`通配符用于匹配任何零个或多个字符的字符串。例如，如果你想查询所有以字母“E”开头的专业，你可以写`"E%"`，这将匹配“英语”和“经济学”。

您也可以在图形的末尾使用`%`操作符。模式`“%s”`将匹配一个或多个字符的任何字符串，该字符串末尾有一个“s”。

`_`字符(下划线)用于匹配字符串中的任何一个字符。例如，模式`“____”` 将匹配任何四个字符的字符串，而`“E___”`将匹配任何以“E”开头并有其他三个字符的字符串。

记住所有这些，让我们看一些例子。第一个示例返回专业以“E”开头的所有学生:

```
sqlite> select last_name, major from demographics where major like "E%";
Stanton|English
Wise|English
Hodges|Economics
```

下一个示例匹配所有生日在 1985 年的学生:

```
sqlite> select first_name, last_name from demographics where dob like '1985%';
Raymond|Williams
Meredith|Stanton
```

# 介于之间的范围过滤

您可以使用`BETWEEN`子句在表中查询一组介于两个指定值之间的值。`BETWEEN`的语法模板是:

*从表名中选择列名，其中列名在值 1 和值 2 之间*

对于这一部分，我在表中添加了一个平均绩点(gpa)列。在后面的文章中，我将向您展示如何做到这一点。不过现在，让我们用 gpa 专栏来演示一下`BETWEEN`子句是如何工作的。

首先，这是所有行的列表，以便您可以看到添加的 gpa 值:

```
sqlite> select * from demographics;
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts|3.64
1231|Raymond|Williams|Sheridan|1985-12-07|Classics|2.43
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology|3.27
2143|Jonathan|Childs|Hot Springs|1988-05-13|Film and Sound|3.52
3124|Meredith|Stanton|Maumelle|1985-06-03|English|3.72
2341|Jessica|Wise|North Little Rock|1984-08-01|English|3.55
4213|Mindy|Hodges|North Little Rock|1984-12-23|Economics|3.71
```

下面是第一个示例，我们在表中查询介于 3.3 和 3.6 之间的 gpa 值:

```
sqlite> select last_name, gpa from demographics where gpa between 3.3 and 3.6;
Childs|3.52
Wise|3.55
```

您还可以对字符串使用`BETWEEN`子句，如下例所示:

```
sqlite> select last_name, major from demographics where major between 'A' and 'F';
Williams|Classics
Stanton|English
Wise|English
Hodges|Economics
```

您可以通过使用`NOT`来否定`BETWEEN`查询，如下所示:

```
sqlite> select last_name, gpa from demographics where gpa not between 3.3 and 3.6 order by gpa;
Williams|2.43
Martin|3.27
Fehrenbach|3.64
Hodges|3.71
Stanton|3.72
```

请注意，gpa 值是有序的，因此较低的 gpa 值首先显示。

请记住，`BETWEEN`子句是使用`WHERE`和`AND`的简写:

```
sqlite> select last_name, gpa from demographics where gpa >= 3.3 and gpa <= 3.6 order by gpa;
Childs|3.52
Wise|3.55
```

# 使用 IN 进行列表过滤

执行查询的另一种方式是要求查询返回与给定列表的一个或多个值匹配的行。这是使用`IN`关键字完成的。

下面是`IN`的语法模板:

*从列所在的表中选择列(值列表)；*

下面是一个使用`IN`返回与给定列表匹配的专业列表的例子:

```
sqlite> select last_name, major from demographics where major in ('English','Economics');
Stanton|English
Wise|English
Hodges|Economics
```

下面是另一个示例，其中指定的列表是平均绩点列表:

```
sqlite> select last_name, major, gpa from demographics where gpa in (3.70, 3.71, 3.72);
Stanton|English|3.72
Hodges|Economics|3.71
```

请记住，使用`IN`关键字是编写使用带有一系列`OR`的`WHERE`的查询的另一种方式，如上面的专业示例中的重写:

```
sqlite> select last_name, major from demographics where major = 'English' or major = 'Economics';
Stanton|English
Wise|English
Hodges|Economics
```

这就结束了我对使用 SELECT 语句进行查询的讨论。在我的下一篇文章中，我将开始研究 SQL 操作符和函数。

感谢您的阅读，请对本文提出您的意见和建议，或给我发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)。