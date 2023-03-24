# 学习 SQL:从表中检索数据

> 原文：<https://levelup.gitconnected.com/learning-sql-retrieving-data-from-a-table-fa1659636bcc>

![](img/e44767cd24fd226dfae1c2abdbb04e3f.png)

扬·安东宁·科拉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

欢迎阅读我关于学习 SQL 的第二篇文章。在我的第一篇文章中，我讨论了什么是数据库以及数据如何存储在数据库中，并向您介绍了 SQLite 数据库系统，我将使用该数据库来演示 SQL 如何工作。我还讨论了如何创建一个新的数据库，以及如何创建一个表来存储数据。我在文章的最后演示了如何在数据库表中存储数据。

在本文中，我将开始一个关于使用`SELECT`语句从数据库表中检索数据的长时间讨论(多篇文章)。

# 一些检索术语

要求系统返回数据被称为*查询*。查询通常由某种形式的`SELECT`语句构成，我将在下面和以后的文章中详细描述。

当讨论从数据库表中检索数据时，程序员通常指的是检索一列或多列数据。*列*是数据字段。它们被称为列，因为当从表中检索数据时，字段中的数据以列的形式排列。因此，在本文的其余部分，当我使用术语列时，我指的是一个字段。

当查询返回数据时，它将每个数据库记录作为一个*行*返回。当我描述`SELECT`语句如何工作时，我经常引用查询返回的行。

# 使用基本的 SELECT 语句

`SELECT`语句用于从数据库表中检索数据。`SELECT`声明有几种形式，我将在本文中介绍其中的基本形式。我将在以后的文章中介绍更复杂形式的`SELECT`语句。

我将使用的第一个表单从表中检索一列数据。以下是该表单的语法模板:

*从表中选择列；*

让我们用我在上一篇文章中建立的人口统计表来看几个例子:

```
sqlite> select first_name from demographics;
Cynthia
Raymond
Danny
Jonathan
sqlite> select last_name from demographics;
Fehrenbach
Williams
Martin
Childs
sqlite> select major from demographics;
Graphic Arts
Classics
Information Technology
Film and Sound
sqlite>
```

如果您只想查看表中的某些数据，此表单非常有用。

一种更有用的形式的`SELECT`语句允许您从一个表中检索多个列。以下是该表单的语法模板:

*从表中选择列 1，列 2，…，列 n；*

请求的每一列都用逗号分隔，直到最后一列。

以下是一些例子:

```
sqlite> select first_name, last_name from demographics;
Cynthia|Fehrenbach
Raymond|Williams
Danny|Martin
Jonathan|Childs
sqlite> select last_name, major from demographics;
Fehrenbach|Graphic Arts
Williams|Classics
Martin|Information Technology
Childs|Film and Sound
sqlite> select last_name, id from demographics;
Fehrenbach|1234
Williams|1231
Martin|2314
Childs|2143
sqlite>
```

请注意，在最后一个示例中，我将 id 号放在姓氏后面，以演示不必按照列在表中的顺序来检索它们。这对你来说似乎是显而易见的，但我过去也有学生问过这个问题。

您还应该注意到，SQLite 在每一列之间放置了一个横条，让您知道一列的结束位置和下一列的开始位置。

第三种形式的 SELECT 将检索表中的所有列。以下是该表单的语法模板:

*从表中选择*；*

星号被称为*通配符*字符。通配符用于用任何字符替换特定字符。在这种情况下，星号用于指代表中的所有列。

下面是如何使用这种形式的`SELECT`语句:

```
sqlite> select * from demographics;
1234|Cynthia|Fehrenbach|Little Rock|02-09-1987|Graphic Arts
1231|Raymond|Williams|Sheridan|05-12-1988|Classics
2314|Danny|Martin|Greenbrier|06-12-1978|Information Technology
2143|Jonathan|Childs|Hot Springs|05-13-1985|Film and Sound
sqlite>
```

# 将检索到的数据排序

有几个子句可以和`SELECT`语句一起使用来修改查询结果。这些条款之一是由命令的*。以下是 order by 子句的一个版本的语法模板:*

*按列名顺序从表名中选择[列，列，*];*

下面是一个例子，我根据姓氏按字母顺序对返回的数据进行排序:

```
sqlite> select first_name, last_name from demographics order by last_name;
Jonathan|Childs
Cynthia|Fehrenbach
Danny|Martin
Raymond|Williams
```

订单按姓氏的字母顺序排序。

现在让我们检索按出生日期排序的数据集:

```
sqlite> select last_name, dob from demographics order by dob;
Martin|1978-06-12
Williams|1985-12-07
Fehrenbach|1988-02-09
Childs|1988-05-13
```

我们还可以通过添加我们希望降序(`DESC`)而不是升序的规范，从最年轻到最老对数据进行排序。这里有一个例子:

```
sqlite> select last_name, dob from demographics order by dob desc;
Childs|1988-05-13
Fehrenbach|1988-02-09
Williams|1985-12-07
Martin|1978-06-12
```

你可以提供的另一个规格是`DISTINCT`。这将导致系统只返回唯一的行，也就是说，对于特定的查询，如果两行中有相同的数据，则只返回一行。

这是一份最新的学生名单，其中两名学生的专业相同:

```
1234|Cynthia|Fehrenbach|Little Rock|1988-02-09|Graphic Arts
1231|Raymond|Williams|Sheridan|1985-12-07|Classics
2314|Danny|Martin|Greenbrier|1978-06-12|Information Technology
2143|Jonathan|Childs|Hot Springs|1988-05-13|Film and Sound
3124|Meredith|Stanton|Maumelle|1985-06-03|English
2341|Jessica|Wise|North Little Rock|1984-08-01|English
```

如果我只想查看学生的各种专业，并且不需要看到任何专业被列出两次，我可以编写如下查询:

```
sqlite> select distinct major from demographics;
Graphic Arts
Classics
Information Technology
Film and Sound
English
```

让我们跳回分类数据。您可以按多列对数据进行排序，因此，如果我想按学生的专业和姓氏对他们进行排序，我可以编写如下查询:

```
sqlite> select first_name, last_name from demographics order by major, last_name;
Raymond|Williams
Meredith|Stanton
Jessica|Wise
Jonathan|Childs
Cynthia|Fehrenbach
Danny|Martin
```

这样两个英语专业就按字母顺序显示了。

这就结束了对`SELECT`声明的第一次讨论。在我的下一篇文章中，我将深入探讨另一种形式的`SELECT`语句，它允许对查询返回的数据进行更精确的处理——`WHERE`子句。

感谢您的阅读，请给我发邮件，地址是[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)，告诉我您的意见和建议，或者对本文做出回应。