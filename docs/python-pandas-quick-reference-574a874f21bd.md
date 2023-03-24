# Python 熊猫快速参考

> 原文：<https://levelup.gitconnected.com/python-pandas-quick-reference-574a874f21bd>

## 有用的 Python 熊猫功能的介绍性指南

![](img/85f0f8efd506b4c1f83787fc758c64d7.png)

我最近一直在使用 Python Pandas 库，这是一个如此巨大和令人印象深刻的库。我想记录我到目前为止学到和发现的东西，以供将来参考，我认为这对其他人也有帮助，特别是在数据科学领域。我假设你已经熟悉 Python 和熊猫了。

我想介绍 Pandas 中的两种主要数据类型。一个“系列”和一个“数据框架”。“系列”相当于 Python 列表。这是一组一维数据。“数据框架”可以与 Python 字典相提并论。一个“数据帧”由两个或多个“系列”(列)组成。

## 首字母缩写熊猫

```
**import pandas as pd**
```

## 创建系列

```
**my_series = pd.Series([0,1,2,3,4,5,6,7,8,9])
print (type(my_series))
print (my_series)**<class 'pandas.core.series.Series'>
0    0
1    1
2    2
3    3
4    4
5    5
6    6
7    7
8    8
9    9
dtype: int64**my_series = pd.Series(range(0,10))
print (type(my_series))
print (my_series)**<class 'pandas.core.series.Series'>
0    0
1    1
2    2
3    3
4    4
5    5
6    6
7    7
8    8
9    9
dtype: int64**my_series = pd.Series(['a','b','c','d','e','f','g','h','i','j'])
print (type(my_series))
print (my_series)**<class 'pandas.core.series.Series'>
0    a
1    b
2    c
3    d
4    e
5    f
6    g
7    h
8    i
9    j
dtype: object**my_index = list('abcde')
my_series = pd.Series([1,2,3,4,5], index=my_index)
print (type(my_series))
print (my_series)**<class 'pandas.core.series.Series'>
a    1
b    2
c    3
d    4
e    5
dtype: int64**my_dict = { 
    'K1': 'V1',
    'K2': 'V2',
    'K3': 'V3',
    'K4': 'V4',
    'K5': 'V5',
}****my_series = pd.Series(my_dict)
print (type(my_series))
print (my_series)**class 'pandas.core.series.Series'>
K1    V1
K2    V2
K3    V3
K4    V4
K5    V5
dtype: object**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']], columns = ['col1','col2','col3'])
print (type(my_dataframe))
print (my_dataframe)****my_series = my_dataframe['col2']
print (type(my_series))
print (my_series)**<class 'pandas.core.frame.DataFrame'>
  col1 col2 col3
0    a    b    c
1    d    e    f
2    g    h    i<class 'pandas.core.series.Series'>
0    b
1    e
2    h
Name: col2, dtype: object
```

## 创建数据框架

```
**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']])
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
   0  1  2
0  a  b  c
1  d  e  f
2  g  h  i**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']], columns = ['col1','col2','col3'])
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
  col1 col2 col3
0    a    b    c
1    d    e    f
2    g    h    i**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']], columns = ['col1','col2','col3'], index = ['row1', 'row2', 'row3'])
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
     col1 col2 col3
row1    a    b    c
row2    d    e    f
row3    g    h    i**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']])
my_dataframe.columns = ['col1','col2','col3']
my_dataframe.index = ['row1', 'row2', 'row3']
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
     col1 col2 col3
row1    a    b    c
row2    d    e    f
row3    g    h    i**my_dataframe = pd.DataFrame({
    'numbers': [ 'one', 'two', 'three', 'four', 'five' ],
    'letters': [ 'a', 'b', 'c', 'd', 'e' ]
})
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
  numbers letters
0     one       a
1     two       b
2   three       c
3    four       d
4    five       e**import numpy as np
nparray = np.array([1, 2, 3, 4, 5])
my_dataframe = pd.Series(nparray)
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.series.Series'>
0    1
1    2
2    3
3    4
4    5
dtype: int64**my_dataframe = pd.read_csv('./pandas.csv')**
**# optionally: df = pd.read_csv('./pandas.csv', encoding='utf-8')****# pathlib adds support for Windows, OSX and Linux
from pathlib import Path
file_path = Path('C:/Example') / 'pandas.csv'
df = pd.read_csv(file_path)**
```

请注意 DataFrame 列实际上是一个系列。

