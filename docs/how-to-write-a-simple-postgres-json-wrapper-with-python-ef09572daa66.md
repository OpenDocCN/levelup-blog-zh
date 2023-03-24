# 如何用 Python 写一个简单的 Postgres JSON 包装器

> 原文：<https://levelup.gitconnected.com/how-to-write-a-simple-postgres-json-wrapper-with-python-ef09572daa66>

![](img/43540378f665d7321e0e8e9548a18f92.png)

琼·加梅尔在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Postgres 是一个*传奇*数据库。它已经存在了一段时间，并且得到了广泛的支持。这个数据库快速、高效，被从初创公司到国际企业集团的各种公司广泛使用。Postgres 如此受欢迎的一个原因是它有大量的扩展、插件和连接器。你可以通过许多不同的编程语言进行连接，也有许多顶级的 ORM[or](https://en.wikipedia.org/wiki/Object-relational_mapping)可用。

根据您的特定需求和项目规模，您可能不需要设置大量额外的抽象层或框架来处理对 Postgres 的查询。在 Python 这样的语言中，为不同的查询和常用函数制作一个简单的包装器既简单又有效。

如果您正在构建一个小型应用程序的原型，或者使用一个高度受限的嵌入式系统，那么编写一个轻量级的包装器会有很大的好处。使用非常流行的 [psycopg2](https://pypi.org/project/psycopg2/) 模块，从 PG 中获取 JSON 结果非常容易。这个用于 Python 的低级数据库连接模块允许您连接到 Postgres、执行 SQL 和执行其他数据库功能。让我们探索如何使用这个模块在 Python 中设置一个数据库包装器，以使执行查询变得快速而简单。

## 数据库

对于我们的示例 Postgres 服务器，我们将使用一个充满测试数据的预填充数据库。这给了我们一堆样本数据来查询。毕竟一个空的数据库有什么意思。

我们将使用 Docker 来设置数据库容器，这样我们就不会用测试安装来污染我们的主机操作系统。如果你以前从未使用过 Docker，或者需要快速复习一下,[*Docker——初学者指南——第 1 部分:图片&容器*](https://medium.com/codingthesmartway-com-blog/docker-beginners-guide-part-1-images-containers-6f3507fffc98) 是由[import simplejson as json
import psycopg2
from psycopg2.extras import RealDictCursorclass PostgresWrapper:
def __init__(self, **args):
self.user = args.get('user', 'postgres')
self.port = args.get('port', 5432)
self.dbname = args.get('dbname', 'world')
self.host = args.get('host', 'localhost')
self.connection = None](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[首先，我们引入了`simplejson`而不是标准的`json`库，因为当我们查询`Decimal`值时，只有使用支持转储该数据类型的更新库才能正确序列化它们。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[接下来，我们引入`psycopg2`库和`RealDictCursor`工厂。这个工厂将让我们轻松地将查询结果直接序列化为一个`Dict`类型，然后将它转储到 JSON。我们还将定义一个基本的`PostgresWrapper`类来保存我们所有的特性并提供一些默认的初始化值。现在让我们添加一些简单的连接函数:](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

```
 def connect(self):
        pg_conn = psycopg2.connect(
            user=self.user,
            port=self.port,
            dbname=self.dbname,
            host=self.host
        )
        self.connection = pg_conn def get_json_cursor(self):
        return self.connection.cursor(cursor_factory=RealDictCursor)
```

[在`connect()`函数中，我们使用`psycopg2`直接连接到我们之前设置的 Postgres 数据库。这个连接还指定了在 Postgres 内部连接到哪个数据库。对于这个例子，我们将使用包含城市和国家的`world`数据。下一个函数使用先前的连接来设置新的游标。光标类似于您输入 SQL 语句的交互式`psql`提示符，除了这个是非交互式的并且可以通过编程控制。这个游标还使用`RealDictCursor`规范来返回`Dict`类型，这些类型很容易在以后作为 JSON 字符串返回。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[最后，让我们设置函数来实际获取一些数据:](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

```
 @staticmethod
    def execute_and_fetch(cursor, query):
        cursor.execute(query)
        res = cursor.fetchall()
        cursor.close()
        return res def get_json_response(self, query):
        cursor = self.get_json_cursor()
        response = self.execute_and_fetch(cursor, query)
        return json.dumps(response) def get_countries(self):
        query = "SELECT * FROM country LIMIT 2;"
        print(self.get_json_response(query))
```

[`execute_and_fetch()`函数使用我们之前设置的游标来执行 SQL 查询，关闭游标连接，然后返回结果。当动作完成时关闭游标是很重要的，因为否则每次调用`execute_and_fetch()`都会实例化多个游标并消耗数据库中更多的资源。`get_json_response()`函数是一个助手函数，它处理光标的实例化，并将光标和查询传递给`execute_and_fetch()`。然后，这个函数以 JSON 字符串的形式返回响应。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[`get_countries()`函数将利用`get_json_response()`并传入对国家的查询。当我们调用这个函数时，我们从数据库接收查询行的最终 JSON blob。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

## [把所有的放在一起](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[让我们回顾一下整个包装类应该是什么样子:](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

```
#!/usr/bin/env python3import simplejson as json
import psycopg2
from psycopg2.extras import RealDictCursorclass PostgresWrapper:
    def __init__(self, **args):
        self.user = args.get('user', 'postgres')
        self.port = args.get('port', 5432)
        self.dbname = args.get('dbname', 'world')
        self.host = args.get('host', 'localhost')
        self.connection = None def connect(self):
        pg_conn = psycopg2.connect(
            user=self.user,
            port=self.port,
            dbname=self.dbname,
            host=self.host
        )
        self.connection = pg_conn def get_json_cursor(self):
        return self.connection.cursor(cursor_factory=RealDictCursor) @staticmethod
    def execute_and_fetch(cursor, query):
        cursor.execute(query)
        res = cursor.fetchall()
        cursor.close()
        return res def get_json_response(self, query):
        cursor = self.get_json_cursor()
        response = self.execute_and_fetch(cursor, query)
        return json.dumps(response) def get_countries(self):
        query = "SELECT * FROM country LIMIT 2;"
        print(self.get_json_response(query))dbconn = PostgresWrapper()
dbconn.connect()
dbconn.get_countries()
```

[在文件的底部，我们处理实例化`PostgresWrapper`，连接然后调用`get_countries()`来打印一些样本 JSON 数据。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[您应该能够简单地运行该文件并产生结果:](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

```
./wrapper.py
```

[现在，您应该会看到类似如下的 JSON 输出:](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

```
[{"code": "AFG", "name": "Afghanistan", "continent": "Asia", "region": "Southern and Central Asia", ...}]
```

[如果一切工作正常，您将得到一个非常简单的 Postgres 包装器类，可以调整它来输出不同查询的 JSON blobs。这可以集成到现有的 Python 应用程序中，甚至可以用作 API 的基本后端。请记住，这是一个简单的示例类，您可以对该类进行一些扩展，使查询更加灵活。](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=)

[将 Postgres 与 Python 的强大功能和强大的模块结合起来，您可以简化和抽象连接到数据库和从数据库中获取数据的复杂性。对于一个更加优雅的方法，可以考虑探索一个叫做](https://medium.com/u/c840719f0a3e#!/usr/bin/env python3</span><span id=) [SQLAlchemy](https://www.sqlalchemy.org/) 的 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 模块。这允许您将数据库结果作为实际的 Python 对象进行交互，并编写更少的特定于数据库的代码。

*感谢阅读！Postgres 和 Python 是天作之合。这个包装器是超级轻量级的，容易理解，但是可以扩展更多的功能。在* [*Twitter*](https://twitter.com/Tate_Galbraith) *上发布一些您自己的 Python & Postgres 配对冒险。*