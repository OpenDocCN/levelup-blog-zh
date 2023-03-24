# 通过 PyMySQL 在 Python 中处理 SQL 的技巧和诀窍

> 原文：<https://levelup.gitconnected.com/tips-and-tricks-for-handling-sql-in-python-via-pymysql-49ee738c0abf>

## 利用 PyMySQL 帮助文件和 ConfigParser 进行 CRUD 操作

![](img/e7c8c7b2a5e1260413be3b1f0c25efb5.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本教程重点介绍通过 PyMySQL helper 文件在 Python 中处理 SQL 的最佳实践。此外，我们将使用`ConfigParser`模块来存储数据库的配置设置。我在之前的[文章](https://medium.com/better-programming/tips-and-tricks-for-handling-configuration-files-in-python-a9d7429aa50b)中已经覆盖了一个关于`ConfigParser`的教程。请随意查看。本教程有 5 个部分。

1.  设置
2.  配置文件
3.  PyMySQL 助手
4.  基本 API 调用
5.  结论

# 1.设置

强烈建议事先创建一个虚拟环境。让我们从通过`pip install`安装必要的 python 模块开始。第一个模块是`configparser`。

```
pip install config parser
```

接下来，我们将安装`pymysql`模块。

```
pip install pymysql
```

让我们继续下一节，在配置文件中配置设置。

# 2.配置文件

创建一个名为`mysql_db`的新文件夹。然后，在文件夹中创建一个名为`mysql_config.ini`的新文件。打开文件并填写数据库的详细信息。它应该包含以下信息:

```
[db_dev]
host=192.168.1.1
port=5000
user=admin
password=admin
db_name=development[db_online]
host=192.168.1.2
port=5000
user=admin
password=admin
db_name=production
```

通过修改这个配置文件，可以很容易地更改到特定数据库的连接。当从开发服务器转移到生产服务器时，它为您节省了麻烦和任何潜在问题的风险。

# 3.PyMySQL 助手

在同一个文件夹(`mysql_db`)中，创建一个名为`db_connect.py`的新 Python 文件。让我们从导入必要的模块开始:

```
try:
    import configparser
except:
    from six.moves import configparser
import pymysql
```

创建一个名为`MySqlConnector`的新类。

```
class MySqlConnector(object):
```

我们将用以下函数填充它:

*   __init__
*   queryall
*   更新
*   关闭

## __init__

初始化时将调用 init 函数。首先，我们将创建一个用于读取`mysql_config.ini`文件的`ConfigParser`对象。然后，我们将解析必要的设置并连接到数据库。

```
def __init__(self, env=None):
        cf = configparser.ConfigParser()
        cf.read('mysql_db/mysql_config.ini')
        if not env:
            db_env = ''
        else:
            db_env = env
        sec = 'db_{0}'.format(db_env)
        self.db_host = cf.get(sec, 'host')
        self.db_port = cf.getint(sec, 'port')
        self.db_user = cf.get(sec, 'user')
        self.db_pwd = cf.get(sec, 'password')
        self.db_name = cf.get(sec,'db_name')
        self._connect = pymysql.connect(host=self.db_host, port=int(self.db_port), user=self.db_user, password=self.db_pwd, charset='utf8',db=self.db_name, init_command='set names utf8')
```

## queryall

该函数用作从数据库读取项目的`SELECT`调用。它接受两个参数:

*   `sql` —用于读取操作的 SQL 字符串。
*   `params` —用户定义变量的附加参数。

你必须创建一个`cursor`，然后关闭它

```
def queryall(self, sql, params=None):
        cursor = self._connect.cursor()
        try:
            if params:
                cursor.execute(sql, params)
            else:
                cursor.execute(sql)
            result = cursor.fetchall()
        finally:
            cursor.close()
        return result
```

除此之外，您可以使用上下文管理器来省去关闭`cursor`的麻烦。上述函数可以写成如下形式

```
def queryall(self, sql, params=None):
        with connection.cursor() as cursor:
            try:
                if params:
                    cursor.execute(sql, params)
                else:
                    cursor.execute(sql)
                result = cursor.fetchall()
        return result
```

注意，我们正在使用`cursor.fetchall`函数调用。如果您只想查询第一项，可以调用`cursor.fetchone`函数，但是强烈建议在 SQL 字符串中设置`LIMIT`。

## 更新

该函数负责 CRUD 操作的其余部分。它接受与`queryall`功能相同的参数。唯一的区别是它将调用`commit`函数，因为默认情况下连接不是自动提交的。您必须承诺保存您所做的任何更改。

```
def update(self, sql, params=None):
        cursor = self._connect.cursor()
        try:
            if params:
                cursor.execute(sql, params)
            else:
                cursor.execute(sql)
            self._connect.commit()
        finally:
            cursor.close()
```

## 关闭

最后一个函数帮助关闭数据库。每次完成查询后，您都必须关闭它。强烈建议尽量减少与 SQL 数据库的连接。想出一种方法来查询一切并手动处理它，而不是在一个`for`循环中有多个 CRUD 操作。

```
def close(self):
        if self._connect:
            self._connect.close()
```

完成后，让我们添加最后一个函数来获得`MySqlConnector`对象。

```
def get_mysql_conn(env=None):
    return MySqlConnector(env)
```

# 4.基本 API 调用

返回到承载服务器应用程序的根文件夹。向其中添加以下导入。

```
from mysql_db.db_connect import MySqlConnector
```

创建一个变量来确定我们试图连接的数据库

```
sql_config = 'online'
```

让我们看一个简单的例子，它查询数据库中的所有 AI 模型。首先，我们通过下面的代码进行初始化。建议将所有 SQL 查询函数放在一个`try except`块中。

```
mysql_con = MySqlConnector(sql_config)
```

## queryall

我们创建一个查询字符串并调用`queryall`函数。在本教程中，我将使用一个名为`ai_model`的临时表。记得根据数据库中的内容修改它。

```
model_string = '''select * from ai_model where status = 0;'''
model_data = mysql_con.queryall(model_string)
```

您应该能够获得包含所有查询结果的元组。同样，您可以很容易地循环这个元组来找出您从数据库中查询的数据。

```
for m in model_data:
    print(m)
```

此外，通过在查询字符串中提供列，您可以只查询特定的列。以下示例从`ai_model`表中查询`model_id`和`model_name`。

```
model_string = '''select model_id, model_name from ai_model where status = 0;'''
model_data = mysql_con.queryall(model_string)
```

如果你想访问第一个`model_id`，你可以通过下面的代码得到它

```
print(model_data[0][0])
```

您可以传入用户定义的变量，如下面的代码，该代码查询所有语言为 0 的 AI 模型。

```
model_string = '''select * from ai_model where language= %s;'''
model_data = mysql_con.queryall(model_string, (language))
```

## 更新

让我们看看如何通过`update` API 函数更新 ai 模型的状态。

```
update_string = '''update ai_model set status = 1 where model_id = %s;'''
mysql_con.update(update_string, (model_id))
```

插入操作也是以类似的方式完成的。

```
insert_string = '''insert into ai_model(model_id,port,language) values(%s,%s,%s);'''
mysql_con.update(insert_string, (model_id, port, lang))
```

下面的例子展示了我们如何根据`model_id`从`ai_model`表中删除一个模型。

```
delete_string = '''delete from ai_model where model_id = %s;'''
mysql_con.update(delete_string, (model_id))
```

# 5.结论

让我们回顾一下今天所学的内容。我们从一个简单的设置开始，安装了两个 python 模块，`configparser`和`pymysql`。

然后，我们继续为数据库创建配置文件。有了配置文件，我们就可以轻松地切换数据库，没有太多麻烦。如果你想知道更多，请查看我以前的关于处理配置文件的文章。

接下来，我们创建了一个 PyMySQL helper 文件，用于使用配置文件中的设置初始化连接。我们在 helper 文件中添加了一些函数来查询、更新和关闭数据库连接。

我们还详细探讨了如何使用 helper 文件来执行 CRUD 操作。我们尝试了几个从`ai_model`表中选择、更新、插入和删除项目的例子。

感谢阅读，我希望在下一篇文章中再次见到你！

# 参考

1.  [https://medium . com/better-programming/tips-and-tricks-for-handling-configuration-files-in-python-a9d 7429 aa50b](https://medium.com/better-programming/tips-and-tricks-for-handling-configuration-files-in-python-a9d7429aa50b)
2.  [https://pymysql.readthedocs.io/en/latest/user/examples.html](https://pymysql.readthedocs.io/en/latest/user/examples.html)
3.  [https://github.com/PyMySQL/PyMySQL](https://github.com/PyMySQL/PyMySQL)
4.  [https://www.toutiao.com/i6768316282405126668/](https://www.toutiao.com/i6768316282405126668/)