```
**my_dataframe = pd.DataFrame([['a','b','c'],['d','e','f'],['g','h','i']], columns = ['col1','col2','col3'])
print (type(my_dataframe['col1']))
print (my_dataframe['col1'])**<class 'pandas.core.series.Series'>
0    a
1    d
2    g
Name: col1, dtype: object
```

## 编辑数据帧

```
**my_dataframe = pd.DataFrame({
    'name': [ 'Mike', 'Eva', 'George' ]
})****print (my_dataframe)**name
0    Mike
1     Eva
2  George**# add new column 'age'
my_dataframe['age'] = [ 41, 34, 3 ]****print (my_dataframe)** name   age
0    Mike   41
1     Eva   34
2  George    3**# drops column 'age'
my_dataframe = my_dataframe.drop(columns=['age'])**name
0    Mike
1     Eva
2  George**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
print (my_dataframe)** a      b      c      d      e      f
0  False  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3  False  False  False  False  False  False
4  False  False  False  False  False  False
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7  False  False  False  False  False  False
8  False  False  False  False  False  False
9  False  False  False  False  False  False**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
my_dataframe.loc[0, 'a'] = True
print (my_dataframe)** a      b      c      d      e      f
0   True  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3  False  False  False  False  False  False
4  False  False  False  False  False  False
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7  False  False  False  False  False  False
8  False  False  False  False  False  False
9  False  False  False  False  False  False**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
my_dataframe.loc[3] = True** a      b      c      d      e      f
0  False  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3   True   True   True   True   True   True
4  False  False  False  False  False  False
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7  False  False  False  False  False  False
8  False  False  False  False  False  False
9  False  False  False  False  False  False**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
my_dataframe.loc[7, ['a', 'c', 'f']] = True
print (my_dataframe)** a      b      c      d      e      f
0  False  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3  False  False  False  False  False  False
4  False  False  False  False  False  False
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7   True  False   True  False  False   True
8  False  False  False  False  False  False
9  False  False  False  False  False  False**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
my_dataframe.loc[9] = [True, False, True, False, True, False]
print (my_dataframe)** a      b      c      d      e      f
0  False  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3  False  False  False  False  False  False
4  False  False  False  False  False  False
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7  False  False  False  False  False  False
8  False  False  False  False  False  False
9   True  False   True  False   True  False**columns = list('abcdef')
data = [[False for j in columns] for i in range(0, 10)]
my_dataframe = pd.DataFrame(data, columns=columns)
my_dataframe.loc[3:4, 'd':'f'] = [['a','a','a'], ['b','b','b']]
print (my_dataframe)** a      b      c      d      e      f
0  False  False  False  False  False  False
1  False  False  False  False  False  False
2  False  False  False  False  False  False
3  False  False  False      a      a      a
4  False  False  False      b      b      b
5  False  False  False  False  False  False
6  False  False  False  False  False  False
7  False  False  False  False  False  False
8  False  False  False  False  False  False
9  False  False  False  False  False  False**# following on from above
print (my_dataframe[my_dataframe.d.isin(['a','b'])])** a      b      c  d  e  f
3  False  False  False  a  a  a
4  False  False  False  b  b  b**my_dataframe = my_dataframe[my_dataframe.d.isin(['a','b'])]
print('Number of rows: {}, number of columns: {}'.format(my_dataframe.shape[0],my_dataframe.shape[1]))**Number of rows: 2, number of columns: 6
```

## 处理南的

使用数据帧的最大问题之一是 NaN 值。我认为有两种主要的方法来处理这个问题。从数据集中排除它们或用默认值填充它们。

```
# remove NaN values
# [refer to official docs for options](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html)
my_dataframe['col1'] = mydataframe['col1'].dropna()# default NaN values to 0
my_dataframe['col1'] = mydataframe['col1'].fillna(0)
```

## 处理不正确的数据类型(列数据类型)

您可能遇到的另一个问题是 DataFrame 列类型被错误地标识。我在做的一个项目中遇到了这个问题。我有一个数据列被检测为字符串，但我不得不将其重新转换为“日期时间”。

```
my_dataframe['datetime'] = pd.to_datetime(my_dataframe['datetime'])
```

## 转换数据帧

```
**my_dataframe = pd.DataFrame({
    'names': [ 'Mike', 'Eva', 'George' ],
    'gender': [ 'Male', 'Female', 'Male' ]
})****print (my_dataframe)** names   gender
0    Mike    Male
1     Eva  Female
2  George    Male**my_dataframe['gender_letter'] = my_dataframe.gender.map(lambda g: 'f' if g == 'Female' else 'm')**names        gender    gender_letter
0    Mike    Male      m
1     Eva  Female      f
2  George    Male      m
```

