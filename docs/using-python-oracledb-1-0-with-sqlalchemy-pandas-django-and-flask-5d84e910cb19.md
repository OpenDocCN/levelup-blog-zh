# 使用 python-oracledb 1.0 和 SQLAlchemy、Pandas、Django 和 Flask

> 原文：<https://levelup.gitconnected.com/using-python-oracledb-1-0-with-sqlalchemy-pandas-django-and-flask-5d84e910cb19>

![](img/10fd266ab65e24951cca2ae74c49ea3e.png)

用于 Oracle 数据库的 Python cx_Oracle 驱动程序的一个新的主要版本已经发布，并带有一个全新的名称: [**python-oracledb**](https://oracle.github.io/python-oracledb/) 。

详见[发布公告](https://cjones-oracle.medium.com/open-source-python-thin-driver-for-oracle-database-e82aac7ecf5a)。

这个名称的改变对你喜欢的框架、ORM、SQL 生成器和库意味着什么？驱动程序继续符合 [Python 数据库 API v2.0 规范](https://www.python.org/dev/peps/pep-0249/)(有许多增加，只有几个排除)，因此任何框架等。那期望一个标准的驱动应该没有问题。直到每个框架等等。添加对新名称的支持，您可以在应用程序中添加几行代码来进行名称映射。请注意，一些功能，如死连接错误检测，可能无法工作，直到本机支持登陆，在这种情况下，因为一些驱动程序错误必须改变。

这里有一些调整，可以用来测试一些流行的环境。

# SQLAlchemy 1.4

***更新:*** *对 python-oracledb 的原生支持已经添加到即将发布的 SQLAlchemy 2.0 版本中，参见* [*提交*](https://github.com/sqlalchemy/sqlalchemy/commit/1961e1321440a1e0500ecd13624837ed088eaceb#diff-3cfc940bb8ec8a69020c12f483a6ef4659a5f8ce5c31d2de830ad6ed5ae030e6R17) *和* [*用法说明*](https://github.com/sqlalchemy/sqlalchemy/commit/1961e1321440a1e0500ecd13624837ed088eaceb#diff-d58626ed254b6d1d057fdd34e0aa8b56286406bd0be23ca7b534f9c95c4590dbR9-R41) *和本* [*博文*](https://cjones-oracle.medium.com/using-the-development-branch-of-sqlalchemy-2-0-with-python-oracledb-d6e89090899c) *。如果您使用的不是 SQLAlchemy 的预发布版本，请遵循下面的说明。*

要立即试用 python-oracledb 1.0，请使用 pip 安装它:

`python -m pip install oracledb`

然后将其添加到您的顶级 SQLAlchemy 1.4 文件中:

```
import sys
import oracledb
oracledb.version = "8.3.0"
sys.modules["cx_Oracle"] = oracledb
```

这个片段需要在 SQLAlchemy 1.4 执行“导入 cx_Oracle”之前出现。它启用 python-oracledb 的默认“瘦”模式。这是一种全新的模式，不需要任何 Oracle 客户端库。我们已经像这样运行了 SQLAlchemy 测试套件。我们非常感谢 SQLAlchemy 的测试广度，它在瘦模式的早期开发中帮助了我们。

如果您想使用 python-oracledb 的“厚”模式(它提供了更多的特性，如高级排队，参见[特性列表](https://python-oracledb.readthedocs.io/en/latest/user_guide/appendix_a.html#featuresummary))，那么您可以添加对`init_oracle_client()`的调用。我在上面显示的代码之后添加了这段代码:

```
import os
import platform
if platform.system() == "Darwin":
    oracledb.init_oracle_client(lib_dir=os.environ.get("HOME")+"/Downloads/instantclient_19_8")
elif platform.system() == "Windows":
    oracledb.init_oracle_client(lib_dir=r"C:\oracle\instantclient_19_14")
else:
    oracledb.init_oracle_client()
```

在 Linux 上，你需要调用`init_oracle_client()`但不传递`lib_dir`参数:在 Linux 上启动 Python 之前，你必须运行`ldconfig`或设置`LD_LIBRARY_PATH`。

python-oracledb 厚模式与 cx_Oracle 具有相同的架构，并使用相同的 Oracle 客户端库堆栈。

SQLAlchemy 中的连接与之前 python-oracledb 中的相同，例如:

```
engine = create_engine(
    f'oracle://{un}:{pw}@{host}:{port}/?service_name={service_name}', max_identifier_length=128
)
```

通过使用 SQLAlchemy 的`connect_args`选项，可以有选择地使用 python-oracledb 中的一些新连接参数。下面是一个完整的应用程序，它设置了 python-oracledb 的新的单独的`host`、`port`和`service_name`连接参数:

```
import os
import oracledb
import sys
oracledb.version = "8.3.0"
sys.modules["cx_Oracle"] = oracledbfrom sqlalchemy import create_engine
from sqlalchemy import textun = os.environ.get("PYTHON_USERNAME")
pw = os.environ.get("PYTHON_PASSWORD")engine = create_engine(f'oracle://{un}:{pw}@',
                       connect_args={
                           "host": "localhost",
                           "port": 1521,
                           "service_name": "orclpdb"
                       }
         )with engine.connect() as conn:
    print(conn.scalar(text(
           """SELECT UNIQUE CLIENT_DRIVER
              FROM V$SESSION_CONNECT_INFO
              WHERE SID = SYS_CONTEXT('USERENV', 'SID')""")))
```

这表明正在使用驱动程序的精简模式:

```
python-oracledb thn : 1.0.0
```

为了适应`CLIENT_DRIVER`列的宽度，缩写“thn”和“thk”用于表示两种驱动模式。

查看 python-oracledb [文档](https://python-oracle.readthedocs.io/en/latest/index.html)，了解可以传递的其他`connect()`参数。

**更新**:简介[中提到的死连接检测支持将在 SQLAlchemy 1.4.37](https://github.com/sqlalchemy/sqlalchemy/commit/e347aa81a593252b6ee4f84a411f62f526ba0c77) 中登陆，但是在对 python-oracledb 的完全支持合并之前，您仍然需要使用名称映射片段。

**更新**:如果你也在看我在[上发表的关于使用 SQLAlchemy 2.0(开发)和 python-Oracle db for Oracle Database](https://medium.com/oracledevs/using-the-development-branch-of-sqlalchemy-2-0-with-python-oracledb-d6e89090899c)的文章，请注意，在 1.4 版本中，你连接的是`oracle://`，而在 2.0 版本中，你连接的是`oracle+oracledb://`。

# 熊猫

由于 Pandas 使用 SQLAlchemy，所以可以使用上面的步骤。将该代码段添加到第一个文件运行的顶部。

# 姜戈

同样，用于 SQLAlchemy 的初始代码片段也可以用于 Django。需要添加到`settings.py`的顶部:

```
import sys
import oracledb
oracledb.version = "8.3.0"
sys.modules["cx_Oracle"] = oracledb
```

如果您有 Oracle 客户端库，并且想要使用 python-oracledb“厚”模式，您也可以调用`init_oracle_client()`。

要连接到 Oracle 数据库，您的`settings.py`文件连接设置将是正常的，例如:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': 'example.com:1521/orclpdb',
        'USER': 'scott',
        'PASSWORD': 'XXXX'
    }
}
```

许多选项可以添加到简易连接字符串中，参见[简易连接加语法](https://download.oracle.com/ocomdocs/global/Oracle-Net-21c-Easy-Connect-Plus.pdf)。

对于瘦模式下的 python-oracledb，可以通过使用选项部分来选择使用新的 python-oracledb 连接参数。例如，要指定连接的时间限制，可以传递新的 python-oracledb `tcp_connect_timeout`参数，如下所示。在该示例中，作为示例，`host`、`port`和`service_name`值也保持分开。注意服务名在`OPTIONS`块中指定:

```
DATABASES = {
    'default': {
            'ENGINE': 'django.db.backends.oracle',
            'USER': 'scott',
            'PASSWORD': 'xxx',
            'HOST': 'localhost',
            'PORT': 1521,
            'OPTIONS': {
                'service_name': 'orclpdb',
                'tcp_connect_timeout': 10,
            },
    }
}
```

# 瓶

Flask 是一个“微框架”,不强制依赖，所以你可以立即开始使用 python-oracledb 编写新的应用程序。查看 [connection_pool.py](https://github.com/oracle/python-oracledb/blob/main/samples/connection_pool.py) 示例，它展示了如何使用带有连接池的 Flask。如果您想将一个应用从 cx_Oracle 升级到 python-oracledb，那么请查阅[升级](https://python-oracledb.readthedocs.io/en/latest/user_guide/appendix_c.html#upgrading-from-cx-oracle-8-3-to-python-oracledb)文档。

# 摘要

总之，您现在可以通过添加一点脚手架代码在您的应用程序中测试 python-oracledb。当框架/ ORM / SQL 生成器/库社区有时间添加新的“oracledb”名称空间时，就不需要这个脚手架了。

# 资源

首页:[oracle.github.io/python-oracledb](https://oracle.github.io/python-oracledb/index.html)

安装说明:[python-oracledb.readthedocs.io/en/latest/installation.html](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)

文件:[python-oracle.readthedocs.io/en/latest/index.html](https://python-oracle.readthedocs.io/en/latest/index.html)

提问:[github.com/oracle/python-oracledb/discussions](https://github.com/oracle/python-oracledb/discussions)

源代码库:[github.com/oracle/python-oracledb](https://github.com/oracle/python-oracledb)