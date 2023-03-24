# 面向 Oracle 数据库的开源 Python 瘦驱动程序

> 原文：<https://levelup.gitconnected.com/open-source-python-thin-driver-for-oracle-database-e82aac7ecf5a>

# 一个新的主要 Python cx_Oracle 驱动程序发布可用，并带有一个全新的名称 python-oracledb。

![](img/ad2482b256222f3a4ef31ecf4a3f7d2f.png)

*照片由* [*火星文图片*](https://unsplash.com/@rovenimages_com?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上* [*油彩*](https://unsplash.com/s/photos/new-release?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

**Python-oracledb 1.0** 是 Oracle 非常流行的 Python 驱动 cx_Oracle 8.3 的升级版，Python 应用使用它来连接 Oracle 数据库。

新版本引入了重要的新功能。**默认情况下，它现在是一个瘦驱动程序，不需要 Oracle 客户端库**。这使得当 Oracle 客户端库不可用时(例如苹果 M1、Alpine Linux 或物联网设备)，或者当客户端库不容易安装时(例如一些云环境)，可以使用 python-oracledb。当然，您可以从许多其他环境轻松连接到 Oracle 数据库，包括我们最喜欢的 Oracle Linux。

cx_Oracle 驱动程序项目是由 Anthony Tuininga 于 1998 年针对 Python 1.5 和 Oracle 8i 启动的。(仿真陈述:名字中的“cx”是他当时工作的公司的缩写)。从那以后，Python 和 Oracle 世界发生了很多事情——包括 Anthony 加入 Oracle。他和 Oracle 数据库的驱动程序团队现在已经在驱动程序的现代化方面投入了大量资金，为您带来了这一重要版本。感谢所有参与其中的人，感谢使用 cx_Oracle 并为其做出贡献的 Oracle 社区成员。我相信随着驱动程序的不断发展，您将会继续接受和使用它。这是一个令人兴奋的旅程，为您带来了新版本的优雅，一种我们喜欢的优雅。

这里有一个讨论 python-oracledb 介绍的视频。

# PYTHON-ORACLEDB 需要知道

*   Python-oracledb 是我们流行的 cx_Oracle 驱动程序的新名称。
*   简单且易于安装—小于 15 MB(包括其 Python 包依赖项):`pip install oracledb`
*   默认情况下，该驱动程序现在是瘦驱动程序。
*   默认情况下，它不需要 Oracle 客户端库，因此您可以在许多平台上使用它，甚至可以在一些没有 Oracle 客户端库的平台上使用。
*   它具有符合 [Python 数据库 API v2.0 规范](https://www.python.org/dev/peps/pep-0249/)的全面功能，增加了许多功能，但只排除了一些功能。
*   “厚”模式可以通过应用程序调用来可选地启用。该模式具有与 cx_Oracle 类似的功能，并支持扩展 Python DB API 的 Oracle 数据库特性。要使用此模式，必须单独安装广泛使用和测试的 Oracle 客户端库，例如 Oracle Instant Client。
*   SQLAlchemy、Pandas、Django 等框架和库，通过添加[几行名称映射代码](/using-python-oracledb-1-0-with-sqlalchemy-pandas-django-and-flask-5d84e910cb19)，可以立即在瘦或厚模式下使用 python-oracledb。将来当框架和 ORM 维护者添加等效的代码来使用新的名称空间时，这就没有必要了。(更新:在 [SQLAlchemy 2.0](https://medium.com/oracledevs/using-the-development-branch-of-sqlalchemy-2-0-with-python-oracledb-d6e89090899c) 中增加了对新名称空间的直接支持)。

# PYTHON-ORACLEDB 快速入门

从 PyPi 安装:

```
python -m pip install oracledb
```

在文本和 URL 中，我们将驱动程序称为“python-oracledb”以帮助识别它，但在 python 代码中，模块本身只是“oracledb”。

应用程序使用熟悉的 Python DB API:

```
import oracledbconnection = oracledb.connect(user='scott', password=mypw, dsn='localhost/oraclepdb1')
with connection.cursor() as cursor:
  for row in cursor.execute('select city from locations'):
      print(row)
```

注意`oracledb.connect()`需要命名的“关键字”参数，符合 Python DB API 规范。

详见[安装说明](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)。

# 从 CX ORACLE 升级到 PYTHON-ORACLEDB

要将现有 cx_Oracle 代码升级到 python-oracledb:

1.安装新模块:

```
python -m pip install oracledb
```

2.导入新的接口模块:

```
import oracledb as cx_Oracle
```

3.确保在对`connect()`、`Connection()`和`SessionPool()`的调用中使用命名参数。

例如，这个:

```
c = oracledb.connect("un", "pw", "cs")
```

需要更改为:

```
c = oracledb.connect(user="un", password="pw", dsn="cs")
```

或者执行以下操作(尽管如果您想在密码中使用“/”或“@”，我不建议这样做):

```
c = oracledb.connect("un/pw@cs")
```

这符合 Python DB API 规范。

4.移除对`init_oracle_client()`的调用，因为这将开启厚模式——当然，除非你想使用厚模式。在这种情况下，您需要给`init_oracle_client()`添加一个呼叫。

有关更多详细信息，请参见文档[从 cx_Oracle 8.3 升级到 python-oracledb](https://python-oracledb.readthedocs.io/en/latest/user_guide/appendix_c.html#upgrading-from-cx-oracle-8-3-to-python-oracledb) 。另请参见博文[将您的 Python 应用从 cx_Oracle 8 升级到 python-oracledb](https://medium.com/@sharad-chandran/upgrade-your-python-apps-from-cx-oracle-8-to-python-oracledb-2bfeaee7aac9) 。

# 其他新功能

大量的工程设计重点放在“瘦”模式上，但是在 cx_Oracle 8.3 特性集的基础上，python-oracledb 1.0 中还有一些很棒的功能。这些特征包括一些大的和一些小的:

*   智能 IDE 代码完成的代码注释。这要归功于用 Python 编写的一个新的顶层驱动层。
*   运行在苹果英特尔和 M1 芯片组上的 macOS Universal 2 二进制文件。更新:Linux ARM 二进制文件现在也可以使用了。
*   一个新的`oracledb.defaults`对象，用于设置一些通用的应用程序范围的默认值。例如，属性`oracledb.defaults.fetch_lobs`可以设置为`False`,这样 LOB 就可以获取字符串或字节，而不是 LOB 对象。这消除了实现输出类型处理程序的需要。
*   新的 ConnectParams 和 PoolParams 类，可以分别传递给连接创建和池创建函数。这使得封装连接和连接池设置变得更加容易。
*   使用 DRCP 连接的连接池可以在连接池创建期间指定“连接类”和“纯度”。这使得在初始化期间定义应用程序行为变得更加容易。
*   两阶段提交(TPC)支持。TPC 的全新 API 使分布式事务变得简单。
*   高级排队(AQ)现在支持收件人列表。
*   语句可以从语句缓存中排除，这样不经常运行的语句就不会挤出经常使用的语句
*   更有用的错误信息在重要的地方，包括在连接期间和数据绑定。
*   `_Error`类现在包含一个`full_code`属性，该属性包含错误前缀和错误号，例如‘ORA-01476’
*   在每个进程中可以多次调用`oracledb.init_oracle_client()`函数，这样应用程序就不需要只执行一次调用。
*   添加了一个类似于`connection.ping()`的轻量级`connection.is_healthy()`方法，但是没有对数据库执行完整的往返验证。

有关更多增强功能，请参见[发行说明](https://python-oracledb.readthedocs.io/en/latest/release_notes.html#releasenotes)。

# 表演

我们对 python-oracledb 瘦模式的性能特点非常满意。

查看一些原子测试结果，其中我们获取了 50，000 行 9 列的给定类型，使用默认的`arraysize`值:

*   VARCHAR2 列:python-oracledb 瘦模式比 cx_Oracle 快 12%(运行时间),占用的 CPU 少 23%
*   数字列:python-oracledb 瘦模式比 cx_Oracle 快 19%(运行时间),占用的 CPU 少 25%

使用`executemany()`插入 500，000 行 10 列进行批量插入，分 5 批插入，每批 100，000 行(即调用`executemany()` 5 次):

*   VARCHAR2 列:python-oracledb 瘦模式比 cx_Oracle 快 40%,使用的 CPU 少 48%
*   数字列:python-oracledb 瘦模式比 cx_Oracle 快 28%,使用的 CPU 少 15%

厚模式数字更接近 cx_Oracle。我们将继续努力提高性能。更新:查看这个帖子: [Python-oracledb 瘦模式对象性能](https://medium.com/oracledevs/python-oracledb-thin-mode-object-performance-8a230fe2bf4)。

注意由于对 cx_Oracle 代码进行了一些重构，python-oracledb 中一些现有应用程序的运行时性能和负载概况可能会发生变化，具体取决于您的应用程序。一个影响是由于 Python 臭名昭著的 GIL。(移除 GIL 的 Python 社区项目可能不会在 Python 3.11 中登陆，但在中已经有了其他性能改进[)。](https://docs.python.org/3.11/whatsnew/3.11.html)

# 未来

正如我们在以前的 cx_Oracle 名称空间下所做的那样，我们将继续增强和维护 python-oracledb。我们已经有了一个很长的愿望清单，特别是对瘦模式的增强。清单上一项是支持 AsyncIO，这是一个常见的请求。现在我们有了一个瘦模式，这变得可行，虽然不是微不足道的。

在 cx_Oracle 名称空间下，支持新 Python 版本的错误修复版本和二进制包将在有限的时间内继续，大约两年。根据您的反馈，最终的截止日期将在稍后决定。我们知道你们中的许多人都有很大的代码库，在使用升级后的驱动程序之前，你们会想要对其进行评估。或者您可以选择仅对新项目使用 python-oracledb。对任何特别关键的 Oracle 数据库新特性的支持也可能会在 cx_Oracle 中实现，但目前还没有。

您以前都成功地浏览过 cx_Oracle 的主要版本，这个版本也不例外:有很棒的新特性、一些小的变化和一些不赞成的地方。

通过电子邮件或在 [GitHub 讨论页面](https://github.com/oracle/python-oracledb/discussions)上发帖，让我们了解您使用 python-oracledb 的体验。我们希望收到您的来信。

# 资源

**首页**:【oracle.github.io/python-oracledb】T4

**安装说明**:[python-oracledb.readthedocs.io/en/latest/installation.html](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)

**文档**:[python-Oracle db . readthedocs . io](https://python-oracledb.readthedocs.io/en/latest/index.html)

**问题**:[github.com/oracle/python-oracledb/discussions](https://github.com/oracle/python-oracledb/discussions)

**源代码库**:[github.com/oracle/python-oracledb](https://github.com/oracle/python-oracledb)