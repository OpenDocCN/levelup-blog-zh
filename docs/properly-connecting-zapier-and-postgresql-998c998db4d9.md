# 没有错误和失败:连接 Zapier 和 PostgreSQL

> 原文：<https://levelup.gitconnected.com/properly-connecting-zapier-and-postgresql-998c998db4d9>

## 纠正不正确文档的五步指南

![](img/db2bc8f0e8b8c39be40d56ac70db7a2b.png)

来自 [Pexels](https://www.pexels.com/photo/creative-internet-computer-display-2004161/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Markus Spiske](https://www.pexels.com/@markusspiske?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的照片

*或者是我愤怒的副标题:“又一次，文档的问题……”*

[这篇文章反映在我的博客 chrisfrew.in 上](https://chrisfrew.in/error-and-failure-free-connecting-zapier-and-postgresql/)

# Google Sheets+PostgreSQL——不错！

我正在开发一个应用程序，目前需要大量的数据库维护，因为我们正处于数据收集和清理阶段。我曾多次听说过 Zapier，甚至有一个最长时间的帐户，但从未使用过它。因此，当我听说他们有一个将 Google Sheets 连接到 PostgreSQL 的“Zap ”,创建和编辑行并将它们写入数据库时，我想我应该尝试一下。

然而，我很快被不完整和简单的错误 Zapier 文档和堆栈溢出答案引入歧途。

最后，正确的配置是五个简单的步骤。我希望这些步骤可以让你免受我所经历的痛苦，因为没有什么比糟糕的文档更让我沮丧的了，这只是浪费了其他人的时间。

* * *截至 2020 年 4 月，我采取了这些步骤来获得工作连接，当时是 PostgreSQL 的最新版本 12.2。我假设这些步骤足够基本，可以向后兼容目前支持的最早的 PostgreSQL 版本 9.5。(支持到[2021 年 2 月 11 日](https://www.postgresql.org/support/versioning/)

# 1.编辑 postgresql.conf 文件以监听所有地址

在普通的 Linux 机器上，`postgresql.conf`文件应该位于以下位置:

`/etc/postgresql/12/main/postgresql.conf`

或者，例如，如果您有 9.5 这样的旧版本:

`/etc/postgresql/9.5/main/postgresql.conf`

您也可以使用以下命令从 PostgreSQL 命令行中找到该文件路径:

`SHOW config_file;`

在`postgresql.conf`文件中，取消对`listen_addresses`行(该文件中的第一个设置)的注释，如下所示:

`listen_addresses = '*'`

这确保 PostgreSQL 正在侦听所有端口上的连接。

* * *请注意，您有一个工具，如简单防火墙，又名命令行命令`ufw`(或任何防火墙)，您将需要允许端口 5432 上的连接。(以`ufw`为例，那就是`ufw allow 5432`。您可以使用这个方便的[开放端口检查工具](https://www.yougetsignal.com/tools/open-ports/)检查您的端口是否开放。

# 2.创建您的 PostgreSQL 用户并授予权限

考虑到这个用户将被 Zapier 使用，我也将 PostgreSQL 用户命名为`zapier`。有道理对吗？注意，您不需要为这个连接创建一个 Linux 用户。(在后台，Zapier 正在使用`psycopg2`——一个用于 Python 的 [PostgreSQL 数据库适配器，它通过 PostgreSQL URI 直接连接，完全跳过 SSH 层)](https://www.psycopg.org/docs/)

注意在 [Zapier 文档](https://zapier.com/apps/mysql/help)中指定的 PostgreSQL 命令*不*工作！`'user'@'localhost'`(或任何主机或 IP)语法在 PostgreSQL 中不起作用！正确的命令如下:

以`postgres`用户(或者您的根 PostgreSQL 用户)的身份登录:

`psql -U postgres`

如果您尚未创建用户，请现在创建:

`CREATE USER zapier WITH PASSWORD 'somesuperstrongpasswordhere'`

连接到目标表所在的数据库:

`\connect your_database_here`

向您刚刚创建的 Zapier 用户授予适当的权限——在我的例子中是用户`zapier`:

`GRANT INSERT, UPDATE, SELECT ON your_table_here TO zapier;`

# 3.向 pg_hba.conf 文件添加自定义条目

这就是文档失败的地方。扎皮尔简单地说:

> 对于 PostgreSQL，您需要配置服务器接受来自远程 IP 的登录(在`pg_hba.conf`中)，并创建一个用户供 Zapier 使用。

好的，很好，谢谢你们。😞一点帮助都没有。

他们当然没有提供一个例子。嗯，我会的！

首先，您的`pg_hba.conf`文件应该位于相同的文件夹中，例如，对于版本 12:

`/etc/postgresql/12/main/pg_hba.conf`

或者以 9.5 为例:

`/etc/postgresql/9.5/main/pg_hba.conf`

同样，您总是可以通过发出以下命令直接从 PostgreSQL 命令行连接获取该文件:

`SHOW hba_file;`

正如我们在`pg_hba.conf`文件中看到的，元素的顺序写在一个注释行中，如下所示:

`# TYPE DATABASE USER ADDRESS METHOD`

Zapier 至少告诉我们它将总是从哪个 IP 访问数据库，`54.86.9.50`

如果您知道希望 Zapier 访问的表的名称，我们可以在`pg_hba.conf`中编写我们需要的行。然而，这里有一个你需要采取的关键步骤。我们想要*恰好*这个 IP 并且只想要这个 IP，所以我们必须在`/32`到 IP 的末尾。更多详情请参见`address`下的 [PostgreSQL pg_hba.conf 文档](https://www.postgresql.org/docs/9.1/auth-pg-hba-conf.html)。所以我们的`pg_hba.conf`线出现如下:

```
# Access for zapier user
host    your_table_here     zapier     54.86.9.50/32    md5
```

这里注意`host`就是字面上的`host`这个词。如果您已经为 Zapier 定义了一个不同的用户，那么您只需要修改表名`your_table_here`和`zapier`。

# 4.重新启动 PostgreSQL 实例

问题

`sudo service postgresql restart`

为了使我们在`postgresql.conf`和`pg_hba.conf`中所做的更改生效。

# 5.在 Zapier PostgreSQL 表单中填写信息

到达 Zapier 提示您输入 PostgreSQL 信息的表单后，您应该能够输入所有的服务器信息和用户凭证，连接应该马上就可以工作了！没有神秘的错误信息。

🍺干杯，

克里斯