## 连接数据帧

```
**my_dataframe1 = pd.DataFrame([
    [1,1,1],
    [2,2,2],
    [3,3,3]
])****print (my_dataframe1)**0  1  2
0  1  1  1
1  2  2  2
2  3  3  3**my_dataframe2 = pd.DataFrame([
    [4,4,4],
    [5,5,5],
    [6,6,6]
])****print (my_dataframe2)**0  1  2
0  4  4  4
1  5  5  5
2  6  6  6**my_dataframe = pd.concat([my_dataframe1, my_dataframe2]).reset_index(drop = True)****print (my_dataframe)** 0  1  2
0  1  1  1
1  2  2  2
2  3  3  3
3  4  4  4
4  5  5  5
5  6  6  6
```

## 连接数据框架

这与 SQL 内部、外部、左侧和右侧连接非常相似。

```
pd.merge(left_df, right_df, on = "ID_COLUMN", how = "inner")
pd.merge(left_df, right_df, on = "ID_COLUMN", how = "outer")
pd.merge(left_df, right_df, on = "ID_COLUMN", how = "left")
pd.merge(left_df, right_df, on = "ID_COLUMN", how = "right")
```

## 检查数据帧

```
**print (my_dataframe.info())**class 'pandas.core.frame.DataFrame'>
Index: 4 entries, R1 to R4
Data columns (total 4 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   C1      4 non-null      object
 1   C2      4 non-null      object
 2   C3      4 non-null      object
 3   C4      4 non-null      object
dtypes: object(4)
memory usage: 160.0+ bytes
None**print (my_dataframe.head(2))** C1 C2 C3 C4
R1  a  b  c  d
R2  e  f  g  h**print (my_dataframe.tail(2))** C1 C2 C3 C4
R3  i  j  k  l
R4  m  n  o  p**# 3 rows, 4 columns
print (my_dataframe.shape)**(3, 4)**print (my_dataframe.describe())**C1 C2 C3 C4
count   3  3  3  3
unique  3  3  3  3
top     a  j  g  h
freq    1  1  1  1
```

## 切片系列

这与使用 Python 列表非常相似。

```
**my_series = pd.Series(['a','b','c','d','e','f','g','h','i','j'])****# all rows
print (my_series[:])**0    a
1    b
2    c
3    d
4    e
5    f
6    g
7    h
8    i
9    j
dtype: object**# first row
print (my_series[0])**a**# first four rows
print (my_series[0:4])**0    a
1    b
2    c
3    d
dtype: object**# last row
print (my_series[-1:])**9    j
dtype: object**# last 4 rows
print (my_series[-4:])**6    g
7    h
8    i
9    j
dtype: object**# middle four rows
print (my_series[3:7])**3    d
4    e
5    f
6    g
dtype: object
```

## 分割数据帧

您拥有与系列相同的功能来对行进行切片，但是现在您还拥有列维度。这就是你要用**的地方。锁定**和**。iloc** “大部分是。

我创建了这个数据框架，我将把它作为一个例子。

```
**my_dataframe = pd.DataFrame([['a','b','c','d'],['e','f','g','h'],['i','j','k','l'],['m','n','o','p']], columns = ['A','B','C','D'])
print (type(my_dataframe))
print (my_dataframe)**<class 'pandas.core.frame.DataFrame'>
   A  B  C  D
0  a  b  c  d
1  e  f  g  h
2  i  j  k  l
3  m  n  o  p
```

选择行和列…

```
**# columns C2 and C3**
**print (my_dataframe[['C2','C3']])** C2 C3
R1  b  c
R2  f  g
R3  j  k
R4  n  o**# rows R2 and R3
print (my_dataframe[1:3])** C1 C2 C3 C4
R2  e  f  g  h
R3  i  j  k  l**# all rows, columns C2 and C3
print (my_dataframe.loc[:,'C2':'C3'])** C2 C3
R1  b  c
R2  f  g
R3  j  k
R4  n  o**# rows R3 and R4, columns C2 and C3
print (my_dataframe.loc['R3':'R4','C2':'C3'])** C2 C3
R3  j  k
R4  n  o**# all rows, column 2
print (my_dataframe.iloc[:,1])**R1    b
R2    f
R3    j
R4    n
Name: C2, dtype: object**# rows 2 and 3, columns 2 and 4
print (my_dataframe.iloc[1:3,2:4])** C3 C4
R2  g  h
R3  k  l**# Dynamic selecting of columns by name
year_data = my_dataframe[[col for col in my_dataframe.columns if col.startswith('Year')]]**
```

