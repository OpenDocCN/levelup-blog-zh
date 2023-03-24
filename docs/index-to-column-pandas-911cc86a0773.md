# 如何在 Pandas 中将索引转换为列

> 原文：<https://levelup.gitconnected.com/index-to-column-pandas-911cc86a0773>

## 将索引和多索引转换为 pandas 数据框架列

![](img/b559bb792f26b165dddd2fd33e518420.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/add?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 介绍

pandas 数据帧的索引是一个不可变的序列，用于索引和对齐。即使没有显式定义，pandas 也会隐式地为创建的数据帧创建一个数字索引。此外，pandas 支持多索引(也称为层次索引)。您可以将`MultiIndex`视为一个元组数组，其中每个元组都是唯一的。多维索引通常出现在高维数据中。

在今天的简短教程中，我们将展示**如何将一只熊猫** `**Index**` **或** `**MultiIndex**` **转换成特定熊猫数据帧的实际列**。

首先，让我们创建两个数据帧——一个有一个索引，另一个有`MultiIndex`。

```
import pandas as pd # pandas DF with a single index
df_1 = pd.DataFrame(
    [
        (1, 'A', 10.1),
        (2, 'B', 15.2),
        (3, 'C', 12.2),
        (4, 'D', 10.0),
        (5, 'E', 5.0),
    ],
    columns=['col1', 'col2', 'col3'],
    index=['AA', 'BB', 'CC', 'DD', 'EE'],
)print(df_1) ***col1 col2  col3
AA     1    A  10.1
BB     2    B  15.2
CC     3    C  12.2
DD     4    D  10.0
EE     5    E   5.0*** # pandas DF with MultiIndex
df_2 = pd.DataFrame(
    [
        (1, 'A', 10.1),
        (2, 'B', 15.2),
        (3, 'C', 12.2),
        (4, 'D', 10.0),
        (5, 'E', 5.0),
    ],
    columns=['col1', 'col2', 'col3'],
    index=pd.MultiIndex.from_arrays(
         [
             ['AA', 'BB', 'CC', 'DD', 'EE'],
             [100, 200, 300, 400, 500]
         ],
         names=['col0', 'col4']
    )
)print(df_2)
 ***col1 col2  col3
col0 col4
AA   100      1    A  10.1
BB   200      2    B  15.2
CC   300      3    C  12.2
DD   400      4    D  10.0
EE   500      5    E   5.0***
```

## 将索引转换为 DataFrame 列

我们的第一个选择是通过简单地选择`df.index`来插入一个新列:

```
**df_1['col0'] = df.index**print(df)
 *col1 col2  col3 col0
AA     1    A  10.1   AA
BB     2    B  15.2   BB
CC     3    C  12.2   CC
DD     4    D  10.0   DD
EE     5    E   5.0   EE*
```

即使我们成功地插入了包含相应索引值的新列，索引本身也将保持不变。

相反，我们可以使用`[reset_index()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.reset_index.html)`方法来重置索引或只是它的一个级别。

```
**df_1.reset_index(inplace=True)** print(df) *index  col1 col2  col3
0    AA     1    A  10.1
1    BB     2    B  15.2
2    CC     3    C  12.2
3    DD     4    D  10.0
4    EE     5    E   5.0*
```

默认情况下，`reset_index()`将创建一个以索引名命名的新列。如果您想为新列指定一个特定的名称，只需运行:

```
**df_1.rename_axis('col0').reset_index(inplace=True)** print(df) *col0  col1 col2  col3
0   AA     1    A  10.1
1   BB     2    B  15.2
2   CC     3    C  12.2
3   DD     4    D  10.0
4   EE     5    E   5.0*
```

## 将多索引转换为 DataFrame 列

`reset_index()`也适用于多索引的数据帧。默认情况下，该方法会将`MultiIndex`的所有级别转换为列:

```
**df_2.rename_axis(inplace=True)** print(df) *col0  col4  col1 col2  col3
0   AA   100     1    A  10.1
1   BB   200     2    B  15.2
2   CC   300     3    C  12.2
3   DD   400     4    D  10.0
4   EE   500     5    E   5.0*
```

同样，您可以通过调用`rename()`来重命名新创建的列:

```
**df_2 = df_2 \
  .rename_axis() \** **.rename(columns={'col0':'bar', 'col4': 'foo'})** print(df) *bar  foo  col1 col2  col3
0  AA  100     1    A  10.1
1  BB  200     2    B  15.2
2  CC  300     3    C  12.2
3  DD  400     4    D  10.0
4  EE  500     5    E   5.0*
```

现在，您可以选择只转换多索引的子集。如果是这种情况，您需要指定`level`参数，该参数定义要重置`MultiIndex`的哪个(哪些)级别。

```
**# Reset only first level of MultiIndex
df_2.rename_axis(level=0, inplace=True)** print(df_2) *col0  col1 col2  col3
col4
100    AA     1    A  10.1
200    BB     2    B  15.2
300    CC     3    C  12.2
400    DD     4    D  10.0
500    EE     5    E   5.0*# Reset only second level of MultiIndex
**df_2.rename_axis(level=1, inplace=True)** print(df_2) *col4  col1 col2  col3
col0
AA     100     1    A  10.1
BB     200     2    B  15.2
CC     300     3    C  12.2
DD     400     4    D  10.0
EE     500     5    E   5.0*
```

## 最后的想法

在今天的简短指南中，我们在 pandas 数据框架的上下文中讨论了`Index`和`MultiIndex`，并展示了如何轻松地将索引转换为实际的列。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**你可能也会喜欢**

[](https://towardsdatascience.com/how-to-efficiently-convert-a-pyspark-dataframe-to-pandas-8bda2c3875c3) [## 加快 PySpark 和 Pandas 数据帧之间的转换

### 将大火花数据帧转换为熊猫时节省时间

towardsdatascience.com](https://towardsdatascience.com/how-to-efficiently-convert-a-pyspark-dataframe-to-pandas-8bda2c3875c3) [](https://towardsdatascience.com/loc-vs-iloc-in-pandas-92fc125ed8eb) [## 熊猫中的 loc 与 iloc

towardsdatascience.com](https://towardsdatascience.com/loc-vs-iloc-in-pandas-92fc125ed8eb)