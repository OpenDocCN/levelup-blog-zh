# 学习 SQL:用连接查询数据第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-sql-querying-data-with-joins-part-1-9efcf1ce8666>

![](img/6ff92c5d7da9db131a3b851cf393ae81.png)

[蒂姆·约翰逊](https://unsplash.com/@mangofantasy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

到目前为止，在我学习 SQL 的文章中，我已经演示了如何只使用一个表来查询数据。但是 SQL 的主要特性之一是能够查询存储在多个表中的数据。在 SQL 中执行这些类型的查询的方式是使用一个*连接*。联接是一种查询，它使用组合相关的多个表中的列来返回单个行结果集。在本文中，我将介绍 JOIN 子句以及如何在 SELECT 语句中使用它。

# 为连接准备好数据库

到目前为止，我只开发了学生数据库中的一个表——人口统计表。我至少需要再添加几个表，以使我的连接示例有意义。我要创建的第一个表是一个 courses taken 表，它列出了一个学生所学的所有课程。下面是我为添加该表而编写的 SQL:

```
sqlite> create table courses_taken (
...> id int not null,
...> course_id text not null);
```

这是我创建的另一个表，它存储了每门课程的更多信息:

```
sqlite> create table course_info (
...> course_id text not null,
...> course_title text not null,
...> course_instructor text not null);
```

# 连接如何工作

联接用于通过使用相关列来组合两个或多个不同表中的数据。例如，通过使用 id 列将 demographics 表与 courses_taken 相结合，我可以得到一个包含学生姓名和他或她所学课程的结果集。

最常见的连接类型称为*内部连接*。对于内部联接，比较运算符用于根据两个表中的公共值来匹配这两个表中的行。我将在本文中关注内部连接，并在以后的文章中讨论一些其他的连接类型。

有两种方法可以执行内部联接。第一种方法是使用 INNER JOIN 子句。以下是该表单的语法模板:

*在 column-from-table 1 = column-from-table 2*上从表 1
内连接表 2
中选择列

执行内部连接的第二种方法是使用 WHERE 子句。以下是这种连接形式的语法模板:

*从表 1 别名 1、表 2 别名 2
中选择列
，其中 column-from-table 1 = column-from-table 2*

这是第一个例子。我的查询将使用 WHERE 子句返回在 courses_taken 表中有条目的所有学生的姓氏和课程 id(目前只有两个学生，但我将添加更多)。以下是查询和结果集:

```
sqlite> select last_name, course_id from demographics d, 
courses_taken c
...> where d.id = c.id;
Fehrenbach|BIOL1004
Fehrenbach|ENGL1003
Fehrenbach|MATH1003
Fehrenbach|PHIL1003
Stanton|ENGL1003
Stanton|ENGL1013
Stanton|MATH1003
```

请注意，我使用单个字母作为表别名。这不是必需的，您可以使用任何想要的别名。我倾向于使用单个字母来保持查询的简洁和易读。您可能希望使用更具描述性的别名，这当然是可以接受的(甚至可能是需要的)。

现在，我将使用 INNER JOIN 子句编写相同的查询。以下是查询和结果集:

```
sqlite> select last_name, course_id from demographics d
...> inner join courses_taken c
...> on d.id = c.id;
Fehrenbach|BIOL1004
Fehrenbach|ENGL1003
Fehrenbach|MATH1003
Fehrenbach|PHIL1003
Stanton|ENGL1003
Stanton|ENGL1013
Stanton|MATH1003
```

这两种版本都可以接受，如何编写连接完全取决于您的个人偏好。

# 用 USING 子句连接表

编写 join 语句的另一种方法是使用 USING 子句。这种类型的连接的语法模板如下所示:

*使用(列名)*从表名
内部连接表名
中选择列名

让我们使用这个表单直接重写上面的查询。下面是语句和结果集:

```
sqlite> select last_name, course_id
...> from demographics
...> inner join courses_taken
...> using (id);
Fehrenbach|BIOL1004
Fehrenbach|ENGL1003
Fehrenbach|MATH1003
Fehrenbach|PHIL1003
Stanton|ENGL1003
Stanton|ENGL1013
Stanton|MATH1003
```

这是另一个例子。这一次，我将把 courses_taken 表与 course_info 表连接起来，以查看到目前为止哪些教师教授学生所学的课程:

```
sqlite> select course_id, course_title, course_instructor
...> from course_info
...> inner join courses_taken
...> using (course_id);
ENGL1003|English Comp 1|Oliver
ENGL1003|English Comp 1|Oliver
ENGL1013|English Comp 2|Purkiss
MATH1003|College Algebra|Rathfon
MATH1003|College Algebra|Rathfon
BIOL1004|Biology 1|Rains
```

# 交叉连接

交叉联接是返回两个表的行的所有不同可能组合的联接。结果返回第一个表中的所有行，第一个表中的每一行都与第二个表中的所有行相结合。

下面是交叉连接的语法模板:

*从表 1
交叉连接表 2* 中选择列

为了演示交叉连接的结果，我将首先显示我将用于交叉连接的两个表的行:

```
sqlite> select * from demographics;
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts|3.64|45
1231|Raymond|Williams|Sheridan|1985-12-07|Classics|2.43|32
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology|3.27|43
2143|Jonathan|Childs|Hot Springs|1988-05-13|Film and Sound|3.52|27
3124|Meredith|Stanton|Maumelle|1985-06-03|English|3.72|53
2341|Jessica|Wise|North Little Rock|1984-08-01|English|3.55|14
4213|Mindy|Hodges|North Little Rock|1984-12-23|Economics|3.71|24
3214|Dorothy|Martin|Greenbrier|1979-10-11|Classics|3.11|17
4321|Kate|Earney|Little Rock|1985-01-02|Economics|3.55|23
4231|Logan|Oliver|North Little Rock|1983-03-30|Graphic Arts|3.07|33
5123|Jeff|Williams|Maumelle|1979-12-22|Philosophy|3.99|43sqlite> select * from course_info;
ENGL1003|English Comp 1|Oliver
ENGL1013|English Comp 2|Purkiss
MATH1003|College Algebra|Rathfon
MATH1013|Trigonometry|Hammett
BIOL1004|Biology 1|Rains
BIOL1014|Biology 2|Querns
```

下面是我交叉连接人口统计表和 course_info 表时的查询和结果:

```
sqlite> select last_name, course_id, course_title
...> from demographics
...> cross join course_info
...> ;
Fehrenbach|ENGL1003|English Comp 1
Fehrenbach|ENGL1013|English Comp 2
Fehrenbach|MATH1003|College Algebra
Fehrenbach|MATH1013|Trigonometry
Fehrenbach|BIOL1004|Biology 1
Fehrenbach|BIOL1014|Biology 2
Williams|ENGL1003|English Comp 1
Williams|ENGL1013|English Comp 2
Williams|MATH1003|College Algebra
Williams|MATH1013|Trigonometry
Williams|BIOL1004|Biology 1
Williams|BIOL1014|Biology 2
Martin|ENGL1003|English Comp 1
Martin|ENGL1013|English Comp 2
Martin|MATH1003|College Algebra
Martin|MATH1013|Trigonometry
Martin|BIOL1004|Biology 1
Martin|BIOL1014|Biology 2
Childs|ENGL1003|English Comp 1
Childs|ENGL1013|English Comp 2
Childs|MATH1003|College Algebra
Childs|MATH1013|Trigonometry
Childs|BIOL1004|Biology 1
Childs|BIOL1014|Biology 2
Stanton|ENGL1013|English Comp 2
Stanton|MATH1003|College Algebra
Stanton|MATH1013|Trigonometry
Stanton|BIOL1004|Biology 1
Stanton|BIOL1014|Biology 2
Wise|ENGL1003|English Comp 1
Wise|ENGL1013|English Comp 2
Wise|MATH1003|College Algebra
Wise|MATH1013|Trigonometry
Wise|BIOL1004|Biology 1
Wise|BIOL1014|Biology 2
Hodges|ENGL1003|English Comp 1
Hodges|ENGL1013|English Comp 2
Hodges|MATH1003|College Algebra
Hodges|MATH1013|Trigonometry
Hodges|BIOL1014|Biology 2
Martin|ENGL1003|English Comp 1
Martin|ENGL1013|English Comp 2
Martin|MATH1003|College Algebra
Martin|MATH1013|Trigonometry
Martin|BIOL1004|Biology 1
Martin|BIOL1014|Biology 2
Earney|ENGL1003|English Comp 1
Earney|ENGL1013|English Comp 2
Earney|MATH1003|College Algebra
Earney|MATH1013|Trigonometry
Earney|BIOL1004|Biology 1
Earney|BIOL1014|Biology 2
Oliver|ENGL1003|English Comp 1
Oliver|ENGL1013|English Comp 2
Oliver|MATH1003|College Algebra
Oliver|MATH1013|Trigonometry
Oliver|BIOL1004|Biology 1
Oliver|BIOL1014|Biology 2
Williams|ENGL1003|English Comp 1
Williams|ENGL1013|English Comp 2
Williams|MATH1013|Trigonometry
Williams|BIOL1004|Biology 1
Williams|BIOL1014|Biology 2
```

交叉连接在实践中不常使用，因为结果集很麻烦，输出很难理解和解释。很少使用交叉连接的另一个原因是因为结果集通常很大，即使对于只有少量记录的表也是如此。

但是，在执行更好的查询之前，交叉连接有时用于中间结果。

这是关于 SQL 连接的第一篇文章。在我的下一篇文章中，我将讨论更复杂、更有用的 SQL 连接查询。

感谢您的阅读，请回复本文或发邮件至 mmmcmillan1@att.net[告诉我您的意见和建议。](mailto:mmmcmillan1@att.net)