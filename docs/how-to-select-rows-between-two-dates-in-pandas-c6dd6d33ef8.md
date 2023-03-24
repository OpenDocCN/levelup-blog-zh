# 如何在熊猫中选择两个日期之间的行

> 原文：<https://levelup.gitconnected.com/how-to-select-rows-between-two-dates-in-pandas-c6dd6d33ef8>

## 熊猫中日期之间的行选择

![](img/ac8f3a5a352efa3a9a038495425f375b.png)

[Jerin J](https://unsplash.com/@jn1434?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/select?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

## 介绍

当处理时间序列数据或任何涉及日期(时间)的数据集时，我们通常需要根据某些特定条件过滤掉行。在今天的简短教程中，我们将展示如何选择特定日期范围内的行。

更具体地说，我们将展示如何使用:

*   布尔掩码和`loc`
*   `DatetimeIndex`和`loc`
*   `pandas.Series.between`方法
*   还有`query`法

首先，让我们创建一个示例数据框架，我们将在本教程中使用它来演示一些概念。

```
import pandas as pd df = pd.DataFrame(
    [
        (1, 'A', '28-02-2020', '30-01-2021', False),
        (2, 'B', '27-01-2019', '21-02-2020', True),
        (3, 'A', '05-02-2021', '01-01-2022', False),
        (4, 'D', '04-01-2019', '02-02-2021', False),
        (5, 'B', '15-12-2021', '25-12-2021', False),
        (6, 'C', '16-03-2021', '10-01-2022', True),
        (7, 'D', '21-05-2018', '01-03-2020', True),
        (8, 'A', '21-05-2019', '20-03-2021', False),
    ],
    columns=['colA', 'colB', 'colC', 'colD', 'colE']
) df[['colC', 'colD']] = df[['colC', 'colD']].apply(pd.to_datetime)print(df)
 ***colA colB       colC       colD   colE
0     1    A 2020-02-28 2021-01-30  False
1     2    B 2019-01-27 2020-02-21   True
2     3    A 2021-05-02 2022-01-01  False
3     4    D 2019-04-01 2021-02-02  False
4     5    B 2021-12-15 2021-12-25  False
5     6    C 2021-03-16 2022-10-01   True
6     7    D 2018-05-21 2020-01-03   True
7     8    A 2019-05-21 2021-03-20  False***
```

注意列`colC`和`colD`包含`datetime`对象:

```
print(df.dtypes)
colA             int64
colB            object
**colC    datetime64[ns]
colD    datetime64[ns]**
colE              bool
dtype: object
```

## 选择两个日期列之间的行

现在让我们假设我们想要过滤我们的示例数据帧，以便它只包含列`colC`下的日期在`02–02–2019`和`02–02–2021`之间的行。

第一个选项是使用一个**布尔掩码和** `**loc**` **属性。**

```
mask = (df['colC'] > '2019-2-2') & (df['colC'] <= '2021-2-2')
df_filtered = df.loc[mask]print(df_filtered)
 ***colA colB       colC       colD   colE
0     1    A 2020-02-28 2021-01-30  False
3     4    D 2019-04-01 2021-02-02  False
7     8    A 2019-05-21 2021-03-20  False***
```

请注意，以上内容几乎适用于任何类型的日期列`pd.Timestamp`、`datetime.datetime`和`np.datetime64`。

或者，您可以使用`[pandas.Series.between](https://pandas.pydata.org/docs/reference/api/pandas.Series.between.html)`方法，该方法将返回一个布尔向量，其中包含满足指定范围的行的`True`值，否则返回`False`。

```
df_filtered = df[df.colC.between('2019-2-2', '2021-2-2')]print(df_filtered)
 ***colA colB       colC       colD   colE
0     1    A 2020-02-28 2021-01-30  False
3     4    D 2019-04-01 2021-02-02  False
7     8    A 2019-05-21 2021-03-20  False***
```

## 使用索引选择两个日期之间的行

如果您计划频繁应用日期过滤器，那么更改 dataframe 的索引并将其设置为日期列可能会更明智。然后，您可以使用`[DatetimeIndex](https://pandas.pydata.org/docs/reference/api/pandas.DatetimeIndex.html)`来选择那些索引落在使用`loc`属性指定的范围内的行。

```
df = df.set_index(['colC'])
df_filtered = df.loc['2019-2-2':'2021-2-2']print(df_filtered)
 ***colA colB       colD   colE
colC
2020-02-28     1    A 2021-01-30  False
2019-04-01     4    D 2021-02-02  False
2019-05-21     8    A 2021-03-20  False***
```

## 查询日期列

最后，另一个选择是使用类似 SQL 表达式的`[query](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html)`方法，如下所示:

```
start_date = '2019-02-02'
end_date = '2021-02-02'
df_filtered = df.query('colC >= @start_date and colC <= @end_date')print(df_filtered)
 ***colA colB       colC       colD   colE
0     1    A 2020-02-28 2021-01-30  False
3     4    D 2019-04-01 2021-02-02  False
7     8    A 2019-05-21 2021-03-20  False***
```

## 最后的想法

在今天的简短教程中，我们展示了如何在日期列上过滤熊猫数据帧，并选择特定日期范围内的行。更具体地说，我们讨论了如何使用布尔掩码和带有`loc`属性的`DatetimeIndex`或者专门为此设计的名为`between`的特殊熊猫方法来实现这一点。最后，我们还展示了如何通过使用类似 SQL 的表达式查询数据帧来获得等效的结果。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**你可能也会喜欢**

[](https://towardsdatascience.com/save-trained-models-python-22a11376d975) [## 如何用 Python 在磁盘上保存训练好的模型

### 用 scikit 探索模型持久性——在 Python 中学习

towardsdatascience.com](https://towardsdatascience.com/save-trained-models-python-22a11376d975) [](https://towardsdatascience.com/pycache-python-991424aabad8) [## Python 中 __pycache__ 是什么？

### 了解运行 Python 代码时创建的 __pycache__ 文件夹

towardsdatascience.com](https://towardsdatascience.com/pycache-python-991424aabad8)