# 如果你写 N+1 个查询，你就不是软件工程师

> 原文：<https://levelup.gitconnected.com/youre-not-a-software-engineer-if-you-re-writing-n-1-queries-3421910c0f14>

了解 N+1 查询，为什么以及如何避免它们

![](img/a8155798a8fea487a3a948d60ee572e1.png)

托拜厄斯·菲舍尔在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

好的。你仍然是一名软件工程师，但是你编写的低效查询很容易修复。这是几乎每个新开发人员都会写的一个模式，包括我自己，当我开始我的第一个 Ruby on Rails 开发角色时。

它之所以如此恶毒，是因为当你的数据规模很小时，负面影响是最小的。但是，由于它的成本随着数据的增长而线性增长，当你的平台开始接纳大量新用户时，它不可避免地会达到破坏应用程序的程度。

我们将遍历假设几种不同环境(SQL、Python、Rails)的示例，并向您展示如何纠正它们。

1)纯 SQL
2) Python
3) Rails

# 什么是 N+1 查询？

简单地说，就是当查询中的每个加载记录执行自己的查询时。

因此，如果您在数据库中加载一个所有文章的列表，然后对每篇文章执行一个单独的查询来加载该文章的作者，这将是 N+1。

首选的解决方案是在单个事务中将作者的文章与数据库级别的文章结合起来。

因为 ORM(对象关系映射)提供了如此多的抽象，并且喜欢延迟加载对象，所以经常很容易不小心做到这一点。

# SQL 示例

下面是上面的 SQL 示例。我们执行一个查询来检索文章。

```
SELECT * FROM articles
```

然后，对于每篇文章，我们执行另一个查询来获取文章的作者。因此，如果有 25 篇文章，那么第二个查询将运行 25 次。

```
SELECT * FROM authors WHERE authors.id = article.author_id
```

而不是以上，我们应该这样做。

```
SELECT 
  * 
FROM articles
LEFT JOIN authors ON authors.id = articles.author_id
```

一次加载所有内容。

当然，当编写没有其他约束或 cruft 的纯 SQL 时，很容易发现这一点，所以让我们看看更复杂的例子。

# Python 示例

让我们重写上面的代码，但是在实际的 Python 代码中，你可以在 Jupyter 笔记本或 Python 应用程序中运行。

```
import psycopg2 as pgconn = psycopg2.connect(dbname='mydb')
cur = conn.cursor()# Get all articles
cur.execute("""SELECT id,title from articles""")
articles = cur.fetchall()# For each article, get the author
for a in articles:
    cur.execute("""
      SELECT name from authors where authors.id = {}""".format(a[0]) 
    )
    author = cur.fetchall()[0]
    print(a[1], author)cur.close()
conn.close()
```

上面我们导入了`pyscopg2`,它允许我们在 python 中执行 SQL，打开到数据库的连接，并执行一个查询来获取每篇文章的`id`和`title`。然后，我们遍历这些文章，对每篇文章执行另一个查询来加载该文章的作者。

我们应该写这个。

```
import psycopg2 as pgconn = psycopg2.connect(dbname='mydb')
cur = conn.cursor()# Get articles and their authors at the same time
cur.execute("""
    SELECT 
        articles.id,
        articles.title, 
        authors.name
    from articles 
    left join authors on authors.article_id = articles.id
""")
articles = cur.fetchall()# for each article, print the article title and author name
for a in articles:
    print(a[1],a[2])cur.close()
conn.close()
```

在这里，我们同时检索文章及其作者。

有时，您实际上想要像这样(或成批)加载数据，特别是当加载的对象非常大并且可能超过您的机器的内存时。这在数据管道或训练机器学习模型时更为普遍。

# Rails 示例

在框架环境中，甚至更容易意外地编写 N+1 个查询。如果您没有 Rails 背景知识，请注意控制器包含支持视图的逻辑。

```
# Controller
@articles = Article.all# View
@articles.each do |a|
  <%= a.title %>
  <%= a.author.name %>
```

上述操作将导致执行以下查询。

```
SELECT "articles".* FROM "articles"

SELECT "authors".* FROM "authors" WHERE "articles"."id" = $1 LIMIT 1 [["id", 1]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 2]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 3]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 4]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 5]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 6]]
SELECT "authors".* FROM "authors" WHERE "authors"."id" = $1 LIMIT 1 [["id", 7]]
...
```

这太可怕了！我们应该写信的。

```
# Controller
@articles = Article.includes(:author)# View
@articles.each do |a|
  <%= a.title %>
  <%= a.author.name %>
```

这导致下面的 SQL 被执行。

```
SELECT  "articles".* FROM "articles"SELECT "authors".* FROM "authors" WHERE "authors"."article_id" IN (1, 2, 3,...)
```

尽管这不是一个单独的查询，但这是一个显著的改进，尤其是当第一个查询返回了数千条记录时。

# 结论

N+1 查询很容易做，并且(通常)很容易修复。但是当记录的数量很大时，对性能的影响会很大，所以即使你的应用很小，也最好避免这种查询模式，这样当你扩展时就不会成为问题。

如果你有其他处理 N+1 的方法，我很乐意在评论中分享。