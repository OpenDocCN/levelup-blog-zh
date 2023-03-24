# MySQL 中的索引

> 原文：<https://levelup.gitconnected.com/indexes-in-mysql-9ad4c8c0b223>

![](img/f2ab7191aba860d7e09117e03dc3c3a7.png)

## 数据库索引是一种数据结构，可以提高表中搜索操作的速度。最大的数据在表中，最慢的是查找具体数据。

> **目录:**
> 
> **1。一般指数**
> 
> **2。索引类型**

## 1.我们为什么要使用索引？

索引是一种表，它保存一个主键或索引字段以及指向实际表中每条记录的指针。用户看不到索引，它们只是用来加快查询速度，并将被数据库搜索引擎用来快速定位记录。MySQL 将索引用于多种目的:

*   更快地运行带有`WHERE`子句的查询。
*   当连接其他表时，从其他表中快速检索行。
*   查询优化:分组依据，排序依据优化。
*   选择行时快速检索行。
*   限制
*   最大()，最小()

## 2.索引类型

MySQL 有两个存储引擎:InnoDB 和 MyISAM。你可以通过[此链接](https://www.knownhost.com/wiki/developmental/mysql-myisam-innodb)了解更多关于他们的不同之处。

## 2.1.索引的创建。

在 MySQL 中有 3 种不同的方法来创建索引。

1.  使用`CREAT INDEX`关键字:

```
CREATE INDEX index_name
ON table_name(index_column_1,index_column_2,...);
```

2.使用`CREATE TABLE`关键字创建表格时:

```
CREATE TABLE table_name(
   column_1 CHAR(30) NOT NULL,
   INDEX (column_2));
```

3.使用`ALTER TABLE`关键字:

```
ALTER TABLE table_name ADD INDEX (column_1, column_2);
```

## 2.2.索引的类型。

MySQL 中有 6 种类型的索引。它们每个都有不同的用途。

## 独一无二

**唯一索引**是一种所有列值都必须唯一的索引。在单列唯一索引中，被索引的列中不能有重复的值。在多列唯一索引中，值可以在单个列中重复，但每行中列值的组合必须是唯一的。使用唯一索引来防止重复值，并且通常在创建表后定义索引。下面是如何创建唯一索引:

```
1\. CREATE **UNIQUE INDEX** index_name
   ON table_name(index_column_1,index_column_2,…);or2\. CREATE TABLE table_name(
   column_1 CHAR(30) NOT NULL,
   **UNIQUE INDEX**(column_2));or3\. ALTER TABLE table_name ADD **UNIQUE INDEX**(column_1, column_2)
```

## 主键

**主键**是唯一的索引，其中没有值可以为空。每一行都必须有该列或列组合的值。因此，您通常会在尽可能少的列上定义主键，并且大多数情况下，主键将在单个列上设置。此外，一旦设置，主键中的列值就不能更改。下面是如何创建主键索引。

```
1\. CREATE TABLE table_name(
   column_1 datatype **PRIMARY KEY** );or2\. CREATE TABLE table_name(
   column_1 CHAR(30) NOT NULL,
   column_2 CHAR(30) NOT NULL,
   **PRIMARY KEY**(column_1, column_2));or3\. ALTER TABLE table_name ADD **PRIMARY KEY**(column_1, column_2);
```

## 2.2.3 简单、常规、正常

**简单、常规或普通索引**是一种值不需要唯一并且可以为空的索引。添加它们只是为了帮助数据库更快地搜索内容。下面是如何创建普通索引。

```
1\. CREATE **INDEX** index_name
   ON table_name(index_column_1,index_column_2,...);or2\. CREATE TABLE table_name(
   column_1 CHAR(30) NOT NULL,
   **INDEX**(column_2));or3\. ALTER TABLE table_name ADD **INDEX**(column_1, column_2);
```

## 全文

顾名思义，一个**全文索引**用于全文搜索。有时，您希望找到包含某个单词或一组单词的文本块，或者您可能希望在较大的文本块中找到某个子串。广泛应用于电子商务和搜索引擎。`FULLTEXT`索引仅支持`[InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)`和`[MyISAM](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html)`表，并且只能包括`[CHAR](https://dev.mysql.com/doc/refman/8.0/en/char.html)`、`[VARCHAR](https://dev.mysql.com/doc/refman/8.0/en/char.html)`和`[TEXT](https://dev.mysql.com/doc/refman/8.0/en/blob.html)`列。下面是如何创建全文索引。

```
1\. CREATE **FULLTEXT** INDEX index_name
   ON table_name(index_column_1,index_column_2,...);or2\. CREATE TABLE table_name(
   column_1 **TEXT**,
   **FULLTEXT**(column_1));or3\. ALTER TABLE table_name ADD **FULLTEXT** (column_1, column_2);
```

## 空间

空间索引是 MySQL 中的新功能，并没有被广泛使用。MySQL 允许在`NOT NULL`几何值列上创建`SPATIAL`索引。`SPATIAL INDEX`创建一个 R 树索引。对于支持空间列的非空间索引的存储引擎，该引擎会创建 B 树索引。空间值上的 B 树索引对于精确值查找很有用，但对于范围扫描则没有用。优化器可以使用在受 SRID 限制的列上定义的空间索引。我计划在不久的将来写一篇关于这个索引的全新文章。下面是如何创建空间索引。

```
1\. CREATE **SPATIAL** INDEX index_name
   ON table_name(index_column_1);or2\. CREATE TABLE table_name(
   column_1 **GEOMETRY NOT NULL SRID 4326,**
   **SPATIAL** INDEX(column_2));or3\. ALTER TABLE table_name ADD **SPATIAL** INDEX(column_1, column_2);
```

## 下降

一个**降序索引**只有 MySQL 8+版本才有，是一个以逆序存储的常规索引。当你对最近添加的数据进行查询时，这是很有帮助的，就像你可能会显示你最近的五篇文章或对你所有文章的十条评论。以前，可以逆序扫描索引，但是性能是个问题。可以按照向前的顺序扫描降序索引，这样效率更高。当最有效的扫描顺序混合了某些列的升序和其他列的降序时，降序索引还使优化器可以使用多列索引。下面是如何创建降序索引。

```
1\. CREATE INDEX index_name
   ON table_name(index_column_1 **DESC**);or2\. CREATE TABLE table_name(   
   column_1 INT, 
   column_2 INT,   
   INDEX asc_index_1 (column_1 **ASC**, column_2 **ASC**),   
   INDEX desc_index_1 (column_1 **DESC**, column_2 **DESC**));or3\. ALTER TABLE table_name ADD INDEX(column_1 **DESC**, column_2 **ASC**);
```

这就是今天的文章。希望你喜欢:)。