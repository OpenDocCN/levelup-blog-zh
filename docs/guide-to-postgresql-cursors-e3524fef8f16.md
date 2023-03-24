# PostgreSQL 游标指南

> 原文：<https://levelup.gitconnected.com/guide-to-postgresql-cursors-e3524fef8f16>

## 探究游标在 PostgreSQL 中的工作方式。

![](img/ec94490434905a0f086716e9cf150302.png)

数据库游标是一个指针，它允许用户以基于行的顺序迭代查询结果。通常，游标可以定义为不同的属性:“只读”与“可更新”，“只进”与“可滚动”，以及“敏感”与“不敏感”。我们将讨论与 PostgreSQL 相关的这些属性以及它们如何在内部工作。

可以使用具有以下语法的`DECLARE`子句来定义[游标](https://www.postgresql.org/docs/13/sql-declare.html):

```
DECLARE ***name*** [ BINARY ] [ ASENSITIVE | INSENSITIVE ] [ [ NO ] SCROLL ]
    CURSOR [ { WITH | WITHOUT } HOLD ] FOR ***query***
```

*查询*首先被规划器/优化器解析成查询树。然后，执行器将处理查询树并逐步返回结果，如这里的[和](https://www.postgresql.org/docs/13/executor.html)所述。

每次发出一个`FETCH`命令，执行器都会运行一次查询树的顶层节点。它将递归地运行它的后代，最终，顶部节点将返回结果集的下一行。这个过程可以迭代地进行，直到查询结果用尽并且顶部节点返回 NULL。(注意:该过程由源文件“execMain.c”中定义的`ExecutionRun()`过程处理。)

## 灵敏度

SQL:2016 标准中定义的光标敏感度:

> 如果游标是打开的，并且打开游标
> 的 SQL-transaction 对 SQL-data 进行了重大更改，那么
> 在游标关闭之前是否可以通过该游标看到该更改由下面的
> 确定:
> 
> —如果光标不敏感，则重大变化
> 不可见。
> —如果光标敏感，则明显变化
> 可见。
> —如果光标是敏感的，那么显著的
> 变化的可见性是依赖于实现的。

在撰写本文时，PostgreSQL 仅支持不敏感游标。这意味着在声明游标之后，在同一事务中对原始表所做的更改将不可见。因此，指定`ASENSITIVE`或`INSENSITIVE`没有任何作用。

事实上，当游标打开时(即声明游标时)，将创建表的快照。在`Fetch`期间，每次调用 executor 之前，cursor portal 都会加载快照。它只会在执行器完成处理后弹出快照。(注:这个可以从`SPI_cursor_open_internal()`和`PortalRunSelect()`看出来。)

## 可更新性

通过在*查询*的末尾添加一个`FOR UPDATE`或`FOR SHARE`，可以使游标可更新。这将在更新/读取一行时，分别给`SELECT`语句一个来自其他并发事务的排他锁或共享锁。当我们想要使用`{UPDATE | DELETE} … WHERE CURRENT OF <cursor_name>`通过游标更新或删除原始表中的行时，它们非常有用。

请记住，游标可更新性与 SQL:2014 标准略有不同。在 PostgreSQL 中，对原始表的任何更新/删除，甚至那些使用`{UPDATE | DELETE} … WHERE CURRENT OF <cursor_name>`语句生成的*到*不敏感游标，在同一游标中都是不可见的。这是因为快照是在游标打开时创建的，如上所述，远在更改发生之前。

## 可滚动性

如果光标用`SCROLL`指定，则可以用于向前(即下一行)和向后(即上一行)检索行。

这意味着执行器必须能够反向获取行。根据查询计划，可能需要在查询树的顶部附加一个物化节点来支持向后提取。Materialize 节点将所有提取的行存储到一个名为 tuplestore 的临时存储中，这允许以后再次访问前面的行。

如果提取的行的总大小低于指定值，tuplestore 会将它们存储到内存数组中。否则，它会将行存储到磁盘上的一个临时文件中。除了支持向后获取，它还允许在查询结果中进行有效的随机访问，因为它避免了执行程序扫描大量行以到达所需行的需要。

当指定了`WITH HOLD`时，在创建游标的事务成功提交后，可以继续引用和使用游标。在声明 holdable 游标时，查询将运行到完成，并将行转储到 tuplestore 中。每当在下一个事务中发出一个`FETCH`命令时，它将从 tuplestore 中检索一行。

在内部，每当通过`DECLARE`打开光标时，就会创建一个新的门户。门户只是对光标执行状态的抽象，将根据*查询*采取不同的执行策略。一些相关的例子是:

1.  如果*查询*包含一个单独的`SELECT`语句，那么执行程序将会运行，并且每次发出`FETCH`命令都会返回一个新行。
2.  如果*查询*包含一个带有`RETURNING`子句的`INSERT` / `UPDATE` / `DELETE`查询，执行程序将执行更新/插入/删除操作直到完成，并将查询结果转储到 tuplestore 中。当发出`FETCH`命令时，将从 tuplestore 中读取该行。
3.  如果*查询*包含实用程序语句(例如`EXPLAIN`或`SHOW`)，它将运行该语句直到完成，并将查询结果转储到 tuplestore 中。类似地，当发出`FETCH`时，将从 tuplestore 中读取 row。

**引用的源代码文件:**

*   [PostgreSQL 源代码:src/back end/executor/execmain . c 源文件](https://doxygen.postgresql.org/execMain_8c_source.html)
*   [PostgreSQL 源代码:src/backend/executor/spi.c 源文件](https://doxygen.postgresql.org/spi_8c_source.html)
*   [PostgreSQL 源代码:src/include/utils/portal.h 源文件](https://doxygen.postgresql.org/portal_8h_source.html)
*   [PostgreSQL 源代码:src/backend/tcop/pquery.c 源文件](https://doxygen.postgresql.org/pquery_8c_source.html#l00430)
*   [PostgreSQL 源代码:src/back end/utils/sort/tuple store . c 源文件](https://doxygen.postgresql.org/tuplestore_8c_source.html#l01233)