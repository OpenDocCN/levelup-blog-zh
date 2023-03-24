# 如何在 PostgreSQL 中启用字符串相似性功能

> 原文：<https://levelup.gitconnected.com/how-to-enable-string-similarity-features-in-postgresql-d40700c2d6bd>

## 避免“函数相似性(字符变化，未知)不存在”的错误

![](img/f4b9582c2d89c0497c170aa3537f5429.png)

罗斯·乔伊纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在数据库级别比较字符串是一个很好的特性，特别是考虑到它比在应用程序级别更快更有效。

要利用 PostgreSQL 中的字符串相似性特性，首先必须启用它们。现在让我们来学习如何做这件事。

# pg_trgm PostgreSQL 模块为什么有用

PostgreSQL 9.1 引入了函数和操作符来通过`pg_trgm`模块比较字符串。如[官方文档](https://www.postgresql.org/docs/14/pgtrgm.html)中所述，“`pg_trgm`模块提供了基于三元组匹配确定字母数字文本相似性的函数和操作符，以及支持快速搜索相似字符串的索引操作符类。”

`pg_trgm`提供的字符串比较功能包括:

*   `similarity(text, text) → real`
    返回一个从 0 到 1 的数字，以表示两个参数之间的相似程度。

同样，比较字符串的运算符包括:

*   如果其两个参数的相似度大于由`[pg_trgm.similarity_threshold](https://www.postgresql.org/docs/current/pgtrgm.html#id-1.11.7.42.7)`设置的当前相似度阈值，则`text % text → boolean` 返回`true`。

你可以通过`pg_trgm` [在这里](https://www.postgresql.org/docs/14/pgtrgm.html#id-1.11.7.42.6)找到 PostgreSQL 支持的所有字符串相似函数和操作符的列表。

如果没有安装`pg_trgm`模块，每当您试图在查询中使用相似性函数或运算符时，都会出现错误。例如，让我们考虑以下查询:

```
SELECT similarity("table"."field"::text, %s::text) AS "similarity", "User".* 
FROM "table" 
WHERE similarity > .5 ORDER BY "similarity" DESC LIMIT 10
```

如果没有正确安装`pg_trgm`模块，PostgreSQL 会产生以下错误:

```
"function similarity(character varying, unknown) does not exist"
```

# 启用 pg_trgm 模块

由于`pg_trgm`被认为是“可信”模块，任何拥有`[CREATE](https://www.postgresql.org/docs/current/ddl-priv.html)` [权限](https://www.postgresql.org/docs/current/ddl-priv.html)的用户都可以安装它。要这样做并在 PostgreSQL 中启用类似字符串的功能，请启动以下 SQL 命令:

```
CREATE EXTENSION pg_trgm;
```

您可能会得到以下错误:

```
ERROR: could not open extension control file "…/extension/pg_trgm.control": No such file or directory
```

在这种情况下，你需要用下面的命令在你的 Ubuntu 服务器上安装`pg_trgm`模块:

```
sudo apt install postgresql-contrib
```

重新运行前面提到的`CREATE`查询，现在应该可以工作了。

现在，您可以利用 PostgreSQL 提供的字符串相似性特性，忘记“函数相似性(字符变化，未知)不存在”的错误！

# 结论

在本文中，您看到了什么是`pg_trgm`,它为什么有用，以及如何在 PostgreSQL 中安装它。`pg_trgm`模块使您能够在 PostgreSQL 中使用几个字符串相似性函数和操作符。这特别有用，因为在数据库级别比较字符串的相似性比在应用程序级别快得多。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)