# 如何对熊猫数据帧分组以计算总和

> 原文：<https://levelup.gitconnected.com/how-to-group-by-pandas-dataframes-to-compute-sum-82a6bd890cbf>

## 使用 Group By 表达式聚合 pandas 数据框架中的列值

![](img/613a1d041a89942e558df37f744c6efb.png)

照片由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/object?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

## 介绍

子句最常用的应用之一是让用户计算一组给定数据中特定组值的总和。

在今天的简短教程中，我们将展示几种不同的方法来计算熊猫数据帧的 group by 表达式后的和。此外，我们将探讨如何对多个列执行分组操作，以及如何计算多个列的结果组的总和。

首先，让我们创建一个示例数据框架，我们将在本教程中引用它来演示一些概念和展示不同的方法。

```
import pandas as pd df = pd.DataFrame(
    [
        (1, 'A', 110, 10.0, True),
        (2, 'B', 540, 9.5, False),
        (3, 'A', 230, 12.1, True),
        (4, 'C', 115, 12.5, True),
        (5, 'C', 145, 21.1, False),
        (6, 'D', 321, 9.97, False),
        (7, 'A', 433, 7.85, True),
        (8, 'B', 100, 5.65, True),
        (9, 'B', 89, 8.0, False),
        (10, 'B', 190, 9.7, True),
        (11, 'C', 85, 6.4, True),
        (12, 'A', 75, 4.5, False),
    ],
    columns=['colA', 'colB', 'colC', 'colD', 'colE']
)print(df) ***colA colB  colC   colD   colE*** 0      1    A   110  10.00   True
1      2    B   540   9.50  False
2      3    A   230  12.10   True
3      4    C   115  12.50   True
4      5    C   145  21.10  False
5      6    D   321   9.97  False
6      7    A   433   7.85   True
7      8    B   100   5.65   True
8      9    B    89   8.00  False
9     10    B   190   9.70   True
10    11    C    85   6.40   True
11    12    A    75   4.50  False
```

## 使用 sum()方法

现在让我们假设我们想要根据列`colB`对数据帧进行分组，然后计算每个组的`colC`的总和。

我们需要做的是首先在想要的列上调用`groupby()`方法，然后通过分割`groupby()`的结果来调用`sum()`方法，以便指定我们想要计算总和的列。

```
>>> df.groupby('colB')['colC'].sum()
colB
A    848
B    919
C    345
D    321
Name: colC, dtype: int64
```

## 使用 agg()方法

或者，我们可以使用`agg()`方法来执行各种类型的聚合，包括 sum:

```
>>> df.groupby('colB')['colC'].agg('sum')
colB
A    848
B    919
C    345
D    321
Name: colC, dtype: int64
```

## 按多列分组

如果要在 group-by 表达式中考虑多个列，只需将列名指定为列表:

```
>>> df.groupby(['colB', 'colE'])['colC'].sum()
colB  colE
A     False     75
      True     773
B     False    629
      True     290
C     False    145
      True     200
D     False    321
Name: colC, dtype: int64
```

## 计算多列的总和

同样，如果要计算多个列的总和，只需在对 group-by 表达式的结果进行切片时包括所有列名:

```
>>> df.groupby('colB')['colC', 'colD'].sum()
      colC   colD
colB
A      848  34.45
B      919  32.85
C      345  40.00
D      321   9.97
```

## 最后的想法

在今天的简短教程中，我们讨论了用于计算数据帧中分组数据总和的`GROUP BY`子句。

此外，我们展示了两种不同的方法，您可以使用这两种方法来计算 pandas 数据框架中这些组的总和。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://towardsdatascience.com/how-to-merge-pandas-dataframes-221e49c41bec) [## 如何合并熊猫数据帧

### 对熊猫数据帧执行左、右、内和反连接

towardsdatascience.com](https://towardsdatascience.com/how-to-merge-pandas-dataframes-221e49c41bec) [](https://towardsdatascience.com/how-to-iterate-over-rows-in-a-pandas-dataframe-6aa173fc6c84) [## 如何迭代熊猫数据框架中的行

### 讨论如何在 pandas 中迭代行，以及为什么最好避免(如果可能的话)

towardsdatascience.com](https://towardsdatascience.com/how-to-iterate-over-rows-in-a-pandas-dataframe-6aa173fc6c84) [](https://towardsdatascience.com/how-to-efficiently-convert-a-pyspark-dataframe-to-pandas-8bda2c3875c3) [## 加快 PySpark 和 Pandas 数据帧之间的转换

### 将大火花数据帧转换为熊猫时节省时间

towardsdatascience.com](https://towardsdatascience.com/how-to-efficiently-convert-a-pyspark-dataframe-to-pandas-8bda2c3875c3)