## 过滤系列

您可以通过创建布尔序列并将其传递给序列来添加筛选器。

```
**my_series = pd.Series(range(1,10))
print(my_series)****is_over_5 = my_series > 5
print (is_over_5)****print (my_series[is_over_5])**1
1    2
2    3
3    4
4    5
5    6
6    7
7    8
8    9
dtype: int640    False
1    False
2    False
3    False
4    False
5     True
6     True
7     True
8     True
dtype: bool5    6
6    7
7    8
8    9
dtype: int64
```

## 过滤数据帧

您可以通过创建布尔序列并将其传递给数据帧来添加过滤器。

```
**my_dataframe = pd.DataFrame([
    [1,1,1,1,1],
    [2,2,2,2,2],
    [3,3,3,3,3],
    [4,4,4,4,4],
    [5,5,5,5,5]
])****print (my_dataframe)****print (my_dataframe.loc[my_dataframe[1] > 2])** 0  1  2  3  4
0  1  1  1  1  1
1  2  2  2  2  2
2  3  3  3  3  3
3  4  4  4  4  4
4  5  5  5  5  5 0  1  2  3  4
2  3  3  3  3  3
3  4  4  4  4  4
4  5  5  5  5  5
```

## 重塑数据帧

```
**my_dataframe = pd.DataFrame({
    'year': [ 2020, 2020, 2020, 2019, 2019, 2019 ],
    'temp': [ 'H', 'M', 'L', 'H', 'M', 'L' ],
    'month': [ 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun' ] 
})****print (my_dataframe)
print (my_dataframe.pivot(index = 'year', columns = 'temp', values='month'))** year    temp   month
0  2020    H      Jan
1  2020    M      Feb
2  2020    L      Mar
3  2019    H      Apr
4  2019    M      May
5  2019    L      Juntemp    H     L     M
year               
2019    Apr   Jun   May
2020    Jan   Mar   Feb**print (my_dataframe.melt(id_vars = ['year'], value_vars = ['month']))** year    variable value
0  2020    month    Jan
1  2020    month    Feb
2  2020    month    Mar
3  2019    month    Apr
4  2019    month    May
5  2019    month    Jun**print (my_dataframe.melt(id_vars = ['year'], value_vars = ['month'], var_name = 'Variable Column', value_name = 'Value Column'))** year           Variable Column    Value Column
0  2020           month              Jan
1  2020           month              Feb
2  2020           month              Mar
3  2019           month              Apr
4  2019           month              May
5  2019           month              Jun
```

## 分组和聚集数据帧

```
**my_dataframe = pd.DataFrame({
    'year': [ 2020, 2020, 2020, 2019, 2019, 2019 ],
    'temp': [ 'H', 'M', 'L', 'H', 'M', 'L' ],
    'month': [ 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun' ] 
})****print (my_dataframe)****df_group = my_dataframe.groupby('year')
print (df_group.first())** year    temp   month
0  2020    H      Jan
1  2020    M      Feb
2  2020    L      Mar
3  2019    H      Apr
4  2019    M      May
5  2019    L      Jun temp  month
year           
2019    H     Apr
2020    H     Jan**print (df_group.get_group('H'))** year   month
0  2020   Jan
3  2019   Apr**df_group = my_dataframe.groupby(['year','temp'])
print (df_group.first())**year temp   month      
2019 H      Apr
     L      Jun
     M      May
2020 H      Jan
     L      Mar
     M      Feb**my_dataframe = pd.DataFrame({
    'C1': [ 1, 2, 3, 4, 5 ],
    'C2': [ 6, 7, 8, 9, 10 ],
    'C3': [ 11, 12, 13, 14, 15 ] 
})****print (my_dataframe)** C1  C2  C3
0   1   6  11
1   2   7  12
2   3   8  13
3   4   9  14
4   5  10  15**print (my_dataframe.aggregate(['sum','min','max']))** C1  C2  C3
sum  15  40  65
min   1   6  11
max   5  10  15**print (my_dataframe.aggregate({ 'C1': ['min','max'], 'C2': ['sum'] }))** C1    C2
max  5.0   NaN
min  1.0   NaN
sum  NaN  40.0
```

我希望你觉得这篇文章有趣并且有用。如果您想随时了解情况，请不要忘记关注我，注册我的[电子邮件通知](https://whittle.medium.com/subscribe)。

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作？*** [***咱们上领英***](https://www.linkedin.com/in/miwhittle/) 连线
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***