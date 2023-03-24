# SQLAlchemy 表达式语言简介

> 原文：<https://levelup.gitconnected.com/introduction-to-sqlalchemy-expression-language-6943894bbf7>

使 CRUD 操作更容易的 Python SQL 工具包

![](img/2eb1ab5001659bcb92ff615d2ab4f3dd.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

通过阅读本文，您将学会用 Python 在您喜欢的数据库上执行 CRUD 操作。在本教程中，我将使用一个内存专用的 SQLite 数据库，但它也可以用于其他数据库。基于[官方文档](https://docs.sqlalchemy.org/en/14/)，SQLAlchemy 表达语言:

> “…介绍了一个使用 Python 构造来表示关系数据库结构和表达式的系统。这些结构被建模为尽可能地类似底层数据库的结构，同时提供了数据库后端之间各种实现差异的少量抽象。虽然这些构造试图用一致的结构来表示后端之间的等价概念，但它们并不隐藏后端的特定子集所特有的有用概念。因此，表达式语言提供了一种编写后端中立的 SQL 表达式的方法，但并不试图强制表达式是后端中立的。

事实上，SQLAlchemy 以其对象关系映射器而闻名，这是一个建立在表达式语言之上的独特 API。对于这篇文章，我们将回到基础，只关注表达式语言的抽象层，以了解它是如何工作的。

本教程有 3 个部分:

1.  设置
2.  履行
3.  结论

让我们继续下一部分，开始安装必要的模块。

# 1.设置

使用`pip install`安装 SQLAlchemy 非常简单。强烈建议您在继续安装之前创建一个虚拟环境。激活虚拟终端并运行以下命令。

```
pip install SQLAlchemy
```

使用以下命令检查您的安装(不区分大小写)

```
pip show sqlalchemy
```

您应该会看到以下输出

![](img/8faa4ecd946ba1a3d06defa6bc9220c1.png)

作者图片

让我们继续下一节，开始写一些 Python 代码。

# 2.履行

## 导入

在 Python 文件的顶部添加以下导入声明。

```
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, DateTime, Floatfrom sqlalchemy.sql import select, update, insert, delete, textimport datetime
```

## 初始化

通过将数据库连接到引擎来开始数据库的初始化。引擎是每个 SQLAlchemy 应用程序的起点。下图是 SLQAlchemy 中 it 工作流程的结构。

![](img/fa40a67c0fed4ccd74f3fd4ab0174324.png)

图片来自 [SQLAlchemy](https://docs.sqlalchemy.org/en/13/core/engines.html#sqlalchemy.create_engine)

在导入声明后追加以下代码。

```
engine = create_engine('sqlite:///test.db', echo = True)
```

第一个参数是连接的数据库 URL。它通常使用以下模式。

```
dialect+driver://username:password@host:port/database
```

请查看下面的[链接](https://docs.sqlalchemy.org/en/13/core/engines.html#database-urls)了解更多关于可用数据库及其各自 URL 模式的信息。

我已经将`echo`设置为`True`,以获得控制台的详细输出，用于调试目的。您可以在部署期间安全地忽略此参数。

## 元数据

下一步是定义表示表和表内模式的元数据。创建一个新的`MetaData`对象。

```
meta = MetaData()
```

用下面的代码定义一个新表。我将该表命名为`user`。

![](img/e42f4e1d53229beb5ec4aafd85253242.png)

```
user = Table(
   'user', meta,
   Column('id', Integer, primary_key = True),
   Column('date', DateTime),
   Column('name', String),
   Column('bmi', Float),
)
```

*   `id` —每行数据的唯一标识符。将其定义为该表的主键。
*   `date` —在每次插入期间保存时间戳的日期时间数据。
*   `name` —表示用户姓名的字符串数据
*   `bmi` —代表用户体重指数的浮点数据。

在引擎中创建表。它将在您指定的目录中创建一个新的 db 文件。如果不存在，将创建新的数据库和表。多次调用它是安全的。

```
meta.create_all(engine)
```

创建到引擎的连接。

```
conn = engine.connect()
```

## 插入

您可以使用以下模式轻松地将数据插入到表中。第一种样式是单一插入，但强烈建议使用多重插入样式。

```
date = datetime.datetime.now()
ins = users.insert().values(date=date, name='wfng', bmi=25.5)conn.execute(ins)
```

使用下面的代码可以很容易地完成多次插入

```
date = datetime.datetime.now()conn.execute(user.insert(), [
    {'date': date, 'name' : 'Alice', 'bmi' : 18.4},
    {'date': date, 'name' : 'Bob', 'bmi' : 22.5},
    {'date': date, 'name' : 'Charlie', 'bmi' : 26.1},
])
```

您只需要向执行函数传递两个参数

*   `user.insert()` —调用插入函数进行插入操作。
*   `list` —要插入的字典数据列表。

如果您启用了`echo`参数到`True`，您应该在您的终端看到以下输出。

![](img/288de97acc147d53422292b560dfc277.png)

作者图片

## 挑选

可以用下面的代码获取数据

```
s = select([user])
result = conn.execute(s)for row in result:
    print(row)
```

您应该会看到以下结果

![](img/b48f923a1c09ed3c550ba791113fca85.png)

作者图片

返回的结果是一组字典，可以通过索引来访问

```
row[2]
```

或键值

```
row['name']
```

## 更新

使用以下代码执行 dUpdate。如果`bmi`值超过 25，我们就更新日期时间

```
date = datetime.datetime.now()up = user.update().where(user.c.bmi > 25).values(date=date)
conn.execute(up)
```

当您使用`SELECT`语句查询结果时，您应该会看到第三行数据的日期时间的差异。

## 删除

让我们试着删除一行数据。以下代码将删除低于 20.5 `bmi`的用户的数据。在这种情况下，应该在运行这段代码时删除 Alice。

```
delete = user.delete().where(user.c.bmi < 20.5)
conn.execute(delete)
```

当您运行`SELECT`语句时，您应该得到以下输出，这表明它按预期工作。

![](img/b91f89bdf8c1091fc038800714848df4.png)

作者图片

您实际上可以调用 delete 而无需指定条件。在这种情况下，它将删除表中的所有数据。

```
delete_all = user.delete()
conn.execute(delete_all)
```

当您试图再次插入数据时，`id`将从初始值 1 开始。

## 文本 SQL

如果您正在寻找一个好的旧 SQL 语句，您可以依靠 text()构造来实现。以下示例将查询`bmi`在 20 到 35 之间的用户。

```
stmt = text("SELECT * FROM user WHERE user.bmi BETWEEN :x AND :y")result = conn.execute(stmt, x = 20, y = 35)
for row in result:
    print(row)
```

# 3.结论

让我们回顾一下今天所学的内容。

我们通过`pip install`开始安装 SQLAlchemy。

然后，我们深入探讨了 SQLAlchemy 的基本用法。我们用一些预定义的元数据初始化了一个引擎。已建立连接以连接到本地 SQLite 数据库。

接下来，我们使用 SQLAlchemy 的抽象层测试了基本的 CRUD 操作。此外，我们还尝试了使用文本 SQL 方法来调用使用基本 SQL 语句的查询。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [SQLAlchemy 的文档](https://docs.sqlalchemy.org/en/13/core/tutorial.html)