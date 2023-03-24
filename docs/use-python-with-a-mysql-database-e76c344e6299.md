# 使用 Python 和 MySQL 数据库

> 原文：<https://levelup.gitconnected.com/use-python-with-a-mysql-database-e76c344e6299>

> 像个数据工程师！

![](img/38b6e43363c951968495473ab51ab866.png)

由 [lookstudio](https://www.freepik.com/author/lookstudio) 拍摄的照片

# **我为什么要在 MySQL 中使用 Python？**

您可能希望将 Python 与 MySQL 数据库结合使用有几个原因:

![](img/346c7c8481db8fb616e4a825723a54cc.png)

照片由[rawpixel.com](https://www.freepik.com/author/rawpixel-com)

> **数据存储和管理:**

MySQL 是一个流行的数据库管理系统，允许你有效地存储和检索数据。通过将 **Python** 与 **MySQL** 结合使用，您可以使用 **Python** 的强大功能来访问和操作存储在数据库中的数据。

![](img/6a1bc61a1932ee951958efb10e983640.png)

照片由[故事集](https://www.freepik.com/author/stories)提供

> **自动化:**

Python 是一种功能强大的编程语言，可用于自动化任务。如果您在 MySQL 数据库中存储了大量数据，并且需要对这些数据执行频繁、复杂的查询，那么您可以使用 Python 来自动化这个过程并节省时间。

![](img/0c049ad1682f085baf8421cfe5b11d48.png)

照片由 [tirachardz](https://www.freepik.com/author/tirachardz) 拍摄

> **数据分析:**

**Python** 有很多用于数据分析的库，比如`pandas`、`numpy`、`matplotlib`。通过将 **Python** 与 **MySQL** 结合使用，您可以使用这些库来分析存储在数据库中的数据，并从中获得洞察力。

![](img/2e42ae0f9566892655911ba158bccb0f.png)

照片由[rawpixel.com](https://www.freepik.com/author/rawpixel-com)拍摄

> **网页开发:**

MySQL 经常与 web 开发框架结合使用，如 **Django** 和 **Flask** 。通过将 **Python** 与 **MySQL** 结合使用，您可以构建存储和检索数据库数据的 web 应用程序。

# 准备好了吗？我们开始吧！

用 MySQL 连接 Python

![](img/eabbf65e66daf4396a2d1d3e3a621705.png)

照片由[故事集](https://www.freepik.com/author/stories)提供

要在 MySQL 数据库中使用 Python，您需要安装 Python MySQL 库。在 Python 中使用 MySQL 的一个流行库是 MySQL Connector/Python。这个库提供了用于与 MySQL 服务器通信的 Python API。

要安装 MySQL 连接器/Python，可以使用`pip`，Python 包管理器。打开终端并运行以下命令:

```
pip install mysql-connector-python
```

一旦安装了这个库，就可以用它来连接 MySQL 数据库并执行 SQL 查询。下面是一个如何连接到 MySQL 数据库并从表中选择行的示例:

```
import mysql.connector

# Connect to the database
cnx = mysql.connector.connect(user='username', password='password',
                              host='hostname', database='database')
```

这个例子假设您有一个 MySQL 数据库，其中有一个名为`table`的表，并且您知道用户名、密码、主机名和数据库名。用您自己的值替换这些值，以连接到您自己的数据库。

```
# Create a cursor
cursor = cnx.cursor()

# Insert SQL code here!
query = 'SELECT * FROM table'
cursor.execute(query)

# Iterate over the rows
for (column1, column2, column3) in cursor:
    print(column1, column2, column3)
```

一旦建立了与数据库的连接，就可以使用`cursor`对象来执行 SQL 查询并检索结果。在上面的例子中，我们使用一个`SELECT`语句从`table`表中选择所有行。您可以在`execute`方法中使用任何有效的 SQL 查询从数据库中检索数据。

```
# Close the cursor and connection
cursor.close()
cnx.close()
```

使用它来关闭与 MySQL 数据库的连接。

## 现在，让我们使用一个数据框架来尝试一下！

要在 MySQL 数据库中使用 dataframe，可以使用 Python 库，如`pandas`来读写数据库中的数据。

下面是一个示例，说明如何使用`pandas`和 MySQL Connector/Python 从 MySQL 数据库中读取数据帧，并通过一个附加列将其写回数据库:

```
import mysql.connector
import pandas as pd

# Connect to the database
cnx = mysql.connector.connect(user='username', password='password',
                              host='hostname', database='database')

# Read the dataframe from the database
df = pd.read_sql("SELECT * FROM table", cnx)

# Add a new column to the dataframe
df['new_column'] = 0

# Write the dataframe back to the database
df.to_sql("table", cnx, if_exists="replace", index=False)

# Close the connection
cnx.close()
```

`pandas` `read_sql`函数用于将数据从`table`表读入数据帧。然后使用`to_sql`函数将数据帧写回`table`表，替换表中的任何现有数据。

## 我想用二分搜索法树来尝试一些不同的东西

> 下面是结果！

要了解更多关于二分搜索法树的信息，请点击这里查看我的帖子！

[https://medium . com/git connected/simple-example-of-a-binary-search-tree-426 C2 dfda 24](https://medium.com/gitconnected/simple-example-of-a-binary-search-tree-426c2dfda24)

要使用二叉查找树在 MySQL 数据库中搜索特定数据，您需要设计一个数据库模式，其中包含一个表来存储树的节点。该表应该有存储每个节点中存储的数据的列，以及存储每个节点的左右子指针的列。

一旦在 MySQL 数据库中设计了模式并创建了表，您就可以使用 Python MySQL 库(如 MySQL Connector/Python)连接到数据库并执行 SQL 查询来插入、删除和检索二叉查找树中的数据。

下面是一个示例，说明如何使用 Python 和 MySQL Connector/Python 来搜索存储在 MySQL 数据库中的二叉查找树中的特定数据。

在这个例子中，我们使用`mysql-connector-python`库连接到一个 MySQL 数据库，并从`users`表中检索行。

```
import mysql.connector

# Connect to the database
cnx = mysql.connector.connect(user='username', password='password', host='host', database='database')
cursor = cnx.cursor()

class Node:
    def __init__(self, val, data):
        self.left = None
        self.right = None
        self.val = val
        self.data = data

class BinarySearchTree:
    def __init__(self):
        self.root = None

    def insert(self, val, data):
        new_node = Node(val, data)
        if self.root is None:
            self.root = new_node
            return

        current_node = self.root
        while True:
            if val < current_node.val:
                if current_node.left is None:
                    current_node.left = new_node
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = new_node
                    break
                else:
                    current_node = current_node.right

    def search(self, val):
        current_node = self.root
        while current_node is not None:
            if val == current_node.val:
                return current_node.data
            elif val < current_node.val:
                current_node = current_node.left
            else:
                current_node = current_node.right
        return None
```

在这个修改后的实现中，每个`Node`对象都包含一个`data`属性，用于存储与节点值相关的数据。

```
class Node:
    def __init__(self, val, data):
        self.left = None
        self.right = None
        self.val = val
        self.data = data
```

`insert`方法现在接受一个额外的`data`参数，它存储在新节点的`data`属性中。`search`方法返回它找到的节点的`data`属性，而不仅仅是`True`。

```
class BinarySearchTree:
    def __init__(self):
        self.root = None

    def insert(self, val, data):
        new_node = Node(val, data)
        if self.root is None:
            self.root = new_node
            return

        current_node = self.root
        while True:
            if val < current_node.val:
                if current_node.left is None:
                    current_node.left = new_node
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = new_node
                    break
                else:
                    current_node = current_node.right

    def search(self, val):
        current_node = self.root
        while current_node is not None:
            if val == current_node.val:
                return current_node.data
            elif val < current_node.val:
                current_node = current_node.left
            else:
                current_node = current_node.right
        return None
```

然后我们将每一行插入到二叉查找树中，使用`id`列作为值，整行作为数据。最后，我们使用`search`方法在树中搜索值 3 和 6，并打印结果。

```
# Create a binary search tree
bst = BinarySearchTree()

# Insert rows from the 'users' table into the tree
query = "SELECT * FROM users"
cursor.execute(query)
for (id, name) in cursor:
    bst.insert(id, (id, name))
```

`search`函数使用`SELECT`查询在二叉查找树中搜索特定值。如果找到该值，该函数将返回包含该值的行。如果没有找到该值，函数返回`None`。

```
# Search for a value in the tree and retrieve the associated data
result = bst.search(3)
print(result)  # (3, 'Charlie')

result = bst.search(6)
print(result)  # None
```

与下面类似，我们用它来关闭与 MySQL 数据库的连接。

```
# Close the database connection
cnx.close()
```

下面是完整的代码:

```
import mysql.connector

# Connect to the database
cnx = mysql.connector.connect(user='username', password='password', host='host', database='database')
cursor = cnx.cursor()

class Node:
    def __init__(self, val, data):
        self.left = None
        self.right = None
        self.val = val
        self.data = data

class BinarySearchTree:
    def __init__(self):
        self.root = None

    def insert(self, val, data):
        new_node = Node(val, data)
        if self.root is None:
            self.root = new_node
            return

        current_node = self.root
        while True:
            if val < current_node.val:
                if current_node.left is None:
                    current_node.left = new_node
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = new_node
                    break
                else:
                    current_node = current_node.right

    def search(self, val):
        current_node = self.root
        while current_node is not None:
            if val == current_node.val:
                return current_node.data
            elif val < current_node.val:
                current_node = current_node.left
            else:
                current_node = current_node.right
        return None

# Create a binary search tree
bst = BinarySearchTree()

# Insert rows from the 'users' table into the tree
query = "SELECT * FROM users"
cursor.execute(query)
for (id, name) in cursor:
    bst.insert(id, (id, name))

# Search for a value in the tree and retrieve the associated data
result = bst.search(3)
print(result)  # (3, 'Charlie')

result = bst.search(6)
print(result)  # None

# Close the database connection
cnx.close()
```

请注意，这个示例只是在 MySQL 数据库中使用二叉查找树的一种可能方式，还有许多其他方式可以修改和扩展这段代码以满足您的特定需求。

# 跟我来。

我希望这篇文章对你有帮助！

我正在深入研究 Python、SQL、机器学习、AR、VR 和可视化，目标是确定最佳实践、提高效率并不断提升！

在这里整理我的想法/笔记，希望它能让媒体社区的每个人受益！

请关注我，这样我就知道我的故事正在帮助人们，如果您有任何问题，请随时与我联系！感激不尽！