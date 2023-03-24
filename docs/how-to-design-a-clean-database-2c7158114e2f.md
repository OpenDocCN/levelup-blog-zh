# 如何设计一个干净的数据库

> 原文：<https://levelup.gitconnected.com/how-to-design-a-clean-database-2c7158114e2f>

## 保持名称简单一致的 18 个最佳实践

![](img/3d794ee5728935455220a406145a4ce2.png)

照片由[卢卡斯](https://www.pexels.com/@goumbik?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[派克斯](https://www.pexels.com/photo/person-writing-on-notebook-669615/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

无论你是哪种类型的开发人员，我们偶尔都会遇到这样一种 API，它返回数据的方式让我们不必花太多时间去理解它。

但是产生这种干净一致的结果需要时间、努力和经验。今天，我们将朝着设计一个干净的数据库迈出第一步。

我们尽量简短扼要。我们开始吧

## 一些术语

表:这是一组数据

**主键:**这是表的唯一标识符

**属性:**表示您的数据的属性。例如，`name`是一个`user`的属性。

**数据类型:**数据类型代表数据的各种类型。例如-字符串、整型、时间戳等。

# 1.单词之间应该用下划线隔开

当你的属性名多于一个单词时，用`snake_case`分开。不要使用`camelCase`或任何其他案例来保证一致性。

## 严重的

```
wordcount or wordCount
```

## 好的

```
word_count
```

## 理由

*   提高可读性
*   名称可以变得更加独立于平台

# 2.数据类型不应该是名称

永远不要用数据类型作为列名。这种情况主要发生在时间戳参数上。给它起一个有意义的名字。

## 严重的

```
timestamp or text
```

## 好的

```
created_at or description
```

## 理由

*   使用数据类型会在应用程序的另一端造成混乱。
*   给定一个合适的名称可以为参数的使用提供更多的上下文。

# 3.属性名应该是小写的

不要使用大写的属性名。

## 严重的

```
Description
```

## 好的

```
description
```

## 理由

*   这种做法避免了大写 SQL 关键字的混淆
*   它可以提高打字速度

# 4.写完整的单词

不要为了空间或任何其他逻辑而试图缩短列名。尽可能明确。

## 严重的

```
mid_name
```

## 好的

```
middle_name
```

## 理由

这条规则促进了自文档化设计

# 5.但是使用常见的缩写

规则 4 的一个例外是当你有一个广泛使用的缩写。在这种情况下，选择短的。

## 好的

```
i18n
```

但是如果你发现自己很困惑，就用全名吧。这是你对未来的投资。

# 6.避免在列名中使用数字

信不信由你，我已经看够了。列名中不要有数字。

## 严重的

```
address1 , address2
```

## 好的

```
primary_address, secondary_address
```

## 理由

这是您的正常化非常差的标志。所以尽量避免。

# 7.使用简短的表名

命名表时要非常小心，因为长表名会对将来产生巨大的负面影响。

## 严重的

```
site_detail
```

## 好的

```
site
```

## 理由

当您创建关系列和链接表时，简短的表名会对您有所帮助。

# 8.注意保留字

每个数据库都有一些保留字。学习它们，避开它们。

## 严重的

```
user lock table etc
```

## 一些流行数据库的保留字列表

*   [Postgres](https://www.postgresql.org/docs/9.3/sql-keywords-appendix.html)
*   [MySQL](https://dev.mysql.com/doc/refman/5.7/en/reserved-words.html)
*   [甲骨文](https://docs.oracle.com/database/121/SQLRF/ap_keywd.htm#SQLRF022)

# 9.表格的单数名称

总是尽量对表使用单数名称。这是一个有争议的问题，不同的人有不同的看法。但是坚持一个。

## 严重的

```
users and orders
```

## 好的

```
user and order
```

## 理由

*   这促进了主键和查找表的一致性
*   多元化有时会很棘手。所以使用单数表名可以使编程更容易。

# 10.链接表应该按字母顺序排列

创建连接表时，按字母顺序连接两个表的名称。

## 严重的

```
book_author
```

## 好的

```
author_book
```

# 11.单数列名

通常，这是最佳实践，除非您违反了数据规范化规则。

## 严重的

```
books
```

## 好的

```
book
```

# 12.主键名

如果是单列，那么应该命名为`**id**`

```
CREATE TABLE order (
  id            bigint PRIMARY KEY,
  order_date    date NOT NULL
);
```

# 13.外键名

它应该是另一个表和引用字段的名称。例如，如果你在你的`team_member`表中引用一个`person`，那么你可以这样做。

```
CREATE TABLE team_member (
  person_id     bigint NOT NULL REFERENCES person(id),
);
```

# 14.不要用类型作为列名的后缀

用数据类型作为列名的后缀是没有意义的。避免这样做。

## 严重的

```
name_tx
```

## 好的

```
name
```

# 15.索引应该同时具有表名和列名

如果您正在创建索引，那么让表名后跟您所引用的列名

```
CREATE TABLE person (
  id          bigserial PRIMARY KEY,
  first_name  text NOT NULL,
  last_name   text NOT NULL,
);CREATE INDEX person_ix_first_name_last_name ON person (first_name, last_name);
```

# 16.日期类型列名

在日期类型的列名后面加上`_on`或`_date`。

例如，如果您有一个存储更新日期的列，那么这样做，

## 好的

```
updated_on or updated_date
```

# 17.日期-时间类型列名

如果你的列名有时间，那么在它们后面加上`_at`或`_time`。

例如，如果您想存储订单时间，那么

## 严重的

```
ordered
```

## 好的

```
ordered_at or order_time
```

# 18.布尔类型列名

如果您有布尔类型的列名，那么在它们前面加上前缀`is_`或`has_`。

## 好的

```
is_admin or has_membership
```

# 最后的话

如果你已经在做一个项目，坚持项目已经遵循的惯例。因为

> 唯一比坏约定更糟的是多重约定

但是如果你是从零开始学习或设计一个数据库，记住这些规则会让你走得更远。

你有什么想法？有你不同意的规则吗？我非常乐意在评论区进行一些富有成效的对话！

祝您愉快！:D

**通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) **或我的** [**个人网站**](https://www.mohammadfaisal.dev/) **与我取得联系。**

## 参考

*   [https://launchbylunch . com/posts/2014/Feb/16/SQL-naming-conventions/](https://launchbylunch.com/posts/2014/Feb/16/sql-naming-conventions/)
*   [https://justinsomnia . org/2003/04/essential-database-naming-conventions-and-style/](https://justinsomnia.org/2003/04/essential-database-naming-conventions-and-style/)