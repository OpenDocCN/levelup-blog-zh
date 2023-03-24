# Python 熊猫教程:初学者完全入门

> 原文：<https://levelup.gitconnected.com/python-pandas-tutorial-a-complete-introduction-for-beginners-add7013095c2>

![](img/3fcf431863ebbe5504c1d3a44cbe1115.png)

功劳归于@AnalyticVidya

python 中的 Pandas 包是当今使用 Python 的数据科学家和分析人员最重要的工具。如果你正考虑在数据科学领域开始你的职业生涯，那么你可以接触 Python 的 Pandas 模块。

# 熊猫用来做什么？

熊猫有如此多的用途，以至于列出它不能做的事情而不是它们能做的事情可能是有意义的。有了熊猫，你可以清理你的数据，转换和分析它等等。例如，您在计算机上以 CSV 格式存储数据集，Pandas 将通过以表格形式显示数据框中的数据集来提取数据，您可以对该数据执行以下功能。

*   计算统计数据并回答有关数据的问题，如每列的平均值、中值、最大值或最小值是多少？
*   C 列中的数据分布是什么样的？
*   通过删除丢失的值和根据某些标准过滤行或列来清理数据
*   借助 Matplotlib 可视化数据。绘制条形图、线条、直方图、气泡等。
*   将清理和转换后的数据存储回 CSV、另一个文件或数据库

# 安装和导入

熊猫是非常容易安装使用 Pip 模块，打开您的命令提示符或终端根据您的操作系统，并使用以下命令。

`pip install pandas`

现在要导入 pandas 模块，只需简单地添加导入关键字和模块名称(Pandas ),并使用它作为关键字来缩短类名。

`import pandas as pd`

# 熊猫数据帧:

Pandas Dataframe 是一种表格，它按行和列组织数据，使其像一个二维数组。数据以表格形式排列，是一种大小可变的数据结构。因此它可以被修改。

要创建数据框，请检查以下代码。

**输出:**

```
Column1 Column2  Column3 
500      A        True
1000     Double   False
2000     python   True
3000     Course   False
```

在这个示例代码中，我们首先导入了这个模块，接下来，我们使用`pd.DataFrame()`创建了一个数据框，它将字典数据作为参数。我们在第一列输入数字，在第二列输入字符串，在第三列输入布尔值。最后，我们简单地用语句`print()`显示我们的数据框，正如您注意到的，输出是表格形式的。

也可以创建一个带有列表的数据框架。我们将创建一个包含随机名称的列表，并将其传递给数据框。看看下面的代码。

**输出:**

```
 0
0   John
1   Robert
2   Ron
3   Harry 
```

在这个例子中，我们创建了一个名为 list 的列表，它包含 4 个名字。然后，我们调用 DataFrame()方法，并将列表的名称作为参数传递给它。这是将列表转换成数据帧的地方。接下来，我们打印出数据帧，在结果中我们看到数据帧将第一列设置为 0 索引，最后一列设置为 `N-1`。

# 阅读 CSV

更多时候，你不需要写数据，而是需要从外部读取数据。Pandas 具有从 CSV 文件或 Excel 文件中读取数据的功能。Pandas 为我们提供了一个方法名`read_csv()`，它将参数作为 CSV 的名称。它还需要另一个参数，但常见的参数是 CSV 文件的名称。当您传递名称 pandas 时，从 CSV 读取数据，并将其存储在表格中。

**输出:**

```
Number     Type  Capacity
0    SSD   Premio      1800
1    KCN  Fielder      1500
2    USG     Benz      2200
3    TCH      BMW      2000
4    KBQ    Range      3500
5    TBD   Premio      1800
6    KCP     Benz      2200
```

## 数据查看:

熊猫给我们提供了一些工具，我们可以用它们来方便地浏览大量数据。我们要讨论的工具是`head()`、`tail()`、`info()`、`loc()`。

## 头

当你有大量的数据时，假设有数千行数据，检查数据的样本是非常困难的。如果我们能看到数据的前几行，那将会很有帮助。Pandas 为我们提供了 head()方法，它打印出数据的前几行。head()方法接受一个整数参数。比如，如果我们想要打印前 3 行，那么我们将 head 参数设置为 3，就像这样`head(3)`。但是如果我们让它为空，它将只返回前 5 行。

**输出:**

```
Number     Type  Capacity
0    SSD   Premio      1800
1    KCN  Fielder      1500
2    USG     Benz      2200
3    TCH      BMW      2000
4    KBQ    Range      3500
```

上面的命令只返回数据集的前 5 行，允许您检查少量的数据样本。

## 尾巴

tail 方法与 head 方法相反，它采用与 Head 方法相同的参数。它打印出最后几行数据。如果我们让它为空，它将返回最后 5 行。

**输出:**

```
Number     Type  Capacity
2    USG     Benz      2200
3    TCH      BMW      2000
4    KBQ    Range      3500
5    TBD   Premio      1800
6    KCP     Benz      2200
```

# 读取 Excel

到目前为止，我们已经学会了如何导入 CSV 文件并使用 pandas 来读取它。我们有另一个方法`read_excel`用来读取 excel 文件。以下代码将从 excel 文件中加载内容。

**输出:**

```
ID    Name      Dept  Salary
0   1    John       ICT    3000
1   2    Kate   Finance    2500
2   3  Joseph        HR    3500
3   4  George       ICT    2500
4   5    Lucy     Legal    3200
5   6   David   Library    2000
6   7   James        HR    2000
7   8   Alice  Security    1500
8   9   Bosco   Kitchen    1000
9  10    Mike       ICT    3300
```

## 通信线路（LinesofCommunication）

我们还有另一个神奇的工具，它的名字 loc()代表位置。我们使用 loc 方法从数据中读取特定的行和列。

**输出:**

```
Name  Salary
1   Kate    2500
4   Lucy    3200
7  Alice    1500
```

正如您在上面的代码输出中看到的，我们传递了两个列表参数，一个是特定的行，另一个是列的名称。

## 读取多张纸

有些时候我们要读取多张 excel 文件，假设我们要比较两张 excel 表格的数据。我们可以多次使用`read_excel()`方法，并传递 excel 文件名和工作表号。

在调用了`read_excel()`并传递了工作表名称之后，默认的工作表名称通常是 sheet1、sheet2、sheet3 等等。并将其存储在两个不同的变量中，熊猫将同时读取 excel 表格。接下来，我们可以通过在 print 语句中调用这些变量来打印出数据。

# 导出文件:

我们可以使用名为`.tocsv()`和`.toexcel()`的方法将我们的数据帧保存为 CSV 或 excel 格式，然后用数据帧调用那个方法，然后传递带有扩展名的文件名，检查下面的代码。

# 结论

到目前为止，我们学习了如何创建数据框、读取 CSV 和 excel 文件、数据查看技术以及如何在更短的时间内检查大量数据，并以不同的格式保存数据框。但是熊猫的力量并不止于此，它们还可以被用于数据分析、数据可视化等等。希望这篇文章对你有所帮助，欢迎留下你的回复。