# 熊猫基础知识备忘单(2023)-用于数据科学的 Python

> 原文：<https://levelup.gitconnected.com/pandas-basics-cheat-sheet-2023-python-for-data-science-b59fb7786b4d>

## 初学者学习熊猫的绝对基础 2022

![](img/035e882d5f550da880d9fbfa3c24c576.png)

照片来自 Unsplash，由 Johannes Groll 拍摄

Pandas 库是 Python 中最强大的库之一。它构建于 NumPy 之上，为 Python 编程语言提供了易于使用的数据结构和数据分析工具。

查看以下部分，了解熊猫提供的各种功能和工具。

> ***章节:***1。[熊猫数据结构](#243c)
> 2。[空投](#2c12)3。[排序&排名](#3f07)排名
> 4。[检索系列/数据帧信息](#35cf)
> 5。[数据帧摘要](#7522)
> 6。[选择](#f66a)7
> 。[应用功能](#9c96)
> 8。[数据对齐](#c3a5)9。[输入/输出](#d8f0)

# 熊猫数据结构

熊猫图书馆围绕着两种主要类型的数据结构。第一个是名为**系列**的一维数组，第二个是名为**数据帧**的二维表格。

*   系列-一维标签数组

```
>>> s = pd.Series([3, -5, 7, 4], index = ['a','b','c','d'])
 a    3
 b   -5
 c    7
 d    4
```

*   数据框——一种二维标记数据结构

```
>>> data = {'Country':['Belgium','India','Brazil'], 'Capital':['Brussels','New Delhi','Brasilia'], 'Population':['111907','1303021','208476']}>>> df = pd.DataFrame(data, columns = ['Country','Capital','Population']) Country    Capital Population
0  Belgium   Brussels     111907
1    India  New Delhi    1303021
2   Brazil   Brasilia     208476
```

# 落下

在本节中，您将了解如何从序列中移除特定值，以及如何从数据框中移除列或行。

以下代码中的 **s** 和 **df** 在本节中用作序列和数据帧的示例。

```
>>> sa    6
b   -5
c    7
d    4>>> df Country    Capital  Population
0  Belgium   Brussels      111907
1    India  New Delhi     1303021
2   Brazil   Brasilia      208476
```

*   从行中删除值(轴= 0)

```
>>> s.drop(['a','c']) b   -5
d    4
```

*   从列中删除值(轴= 1)

```
>>> df.drop('Country', axis = 1) Capital Population
0   Brussels     111907
1  New Delhi    1303021
2   Brasilia     208476
```

# 排序和排名

在本节中，您将学习如何按索引或列对数据框进行排序，以及如何对列值进行排序。

在本节中，以下代码中的 **df** 用作示例数据帧。

```
>>> df      Country    Capital  Population
0  Belgium   Brussels      111907
1    India  New Delhi     1303021
2   Brazil   Brasilia      208476
```

*   沿坐标轴按标签排序

```
>>> df.sort_index() Country    Capital Population
0  Belgium   Brussels     111907
1    India  New Delhi    1303021
2   Brazil   Brasilia     208476
```

*   沿坐标轴按值排序

```
>>> df.sort_values(by = 'Country') Country    Capital Population
0  Belgium   Brussels     111907
2   Brazil   Brasilia     208476
1    India  New Delhi    1303021
```

*   给条目分配等级

```
>>> df.rank() Country  Capital  Population
0      1.0      2.0         1.0
1      3.0      3.0         2.0
2      2.0      1.0         3.0
```

# 检索系列/数据帧信息

在本节中，您将了解如何从包含维度、列名、列类型和索引范围的数据框中检索信息。

下面代码中的 **df** 在本节中用作示例数据帧。

```
>>> df Country    Capital  Population
0  Belgium   Brussels      111907
1    India  New Delhi     1303021
2   Brazil   Brasilia      208476
```

*   (行，列)

```
>>> df.shape
 (3, 3)
```

*   描述索引

```
>>> df.index
 RangeIndex(start=0, stop=3, step=1)
```

*   描述数据框架列

```
>>> df.columns
 Index(['Country', 'Capital', 'Population'], dtype='object')
```

*   数据帧信息

```
>>> df.info()
 <class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 3 columns):
Country       3 non-null object
Capital       3 non-null object
Population    3 non-null object
dtypes: object(3)
memory usage: 152.0+ bytes
```

*   非 NA 值的数量

```
>>> df.count()Country       3
Capital       3
Population    3
```

# 数据帧摘要

在本节中，您将了解如何检索数据框的汇总统计数据，其中包括每列的总和、每列的最小/最大值、每列的平均值等。

在本节中，以下代码中的 **df** 用作数据帧的示例。

```
>>> df Even  Odd
0     2    1
1     4    3
2     6    5
```

*   值的总和

```
>>> df.sum()
Even    12
Odd      9
```

*   值的累积和

```
>>> df.cumsum() Even  Odd
0     2    1
1     6    4
2    12    9
```

*   最小值

```
>>> df.min()
Even    2
Odd     1
```

*   最大值

```
>>> df.max()
Even    6
Odd     5
```

*   汇总统计数据

```
>>> df.describe() Even  Odd
count   3.0  3.0
mean    4.0  3.0
std     2.0  2.0
min     2.0  1.0
25%     3.0  2.0
50%     4.0  3.0
75%     5.0  4.0
max     6.0  5.0
```

*   值的平均值

```
>>> df.mean()
Even    4.0
Odd     3.0
```

*   值的中间值

```
>>> df.median()
Even    4.0
Odd     3.0
```

# 选择

在本节中，您将学习如何从系列和数据框中检索特定值。

以下代码中的 **s** 和 **df** 在本节中用作序列和数据帧的示例。

```
>>> sa    6
b   -5
c    7
d    4>>> df      Country    Capital  Population
0  Belgium   Brussels      111907
1    India  New Delhi     1303021
2   Brazil   Brasilia      208476
```

*   获取一个元素

```
>>> s['b']
 -5
```

*   获取数据帧的子集

```
>>> df[1:] Country    Capital Population
1   India  New Delhi    1303021
2  Brazil   Brasilia     208476
```

*   按行和列选择单个值

```
>>> df.iloc[0,0]
 'Belgium'
```

*   通过行和列标签选择单个值

```
>>> df.loc[0,'Country']
 'Belgium'
```

*   选择子集行的单行

```
>>> df.ix[2]Country         Brazil
Capital       Brasilia
Population      208476
```

*   选择列子集的单个列

```
>>> df.ix[:,'Capital']0     Brussels
1    New Delhi
2     Brasilia
```

*   选择行和列

```
>>> df.ix[1,'Capital']
 'New Delhi'
```

*   使用过滤器调整数据帧

```
>>> df[df['Population'] > 120000] Country    Capital  Population
1   India  New Delhi     1303021
2  Brazil   Brasilia      208476
```

*   将系列 s 的索引 a 设置为 6

```
>>> s['a'] = 6 a    6
 b   -5
 c    7
 d    4
```

# 应用函数

在本节中，您将学习如何对数据框或特定列的所有值应用函数。

在本节中，以下代码中的 df 被用作数据帧的示例。

```
>>> df Even  Odd
0     2    1
1     4    3
2     6    5
```

*   应用功能

```
>>> df.apply(lambda x: x*2) Even  Odd
0     4    2
1     8    6
2    12   10
```

# 数据调整

在本节中，您将学习如何对两个具有不同索引的序列进行加、减和除操作。

以下代码中的 **s** 和 **s3** 在本节中用作序列示例。

```
>>> sa    6
b   -5
c    7
d    4>>> s3a    7
c   -2
d    3
```

*   内部数据对齐

```
>>> s + s3a    13.0
b     NaN
c     5.0
d     7.0#NA values are introduced in the indices that don't overlap
```

*   使用填充方法的算术运算

```
>>> s.add(s3, fill_value = 0)a    13.0
b    -5.0
c     5.0
d     7.0>>> s.sub(s3, fill_value = 2)a   -1.0
b   -7.0
c    9.0
d    1.0>>> s.div(s3, fill_value = 4)a    0.857143
b   -1.250000
c   -3.500000
d    1.333333
```

# 进/出

在本节中，您将学习如何使用 Pandas 将 CSV 文件、Excel 文件和 SQL 查询读入 Python。您还将学习如何将数据框从 Pandas 导出到 CSV 文件、Excel 文件和 SQL 查询中。

*   读取 CSV 文件

```
>>> pd.read_csv('file.csv')
```

*   写入 CSV 文件

```
>>> df.to_csv('myDataFrame.csv')
```

*   读取 Excel 文件

```
>>> pd.read_excel('file.xlsx')
```

*   写入 Excel 文件

```
>>> pd.to_excel('dir/'myDataFrame.xlsx')
```

*   从同一个文件中读取多个工作表

```
>>> xlsx = pd.ExcelFile('file.xls')>>> df = pd.read_excel(xlsx, Sheet1')
```

*   读取 SQL 查询

```
>>> from sqlalchemy import create_engine
>>> engine = create_engine('sqlite:///:memory:')
>>> pd.read_sql('SELECT * FROM my_table;', engine)
>>> pd.read_sql_table('my_table', engine)
```

*   写入 SQL 查询

```
>>> pd.to_sql('myDF', engine)
```

# 结论

现在和可预见的将来，Python 都是数据科学领域的佼佼者。关于熊猫的知识是其最强大的图书馆之一，也是当今数据科学家的一项要求。

开始的时候用这个小抄作为指南，需要的时候再回头看，你就能很好地掌握熊猫图书馆了。

[**与 5k+人一起加入我的电子邮件列表，免费获得“2023 年如何学习数据科学 cheat sheet”**](https://pages.christopherzita.com/how-to-learn-datascience)