# Python 中的数据读取—简单易学

> 原文：<https://levelup.gitconnected.com/reading-data-with-python-easy-learning-68947a42fb0>

![](img/182f7619cf8673b22c266467ec713ed0.png)

[https://hdq walls . com/wallpaper/2932 x 2932/蜘蛛侠-阅读-新-论文](https://hdqwalls.com/wallpaper/2932x2932/spider-man-reading-new-paper)

你使用什么编程语言没有区别。如果您需要与现实世界的应用程序交互，您必须处理数据和数据库。您使用的数据源类型取决于您的应用程序。您在 Python 中的大部分时间将用于读取数据和与数据交互。作为 Python 开发人员，您应该熟悉从各种来源读取数据的基础知识。在本文中，我将介绍一些基本的数据源，以及如何使用 Python 从这些数据源中检索数据。

## **先决条件**

*   基本配置选项和命令行界面(Unix 推荐)
*   Python 3.5 及以上(所有程序都用 Python 3.5+测试过)

## 1.读取文本文件

文本是最基本的数据源类型。非结构化数据存储在文本文件中。然而，存储格式化的数据是非常有用的，比如**应用程序日志**。在 Python 中，读取文件是小菜一碟。但是，我想提供一些读取和解析日志文件的示例代码。

**示例:** *app.log*

您可以使用内置函数 [open](https://docs.python.org/3/library/functions.html#open) 打开并读/写一个文件。另一方面，如果您打算使用低级 I/O，您可以使用 [os.open](https://docs.python.org/3/library/os.html#os.open) 。

**注意:**请记住，在前面的例子中，我使用了带有操作符的[。一旦操作块完成，操作员协助清理资源。
在 Python 中，你还可以通过使用迭代器对**逐行进行**操作。](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)

在前面的例子中，我逐行读取文件并解析它以创建一个字典数据数组。在数组析构中，使用了 ***rest 运算符**。更多信息可在[这里](https://blog.teclado.com/destructuring-in-python/)找到。

## 2.正在读取 CSV 文件

在 Python 中读取 CSV 文件是如此常见，以至于它有自己的模块/包。导入 CSV 模块后，您可以使用 CSV 文件。

**样本:** [*country.csv*](https://gist.githubusercontent.com/deepakshrma/b87c5d9d1382bdb1a2bce1b2b03bcabc/raw/22c9670d2a27c0fa132c484bc008b7fcabc09b39/country.csv)

**逐行读取 CSV 行:**

您还可以使用 [csv 读取字典格式的行。字典阅读器](https://docs.python.org/3/library/csv.html#csv.DictReader)。

## **3。使用 HTTP 通过网络**

有时，您可能需要通过网络读取和解析 CSV 文件。 [urllib](https://docs.python.org/3/library/urllib.html) 模块可用于连接网络和下载数据。下载完数据后，您可以用 [csv 解析它。字典阅读器](https://docs.python.org/3/library/csv.html#csv.DictReader)。
在下面的例子中，我将创建一个可重用的助手 **requestCSV** 函数，它通过网络下载并解析 CSV 文件。

**字典排版本:** *requestJsonCSV*

你可以在这里阅读更多关于读写的内容[读写 csv 文件](https://thepythonguru.com/python-how-to-read-and-write-csv-files/)。

## 4.从数据库中读取数据

从数据库中读取数据需要数据库连接器。我将使用一个 **sqlite3** 数据库来演示这些示例。在互联网上，你可以很容易地找到相关数据库的代码和连接器。

**注:**

*   如果你想测试 SQLite，你可以安装 SQLite 表单向导 [sqlite_installation](https://www.tutorialspoint.com/sqlite/sqlite_installation.htm) 。
*   要将数据从 [CSV](https://www.sqlitetutorial.net/wp-content/uploads/2016/05/city.csv) 导入 SQLite，您可以遵循以下步骤。您可以从 [cities.csv](https://gist.githubusercontent.com/deepakshrma/b87c5d9d1382bdb1a2bce1b2b03bcabc/raw/245d02679af4aa7998781ec369c0b09ceb7e8481/cities.csv) 下载样本 CSV。

将数据导入城市表后。您可以从数据库中读取数据，如下所示。

## **结论**

我刚刚接触了海洋的表面。有许多类型的数据源。但是，工作模型必须类似于上面列出的格式之一。我将尝试重温这篇文章，并添加更多的示例代码。

## 参考资料:

*   [https://medium . com/geek culture/building-truth-and-dare-game-with-python-80a8e 83 BBC 1b](https://medium.com/geekculture/building-truth-and-dare-game-with-python-80a8e83bbc1b)
*   [https://www . python tutorial . net/python-basics/python-read-CSV-file/](https://www.pythontutorial.net/python-basics/python-read-csv-file/)
*   【https://docs.python.org/3/library/typing.html 
*   [https://realpython.com/documenting-python-code/](https://realpython.com/documenting-python-code/)
*   [https://www . tutorialspoint . com/SQLite/SQLite _ create _ database . htm](https://www.tutorialspoint.com/sqlite/sqlite_create_database.htm)
*   [https://www.sqlitetutorial.net/sqlite-import-csv/](https://www.sqlitetutorial.net/sqlite-import-csv/)
*   [https://docs . python . org/3/reference/compound _ stmts . html # the-with-statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)
*   [https://docs.python.org/3/library/functions.html#open](https://docs.python.org/3/library/functions.html#open)