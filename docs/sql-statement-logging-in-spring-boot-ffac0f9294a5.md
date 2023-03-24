# Spring Boot 的 SQL 语句日志记录

> 原文：<https://levelup.gitconnected.com/sql-statement-logging-in-spring-boot-ffac0f9294a5>

在开发 spring boot 应用程序时，您可能会遇到各种各样的错误，尤其是在使用数据库时。在这些 Spring boot 应用程序中，我们通常使用 JPA 来简化数据库管理工作。当出现内部服务器错误之类的错误时，您肯定需要查看日志来找到问题。在这篇文章中，您可以学习如何从 Springboot 记录 Hibernate/JPA SQL 语句。

![](img/589b437c0f02cd26228cec2f0c999e54.png)

检查 SQL 日志

有很多方法可以做到这一点。

## 1)标准输出

下面是记录 SQL 语句的最简单的方法。您只需将这一行添加到 *application.properties* 文件中:

```
spring.jpa.show-sql=true
```

如此简单。对吗？让我们看看输出。如果你使用的是 tomcat，你可以从*/Tomcat/logs/catalina . out*中找到日志

```
Hibernate: insert into program (id, created_at, title, image_url) values (?, ?, ?, ?)
```

你可能会注意到这里有些不寻常。是的，它没有显示我们发送的参数。这些用“？”表示马克斯。不过，我主要是在 [ScholarX](https://github.com/sef-global/scholarx) 和 [Academix](https://github.com/sef-global/sef-core) 在 [SEF](http://sefglobal.org/) 开发后端的时候用这个方法。

如果你想美化这个陈述，特别是当它涉及到长的陈述时，你可以添加以下内容:

```
spring.jpa.properties.hibernate.format_sql=true
```

这将格式化语句，并通过添加一些缩进来美化它，就像这样。所以你会更清楚。

```
Hibernate: 
    insert 
    into
        program
        (id, created_at, title, image_url) 
    values
        (?, ?, ?, ?)
```

## 2)通过记录器

另一种方法是通过记录器。通过使用记录器，我们还可以跟踪语句参数。同样，我们只需通过添加以下内容来更新*应用程序.属性*:

```
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

这将在更详细的视图中记录带有参数的 SQL 查询:

```
2020–10–24 00:28:26.005 DEBUG 1096 — — [io-8080-exec-12] org.hibernate.SQL : insert into program (id, created_at, title, image_url) values (?, ?, ?, ?)2020–10–24 00:28:26.039 TRACE 1096 — — [io-8080-exec-12] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [BIGINT] — [1]
2020–10–24 00:28:26.037 TRACE 1096 — — [io-8080-exec-12] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [TIMESTAMP] — [Sat Oct 24 00:28:25 IST 2020]
2020–10–24 00:28:26.037 TRACE 1096 — — [io-8080-exec-12] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [VARCHAR] — [Hello World]
2020–10–24 00:28:26.037 TRACE 1096 — — [io-8080-exec-12] o.h.type.descriptor.sql.BasicBinder : binding parameter [4] as [VARCHAR] — [hello.jpg]
```

那个“？”痕迹还在。但是你可以看到参数显示在下面。你也可以使用我们之前用过的美化命令(这看起来很复杂。所以我一般不用这个)

今天到此为止。我希望你学到了新东西。向我们的 [Github repos](https://github.com/sef-global) 投稿。还有 Hacktoberfest 快乐！