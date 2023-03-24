# 学习基础知识并开始使用 SQLAlchemy ORM

> 原文：<https://levelup.gitconnected.com/learn-the-basics-and-get-started-with-sqlalchemy-orm-from-scratch-66c8624b069>

## 让我们以 Pythonic 的方式与数据库交互

我们已经介绍了如何用 Python 中的 SQLAlchemy 执行普通的 SQL 查询，如果您是 SQL 高手，但还不太了解 Python 或 SQLAlchemy，这会很方便。然而，一旦你足够熟悉了，你就会想要编写更多的 pythonic 代码来与数据库交互，这可以通过 SQLAlchemy**O**object**R**relational**M**apper(**ORM**)来实现。

> [ORM](https://docs.sqlalchemy.org/en/14/orm/tutorial.html) 提供了一种将用户定义的 Python 类与数据库表相关联的方法，以及将这些类(对象)的实例与它们对应的表中的行相关联的方法。

![](img/c0cd7dbe447af658bec9b9b9c8588ba4.png)

图片来自 [Pixabay](https://pixabay.com/illustrations/server-servers-data-computer-5451985/) 。

ORM 第一次使用时可能会让人不知所措，因为有很多新概念和 CRUD 操作的特殊语法。在这篇文章中，我们将介绍最常见的概念，并为常见的 CRUD 操作提供易于遵循的分步说明。在这篇文章之后，你会得到一个 ORM 的快速入门或者更新。

强烈建议先查看[关于普通 SQL 查询的帖子](https://lynn-kwong.medium.com/how-to-execute-plain-sql-queries-with-sqlalchemy-627a3741fdb1)，因为那里有关于系统设置和非常基础的概念的详细介绍，如方言、驱动程序、引擎、连接等。然而，这不是强制性的。您可以按照下面的基本步骤来设置本教程的环境。或者你可以通读代码和解释，也会学到很多东西，特别是当你想快速刷新的时候。

对于本教程，我们需要有一个本地运行的 MySQL 服务器。出于学习目的，建议使用 Docker 在本地启动一个 MySQL 容器。这样，您可以使用任何版本的 MySQL，并且可以避免潜在的端口冲突。

在使用 SQLAlchemy 连接到我们的数据库并创建一些 ORM 模型之前，我们需要安装这个包及其依赖项。建议在虚拟环境[中安装软件包](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)，这样就不会弄乱你的系统库。你可以使用 *venv* 或 *conda* 在 Python 中创建一个虚拟环境。推荐使用 [*康达*](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2) ，因为可以在虚拟环境中安装特定版本的 Python:

为本教程安装的软件包:

*   [*SQLAlchemy*](https://pypi.org/project/SQLAlchemy/) —将用于与数据库交互的主包。
*   [*SQLAlchemy-Utils*](https://pypi.org/project/SQLAlchemy-Utils/)—为 SQLAlchemy 提供各种实用函数。
*   [*sqlacodegen*](https://github.com/agronholm/sqlacodegen)—一个读取现有数据库结构并生成适当的 SQLAlchemy 模型代码的工具。
*   [*PyMySQL*](https://pypi.org/project/PyMySQL/)—SQLAlchemy 使用它来连接 MySQL 数据库并与之交互，如果您想了解为什么选择 PyMySQL，请查看[这篇文章](https://lynn-kwong.medium.com/how-to-execute-plain-sql-queries-with-sqlalchemy-627a3741fdb1)。
*   [*密码术*](https://pypi.org/project/cryptography/) —被 SQLAlchemy 用于认证。
*   [*ipython*](https://pypi.org/project/ipython/) —用于更方便地执行交互式 python 代码。

现在一切都准备好了，我们可以开始使用 SQLAlchemy ORM 了。

类似于使用 SQLAlchemy 处理普通 SQL 查询的过程，我们需要创建一个[引擎](https://docs.sqlalchemy.org/en/14/core/engines.html)，这是任何 SQLAlchemy 应用程序的起点。

要创建引擎实例，我们需要首先定义一个数据库 URL，其格式如下:

```
**dialect[+driver]://user:password@host:port/dbname**
```

*   方言——上面介绍的方言，可以是`mysql`、`postgresql`等。
*   driver —用于连接数据库并与之交互的 DBAPI 的名称，它是上面安装的 PyMySQL 库。
*   用户、口令、主机、端口、数据库名-目标数据库的用户名、口令、主机名、端口和默认数据库/模式。请在实践中针对自己的案例使用相应的。

现在让我们为 MySQL 数据库创建一个引擎，从上面的 Docker 容器开始:

[https://gist . github . com/lynnkwong/99 EC 7 db 1c 23 e 577 f 3025 B1 f 84d 005 e0a](https://gist.github.com/lynnkwong/99ec7db1c23e577f3025b1f84d005e0a)

`pool_size`和`pool_recycle`分别定义了池中有多少个连接(5)将保持打开，以及多长时间(3600 秒)后一个连接将被回收到池中。这样，我们可以控制活动连接的数量，并且不会有已经关闭的过时连接。

与普通的 SQL 查询不同，我们不需要直接创建一个[连接](https://docs.sqlalchemy.org/en/14/core/connections.html#sqlalchemy.engine.Connection)。相反，我们将创建一个[会话](https://docs.sqlalchemy.org/en/14/orm/session_api.html#sqlalchemy.orm.Session)，它是 ORM 的数据库“句柄”。会话在幕后创建和关闭连接实例，我们不需要担心细节。

创建会话的首选方式如下:

尽管我们可以直接使用`session_factory`(它产生会话实例),但是最好使用作用域会话工厂(按照惯例由`Session`调用),因为在同一个线程中调用时，它会产生相同的`Session`实例:

正如我们所看到的，作用域会话工厂在第二次被调用时“神奇地”产生了同一个`Session`实例。

此外，应该注意的是`Session`不是线程安全的，我们需要为每个线程创建一个新的`Session`，这可以通过`threading.local()`方法来实现:

有关如何用*多线程*编写并发 Python 代码的更多信息，请查看[这篇文章](https://lynn-kwong.medium.com/how-to-write-concurrent-python-code-with-multithreading-b24dec228c43)。

现在 SQLAlchemy 的“句柄”已经准备好了，我们需要创建一个 ORM 模型，它只是一个普通的 Python 类，在我们的数据库中定义一个表。

使用现代的 SQLAlchemy，ORM 模型是使用声明性系统创建的，该声明性系统使用一个公共的声明性基类:

```
>>> **from sqlalchemy.orm import declarative_base**>>> **Base = declarative_base()**
```

声明性基类`Base`作为一个注册表工作，并维护用它创建的类和表的目录。在所有通常导入的模块中，通常应该只有一个声明性基类。这个声明性基类应该被所有 ORM 类继承。重要的是，带有外键约束的表的 ORM 类必须使用相同的基类，否则将引发异常。

让我们使用与[普通 SQL 查询](https://lynn-kwong.medium.com/how-to-execute-plain-sql-queries-with-sqlalchemy-627a3741fdb1)相同的例子，并为`data`模式中的`product_stocks`表创建一个 ORM 类:

我们刚刚创建了一个 ORM 类`ProductStock`，它被映射到`product_stocks`表。这个类继承了`Base`类，所以它被编目在`Base`类的元数据中。如您所见，手动创建 ORM 类相当麻烦。一旦我们创建了数据库和表，我们将介绍一个更方便的方法。

如果您没有遵循普通 SQL 查询的教程，您的本地 MySQL 服务器上就不会有`data`数据库和`product_stocks`表。我们可以在 MySQL 控制台中创建数据库和表，但是我想向您展示一些不同的、更酷的、更 Pythonic 化的东西。

我们将用 [SQLAlchemy-Utils](https://sqlalchemy-utils.readthedocs.io/en/latest/index.html) 库创建数据库，该库为 SQLAlchemy 提供各种实用函数:

`db_url`与用来制造引擎的是同一个。它被从 *SQLAlchemy-Utils* 库中传递给`create_database()`函数，以创建目标数据库，在本例中是`data`。需要注意的是，如果要创建的数据库已经存在，那么`create_database()`会引发一个异常，因此我们需要使用同一个库中的`database_exists()`函数来检查数据库在创建之前是否已经存在。

运行上面的命令时，应该会创建`data`模式(如果还没有创建的话)。然后，我们可以使用神奇的`Base`类来创建`product_stocks`表，上面为其定义了一个 ORM 类。从技术上来说，使用了`Base`类的`metadata`，它将创建所有 ORM 类继承这个`Base`类的表:

`[MetaData.create_all()](https://docs.sqlalchemy.org/en/14/core/metadata.html#sqlalchemy.schema.MetaData.create_all)`方法创建存储在`Base`类的元数据中的所有表。默认情况下，将跳过已经存在的表，并且不会引发任何异常。

当运行上面的命令时，应该在`data`模式中创建`product_stocks`表(如果没有提前创建的话)。

正如我们上面提到的，手工从头开始创建一个 ORM 类非常麻烦。如果您已经预先创建了表(这在实践中很常见)，您可以使用 [*sqlacodegen*](https://github.com/agronholm/sqlacodegen) 库自动为表生成一个 ORM 类:

作为一名数据工程师或软件工程师，您应该非常了解 SQL 语法。对于您来说，提前创建一个表并优化项目数据库中的索引，然后使用如上所示的 *sqlacodegen* 库为其创建一个 ORM 类会更加自然。

最后，数据库、表和 orm 类都准备好了，我们可以开始用 ORM 语法执行一些基本的 CRUD 操作。

在开始之前，让我们稍微组织一下代码，并将逻辑上相关的代码移到单独的模块中，这样我们就不需要重复代码了。组织代码可在[本报告](https://github.com/lynnkwong/sqlalchemy_orm_basic)中找到。

DB 连接代码将放在一个名为`[db_connection.py](https://github.com/lynnkwong/sqlalchemy_orm_basic/blob/main/db_connection.py)`的模块中:

`Base`类和它的`metadata`放在一个名为`[orm_base.py](https://github.com/lynnkwong/sqlalchemy_orm_basic/blob/main/orm_base.py)`的独立模块中，因此它可以被其他 ORM 类使用，这将在以后的教程中介绍:

我们为`Base`类指定了一个默认模式(`data`)。这样，当使用多个模式时，如果模式是`data`，我们不需要指定一个模式。这个后面也会更详细的介绍。

表`product_stocks`的 ORM 类保存在一个单独的模块中，删除了`Base`类的代码。为了更好地组织代码，为每个单独的表创建一个单独的 ORM 模块是一个很好的实践。如果所有表的所有 ORM 类都保存在同一个文件中，将很难阅读和维护。

让我们先用 ORM 创造一个记录。。正如我们已经知道的，ORM 类被映射到一个表，而类的实例被映射到表的行。让我们创建一个`ProductStock`类的实例，并将它作为一行保存到数据库中。

此代码片段的注释:

*   ORM 类的工作方式就像普通的 Python 类一样。如果你有敏锐的眼光，你可能会注意到我们没有为 ORM 类创建一个`__init__()`方法，它是由 SQLAlchemy 在幕后创建的。
*   我们可以使用`Session`作为上下文管理器，所以当数据库操作完成或失败时，我们不需要显式关闭`Session`。
*   我们需要向`Session`实例添加一个 ORM 实例，然后调用`commit()`方法将它保存到数据库中。如果需要添加多个 ORM 实例，可以调用`Session`的`add_all()`方法。

如果在 MySQL 控制台中查询该表，可以看到刚刚插入的记录:

```
mysql> **SELECT** * **FROM** data.product_stocks;
+----------+----------+-------+---------------------+
| stock_id | category | stock | check_time          |
+----------+----------+-------+---------------------+
|        1 | Laptops  |   999 | 2022-03-20 23:04:39 |
+----------+----------+-------+---------------------+
1 row in set (0,00 sec)
```

现在让我们来看看刚刚创造的记录。我们需要调用`query()`方法并用`filter()`或`filter_by()`方法链接它。使用这些方法，事情会变得相当复杂，但我们在这里只是触及了表面:

此代码片段的注释:

*   我们首先以`ProductStock`类为参数调用`Session`的`query()`方法，然后调用`filter()`或`filter_by()`方法按照指定的条件进行过滤。
*   `filter()`是使用 SQLAlchemy ORM 语法通过某些条件过滤[查询](https://docs.sqlalchemy.org/en/14/orm/query.html)的本地方式。它支持非常强大的过滤语法，这将在后面的教程中介绍。现在，您只需要知道您需要指定类和属性，并且必须使用双等号，而不是像`filter_by()`那样使用单等号。
*   `filter_by()`用于使用常规 kwargs 对列名进行简单查询。
*   如您所见，`query()`和 f `ilter()` / `filter_by()`方法的结果可以链接在一起。如果需要，我们可以链接更多的`filter()` / `filter_by()`方法。
*   `first()`方法返回查询的第一个结果。如果没有结果，将返回`None`。如果我们想从查询中获得所有结果，我们可以使用`all()`方法，它将返回一个结果列表，如果没有结果，则返回一个空列表。
*   我们可以用结果的属性访问数据，结果是一个 ORM 实例。

让我们现在更新这个记录。我们把股票设为 1000 吧。要更新记录，我们需要首先找到记录，然后更新它，这非常简单:

如果在 MySQL 控制台中查询该表，可以看到记录的更新数据:

```
mysql> **SELECT** * **FROM** data.product_stocks;
+----------+----------+-------+---------------------+
| stock_id | category | stock | check_time          |
+----------+----------+-------+---------------------+
|        1 | Laptops  |  1000 | 2022-03-20 23:04:39 |
+----------+----------+-------+---------------------+
1 row in set (0,00 sec)
```

如果你需要在 MySQL 中使用[***upsert***](https://en.wiktionary.org/wiki/upsert)或`ON DUPLICATE KEY UPDATE`语法，你可以使用`merge()`方法:

```
mysql> **SELECT** * **FROM** data.product_stocks;
+----------+----------+-------+---------------------+
| stock_id | category | stock | check_time          |
+----------+----------+-------+---------------------+
|        1 | Laptops  |  2000 | 2022-03-20 23:04:39 |
+----------+----------+-------+---------------------+
1 row in set (0,00 sec)
```

注意`merge()`比`add()`或者`add_all()`慢很多，所以只有真正需要的时候才使用。

最后，让我们看看如何用 ORM 删除记录。让我们删除我们一直在处理的记录。要删除一条记录，我们需要在目标记录上调用`delete()`方法，然后调用`commit()`:

现在，该记录已从数据库中删除:

```
mysql> **SELECT** * **FROM** data.product_stocks;
Empty set (0,00 sec)
```

在本教程中，我们介绍了 SQLAlchemy 对象关系映射器(ORM)的基础知识。更多的关注点放在如何真正从零开始使用 ORM。现在，您应该对引擎、会话、ORM、查询等基本概念更加熟悉了。对于如何在实际工作中处理 ORM 问题，也给出了一些提示，比如如何用 SQLAlchemy 创建数据库，如何从现有的表中创建 ORM 类，而不是从头开始编写一切。

在后面的文章中，我们将关注如何使用 SQLAlchemy ORM 的实际例子，这将涉及更复杂的搜索和过滤查询。我们还将介绍如何将 SQLAlchemy 用于流行的 Python 框架，如 Scrapy 和 Flask。

相关文章:

*   [如何用 Python 中的 SQLAlchemy 执行普通 SQL 查询](https://lynn-kwong.medium.com/how-to-execute-plain-sql-queries-with-sqlalchemy-627a3741fdb1?source=your_stories_page----------------------------------------)