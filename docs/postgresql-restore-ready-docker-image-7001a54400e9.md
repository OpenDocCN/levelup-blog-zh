# PostgreSQL 恢复就绪 Docker 映像

> 原文：<https://levelup.gitconnected.com/postgresql-restore-ready-docker-image-7001a54400e9>

![](img/c4635d8b8dd5cb8d2802eebc7e646c42.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Victoire Joncheray](https://unsplash.com/@victoire_jonch?utm_source=medium&utm_medium=referral) 拍摄的照片

这篇文章旨在分享我在个人项目中创建的 PostgreSQL 恢复就绪映像。很简单，只有几行字。

首先，您可以从我的 [GitHub](https://github.com/diegohordi/pgsql-restore-ready-image.git) 中克隆或下载本文中使用的 Dockerfile 和 bash 脚本。

为了构建新的映像，我们扩展了 Docker Hub 上最新的 [Postgres 映像，并复制了一个 bash 文件，其中包含创建新数据库的指令，并使用给定的备份文件恢复它。](https://hub.docker.com/_/postgres)

让我们把手弄脏吧！

## 文档文件

在第 1 行，我们扩展了[最新的官方 Postgres 映像](https://hub.docker.com/_/postgres)，从第 2 行到第 5 行，我们创建了 build-args 和环境变量，我们将在 bash 文件中使用它们来恢复数据库。在第 6 行，我们创建了卷，在第 8 行，我们将通过 build-args 传递的给定备份文件复制到创建的卷。在#10，我们将 bash 文件复制到图像的入口点。在第 11 行(如果你使用的是 Windows ),我们将 bash 文件最后一行的字符从 DOS 转换到 Unix。在第 12 行，我们授予 bash 文件正确运行的权利。最后，我们向主机公开 PostgreSQL 的默认端口 5432。

## bash 文件

在第 3 行，我们使用 postgres 用户创建了具有给定名称的数据库。在#4，我们恢复数据库，在#5，我们授予用户 postgres 对所创建的数据库的权限。第 2 行和第 6 行只是为了报告状态。

## 建立形象

要构建映像，只需从放置 Dockerfile 的目录中执行下面的代码，替换您想要的名称和标记的<tag-name>，备份文件的名称的<backup-file-name>，最后是您想要恢复的数据库的名称的<database-name>。</database-name></backup-file-name></tag-name>

为了简单起见，我建议您将备份文件放在 Dockerfile 的同一个目录中。

```
docker build -t <image-name:tag-name> . --build-arg FILE=<backup-file-name> --build-arg DBNAME=<database-name>
```

## 创建容器

要创建容器，执行下面的代码，替换您想要的名称的<container-name>，您想要用来访问数据库的端口的<port-number>，以及您的映像名称的<tag-name>。</tag-name></port-number></container-name>

```
docker run -d --name <container-name> -p <port-number>:5432 <image-name:tag-name>
```

## 访问数据库

要从您的容器的主机访问数据库，只需使用主机 **localhost** 和您在用户 **postgres** 的<端口号>中给出的端口号，无需密码。

## 结论

在这篇文章中，我分享了如何通过一个简单的恢复命令来扩展官方 Postgres 映像，允许您在容器准备就绪后立即访问数据库。

我用它来节省短时间项目的时间和资源，但是您可以扩展它并执行其他命令，例如 vacuum。

感谢您的阅读，如果您有任何问题，请联系我。=)

## 参考

[Docker 构建参考指南](https://docs.docker.com/engine/reference/commandline/build/)

[码头工人运行参考指南](https://docs.docker.com/engine/reference/commandline/run/)

[Postgres 官方形象](https://hub.docker.com/_/postgres)

[pg _ restore 参考指南](https://www.postgresql.org/docs/9.2/app-pgrestore.html)