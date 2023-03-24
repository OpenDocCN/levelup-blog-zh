# 用于数据科学的 Python NumPy/Pandas

> 原文：<https://levelup.gitconnected.com/python-numpy-pandas-for-data-science-4b548de8517a>

## 介绍流行的数据科学图书馆、NumPy 和 Pandas

![](img/417bf0963fbf20c382e953c8b28cb5c9.png)

## 介绍熊猫和熊猫

NumPy 是 Python 的一个库，增加了对大型多维数组和矩阵的支持，以及对这些数组进行操作的大量高级数学函数。

Pandas 是一个基于 NumPy 包的高级数据操作工具。Pandas 中的关键数据结构被称为数据帧。数据框架功能强大得令人难以置信，因为它们允许您以观察行和列的形式存储和操作表格数据。

## 安装和使用 NumPy 和 Pandas

首先，我们需要使用 Python 包管理器安装“ **numpy** ”和“**熊猫**”。

```
% **python3 -m pip install numpy pandas**
```

我们将它们包含在代码中。

```
import numpy as np
import pandas as pd
```

别名“ **numpy** ”到“ **np** ”和“**熊猫**”和“ **pd** ”是可选的，推荐使用。

## 使用 NumPy

NumPy 包含了大量有用的数学函数和方法。

例如，如果你想计算正弦**或余弦**或**或**正弦** (θ)和**余弦** (θ)，你可以这样做。**

```
print (np.sin(1))
**0.8414709848078965**print (np.cos(1))
**0.5403023058681398**
```

或者你可以做一些更奇特的事情，像这样…

```
print (np.pi)
**3.141592653589793**X = np.linspace(-np.pi, np.pi, 256, endpoint=True)print (np.sin(X))
**[-1.22464680e-16 -2.46374492e-02 -4.92599411e-02 -7.38525275e-02
 -9.84002783e-02 -1.22888291e-01 -1.47301698e-01 -1.71625679e-01
 -1.95845467e-01 -2.19946358e-01 -2.43913720e-01 -2.67733003e-01
 -2.91389747e-01 -3.14869589e-01 -3.38158275e-01 -3.61241666e-01
 -3.84105749e-01 -4.06736643e-01 -4.29120609e-01 -4.51244057e-01
 -4.73093557e-01 -4.94655843e-01 -5.15917826e-01 -5.36866598e-01
 -5.57489439e-01 -5.77773831e-01 -5.97707459e-01 -6.17278221e-01
 -6.36474236e-01 -6.55283850e-01 -6.73695644e-01 -6.91698439e-01
 -7.09281308e-01 -7.26433574e-01 -7.43144825e-01 -7.59404917e-01
 -7.75203976e-01 -7.90532412e-01 -8.05380919e-01 -8.19740483e-01
 -8.33602385e-01 -8.46958211e-01 -8.59799851e-01 -8.72119511e-01
 -8.83909710e-01 -8.95163291e-01 -9.05873422e-01 -9.16033601e-01
 -9.25637660e-01 -9.34679767e-01 -9.43154434e-01 -9.51056516e-01
 -9.58381215e-01 -9.65124085e-01 -9.71281032e-01 -9.76848318e-01
 -9.81822563e-01 -9.86200747e-01 -9.89980213e-01 -9.93158666e-01
 -9.95734176e-01 -9.97705180e-01 -9.99070481e-01 -9.99829250e-01
 -9.99981027e-01 -9.99525720e-01 -9.98463604e-01 -9.96795325e-01
 -9.94521895e-01 -9.91644696e-01 -9.88165472e-01 -9.84086337e-01
 -9.79409768e-01 -9.74138602e-01 -9.68276041e-01 -9.61825643e-01
 -9.54791325e-01 -9.47177357e-01 -9.38988361e-01 -9.30229309e-01
 -9.20905518e-01 -9.11022649e-01 -9.00586702e-01 -8.89604013e-01
 -8.78081248e-01 -8.66025404e-01 -8.53443799e-01 -8.40344072e-01
 -8.26734175e-01 -8.12622371e-01 -7.98017227e-01 -7.82927610e-01
 -7.67362681e-01 -7.51331890e-01 -7.34844967e-01 -7.17911923e-01
 -7.00543038e-01 -6.82748855e-01 -6.64540179e-01 -6.45928062e-01
 -6.26923806e-01 -6.07538946e-01 -5.87785252e-01 -5.67674716e-01
 -5.47219547e-01 -5.26432163e-01 -5.05325184e-01 -4.83911424e-01
 -4.62203884e-01 -4.40215741e-01 -4.17960345e-01 -3.95451207e-01
 -3.72701992e-01 -3.49726511e-01 -3.26538713e-01 -3.03152674e-01
 -2.79582593e-01 -2.55842778e-01 -2.31947641e-01 -2.07911691e-01
 -1.83749518e-01 -1.59475791e-01 -1.35105247e-01 -1.10652682e-01
 -8.61329395e-02 -6.15609061e-02 -3.69514994e-02 -1.23196595e-02
  1.23196595e-02  3.69514994e-02  6.15609061e-02  8.61329395e-02
  1.10652682e-01  1.35105247e-01  1.59475791e-01  1.83749518e-01
  2.07911691e-01  2.31947641e-01  2.55842778e-01  2.79582593e-01
  3.03152674e-01  3.26538713e-01  3.49726511e-01  3.72701992e-01
  3.95451207e-01  4.17960345e-01  4.40215741e-01  4.62203884e-01
  4.83911424e-01  5.05325184e-01  5.26432163e-01  5.47219547e-01
  5.67674716e-01  5.87785252e-01  6.07538946e-01  6.26923806e-01
  6.45928062e-01  6.64540179e-01  6.82748855e-01  7.00543038e-01
  7.17911923e-01  7.34844967e-01  7.51331890e-01  7.67362681e-01
  7.82927610e-01  7.98017227e-01  8.12622371e-01  8.26734175e-01
  8.40344072e-01  8.53443799e-01  8.66025404e-01  8.78081248e-01
  8.89604013e-01  9.00586702e-01  9.11022649e-01  9.20905518e-01
  9.30229309e-01  9.38988361e-01  9.47177357e-01  9.54791325e-01
  9.61825643e-01  9.68276041e-01  9.74138602e-01  9.79409768e-01
  9.84086337e-01  9.88165472e-01  9.91644696e-01  9.94521895e-01
  9.96795325e-01  9.98463604e-01  9.99525720e-01  9.99981027e-01
  9.99829250e-01  9.99070481e-01  9.97705180e-01  9.95734176e-01
  9.93158666e-01  9.89980213e-01  9.86200747e-01  9.81822563e-01
  9.76848318e-01  9.71281032e-01  9.65124085e-01  9.58381215e-01
  9.51056516e-01  9.43154434e-01  9.34679767e-01  9.25637660e-01
  9.16033601e-01  9.05873422e-01  8.95163291e-01  8.83909710e-01
  8.72119511e-01  8.59799851e-01  8.46958211e-01  8.33602385e-01
  8.19740483e-01  8.05380919e-01  7.90532412e-01  7.75203976e-01
  7.59404917e-01  7.43144825e-01  7.26433574e-01  7.09281308e-01
  6.91698439e-01  6.73695644e-01  6.55283850e-01  6.36474236e-01
  6.17278221e-01  5.97707459e-01  5.77773831e-01  5.57489439e-01
  5.36866598e-01  5.15917826e-01  4.94655843e-01  4.73093557e-01
  4.51244057e-01  4.29120609e-01  4.06736643e-01  3.84105749e-01
  3.61241666e-01  3.38158275e-01  3.14869589e-01  2.91389747e-01
  2.67733003e-01  2.43913720e-01  2.19946358e-01  1.95845467e-01
  1.71625679e-01  1.47301698e-01  1.22888291e-01  9.84002783e-02
  7.38525275e-02  4.92599411e-02  2.46374492e-02  1.22464680e-16]**
```

目前，我们在 NumPy 方法和函数前面加上“ **numpy** 或“ **np** ”。您还可以专门从 NumPy 库中导入特定的函数。

```
from numpy import pi, cos, sinprint (pi)
**3.141592653589793**print (sin(1))
**0.8414709848078965**print (cos(1))
**0.5403023058681398**
```

NumPy 包含的另一个方便的功能是，“**符号**”。

```
from numpy import signprint (sign(10))
**1**print (sign(0))
**0**print (sign(-10))
**-1**
```

如果数字为正，则返回 1；如果数字为 0，则返回 0；如果数字为负，则返回-1。

NumPy 数组数据结构也非常强大。让我们举一个例子，有一个一年中几个月的温度列表和我们想要对这些温度进行调整的列表(即每个温度一半)。如果没有 NumPy，您将需要遍历列表并对每个元素执行调整。使用 NumPy 数组，您可以在整个数组上一次性完成此计算。

```
import numpy as nptemperatures = [4, 3, 7, 9, 11, 14, 16, 17, 12, 11, 9, 9]
adjustments = [0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5, 0.5]print (type(temperatures))
**<class 'list'>**print (type(adjustments))
**<class 'list'>**array_temperatures = np.array(temperatures)
array_adjustments = np.array(adjustments)print (type(array_temperatures))
**<class 'numpy.ndarray'>**print (type(array_adjustments))
**<class 'numpy.ndarray'>**print (array_temperatures * array_adjustments)
**[2\.  1.5 3.5 4.5 5.5 7\.  8\.  8.5 6\.  5.5 4.5 4.5]***# average*
print (np.mean(temperatures))
**10.166666666666666***# middle*
print (np.median(temperatures))
**10.0**print (np.min(temperatures))
**3**print (np.max(temperatures))
**17**
```

**如果列表中的所有数据都相同，你也可以创建多维数组**。

```
import numpy as nptemperature_adjustments = [[4, 0.5], [3, 0.5], [7, 0.5], [9, 0.5], [11, 0.5], [14, 0.5], [16, 0.5], [17, 0.5], [12, 0.5], [11, 0.5], [9, 0.5], [9, 0.5]]*# list of lists*
print (temperature_adjustments)
**[[4, 0.5], [3, 0.5], [7, 0.5], [9, 0.5], [11, 0.5], [14, 0.5], [16, 0.5], [17, 0.5], [12, 0.5], [11, 0.5], [9, 0.5], [9, 0.5]]**temperature_adjustments_array = np.array(temperature_adjustments)*# .shape attribute shows the dimensions of the array*
print (temperature_adjustments_array.shape)
**(12, 2)***# 2D multi-dimensional array*
print (temperature_adjustments_array)
**[ 4\.   0.5]
 [ 3\.   0.5]
 [ 7\.   0.5]
 [ 9\.   0.5]
 [11\.   0.5]
 [14\.   0.5]
 [16\.   0.5]
 [17\.   0.5]
 [12\.   0.5]
 [11\.   0.5]
 [ 9\.   0.5]
 [ 9\.   0.5]]***# prints row 1*
print (temperature_adjustments_array[0])
**[4\.  0.5]***# prints row 3, column 1*
print (temperature_adjustments_array[2,0])
**7.0***# prints column 1*
print (temperature_adjustments_array[:,0:1])
**[[ 4.]
 [ 3.]
 [ 7.]
 [ 9.]
 [11.]
 [14.]
 [16.]
 [17.]
 [12.]
 [11.]
 [ 9.]
 [ 9.]]***# prints column 2*
print (temperature_adjustments_array[:,1:2])
**[[0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]
 [0.5]]***# prints row 4 to row 9 and column 1*
print (temperature_adjustments_array[3:9,0:1])
**[[ 9.]
 [11.]
 [14.]
 [16.]
 [17.]
 [12.]]***# iterate over all columns on all rows*
for temperature_adjustments in np.nditer(temperature_adjustments_array):
    print (temperature_adjustments)
**4.0
0.5
3.0
0.5
7.0
0.5
9.0
0.5
11.0
0.5
14.0
0.5
16.0
0.5
17.0
0.5
12.0
0.5
11.0
0.5
9.0
0.5
9.0
0.5***# iterate over all columns on row 4*
for temperature_adjustments in np.nditer(temperature_adjustments_array[3]):
    print (temperature_adjustments)
**9.0
0.5**
```

NumPy 文档可以在这里找到。

您还可以进行一些高级统计计算，如下所述。

## 使用熊猫

DataFrames 是熊猫提供的一个强大的数据结构。它类似于 NumPy 数组，但允许您包含不同类型的数据。

```
import pandas as pddata_dict = {
    'temperatures': [4, 3, 7, 9, 11, 14, 16, 17, 12, 11, 9, 9],
    'months': ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec']
}print (data_dict)
**{'temperatures': [4, 3, 7, 9, 11, 14, 16, 17, 12, 11, 9, 9], 'months': ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']}**data_df = pd.DataFrame(data_dict)print(data_df)
 **temperatures months
0              4    Jan
1              3    Feb
2              7    Mar
3              9    Apr
4             11    May
5             14    Jun
6             16    Jul
7             17    Aug
8             12    Sep
9             11    Oct
10             9    Nov
11             9    Dec**
```

多好啊。我们的列表字典看起来更像一个电子表格，以一种易于阅读的格式显示。

让我们来看看列表是如何工作的。

```
import pandas as pdtemperature_adjustments = [[4, 0.5], [3, 0.5], [7, 0.5], [9, 0.5], [11, 0.5], [14, 0.5], [16, 0.5], [17, 0.5], [12, 0.5], [11, 0.5], [9, 0.5], [9, 0.5]]temperature_adjustments_df = pd.DataFrame(temperature_adjustments)print (temperature_adjustments_df)
 **0    1
0    4  0.5
1    3  0.5
2    7  0.5
3    9  0.5
4   11  0.5
5   14  0.5
6   16  0.5
7   17  0.5
8   12  0.5
9   11  0.5
10   9  0.5
11   9  0.5**
```

这很好，但是第一列是温度，第二列是调节，现在还不明显。您可以像这样包含列名。

```
import pandas as pdtemperature_adjustments = [[4, 0.5], [3, 0.5], [7, 0.5], [9, 0.5], [11, 0.5], [14, 0.5], [16, 0.5], [17, 0.5], [12, 0.5], [11, 0.5], [9, 0.5], [9, 0.5]]temperature_adjustments_df = pd.DataFrame(temperature_adjustments, columns = ['temperatures', 'adjustments'])print (temperature_adjustments_df)
 **temperatures  adjustments
0              4          0.5
1              3          0.5
2              7          0.5
3              9          0.5
4             11          0.5
5             14          0.5
6             16          0.5
7             17          0.5
8             12          0.5
9             11          0.5
10             9          0.5
11             9          0.5**print (temperature_adjustments_df.head())
 **months  temperatures
0    Jan             4
1    Feb             3
2    Mar             7
3    Apr             9
4    May            11**
```

您还可以将数据从 CSV 导入数据帧。

我用这些数据创建了一个“ **pandas.csv** ”文件。

```
months,temperatures
Jan,4
Feb,3
Mar,7
Apr,9
May,11
Jun,14
Jul,16
Aug,17
Sep,12
Oct,11
Nov,9
Dec,9
```

然后像这样导入它。

```
import pandas as pddf = pd.read_csv('./pandas.csv')
# optionally: df = pd.read_csv('./pandas.csv', **encoding='utf-8'**)print (df)
 **months  temperatures
0     Jan             4
1     Feb             3
2     Mar             7
3     Apr             9
4     May            11
5     Jun            14
6     Jul            16
7     Aug            17
8     Sep            12
9     Oct            11
10    Nov             9
11    Dec             9**print (df.columns)
**Index(['months', 'temperatures'], dtype='object')**
```

您可以像这样选择数据。

```
import pandas as pddf = pd.read_csv('./pandas.csv')print (df)
 **months  temperatures
0     Jan             4
1     Feb             3
2     Mar             7
3     Apr             9
4     May            11
5     Jun            14
6     Jul            16
7     Aug            17
8     Sep            12
9     Oct            11
10    Nov             9
11    Dec             9***# select two columns by name*
print (df[['months', 'temperatures']])
 **months  temperatures
0     Jan             4
1     Feb             3
2     Mar             7
3     Apr             9
4     May            11
5     Jun            14
6     Jul            16
7     Aug            17
8     Sep            12
9     Oct            11
10    Nov             9
11    Dec             9***# select rows 5 to 9 by index*
print (df[4:8])
 **months  temperatures
4    May            11
5    Jun            14
6    Jul            16
7    Aug            17***# select all rows and two columns by name*
print (df.loc[:,'months':'temperatures'])
 **months  temperatures
0     Jan             4
1     Feb             3
2     Mar             7
3     Apr             9
4     May            11
5     Jun            14
6     Jul            16
7     Aug            17
8     Sep            12
9     Oct            11
10    Nov             9
11    Dec             9***# select all rows and column 1*
print (df.iloc[:,0])
**0     Jan
1     Feb
2     Mar
3     Apr
4     May
5     Jun
6     Jul
7     Aug
8     Sep
9     Oct
10    Nov
11    Dec
Name: months, dtype: object**
```

你可以像这样做一些非常奇特的过滤。

```
import pandas as pddf = pd.read_csv('./pandas.csv')tempsabove9 = df['temperatures'] > 9print (type(tempsabove9))
**<class 'pandas.core.series.Series'>**print (tempsabove9)
**0     False
1     False
2     False
3     False
4      True
5      True
6      True
7      True
8      True
9      True
10    False
11    False**print (df[tempsabove9])
 **months  temperatures
4    May            11
5    Jun            14
6    Jul            16
7    Aug            17
8    Sep            12
9    Oct            11**
```

使用 DataFrames 时的两个常见问题是空值或不正确的列类型。

您可以使用。info()方法。

```
print (data.info())
**<class 'pandas.core.frame.DataFrame'>
RangeIndex: 300 entries, 299 to 0
Data columns (total 7 columns):
 #   Column    Non-Null Count  Dtype  
---  ------    --------------  -----  
 0   iso8601   300 non-null    int64  
 1   datetime  300 non-null    object 
 2   low       300 non-null    float64
 3   high      300 non-null    float64
 4   open      300 non-null    float64
 5   close     300 non-null    float64
 6   volume    300 non-null    float64
dtypes: float64(5), int64(1), object(1)
memory usage: 16.5+ KB
None**
```

你会看到我的“**日期时间**字段是一个错误的对象。我可以像这样将列改写为“**日期时间**”。

```
data['datetime']= pd.to_datetime(data['datetime'])
```

而现在…

```
print (data.info())
**<class 'pandas.core.frame.DataFrame'>
RangeIndex: 300 entries, 299 to 0
Data columns (total 7 columns):
 #   Column    Non-Null Count  Dtype  
---  ------    --------------  -----  
 0   iso8601   300 non-null    int64  
 1   datetime  300 non-null    datetime64[ns] 
 2   low       300 non-null    float64
 3   high      300 non-null    float64
 4   open      300 non-null    float64
 5   close     300 non-null    float64
 6   volume    300 non-null    float64
dtypes: float64(5), int64(1), object(1)
memory usage: 16.5+ KB
None**
```

计算熊猫的累积移动平均线(CMA)、指数移动平均线(EMA)、简单移动平均线(SMA)也很琐碎。

如果我们采用一个名为“ **df** 的市场交易数据框架和一个名为“ **close** ”的收盘价列，您可以计算并添加移动平均线，如下所示。

## 累积移动平均线

```
df['cma'] = df.close.expanding().mean()
```

## 指数移动平均线

```
df['ema12'] = df.close.ewm(span=12, adjust=False).mean()
df['ema26'] = df.close.ewm(span=26, adjust=False).mean()
```

## 简单移动平均线

```
df['sma20'] = df.close.rolling(20, min_periods=1).mean()
df['sma50'] = df.close.rolling(50, min_periods=1).mean()
df['sma200'] = df.close.rolling(200, min_periods=1).mean()
```

我希望你觉得这篇文章有趣并且有用。如果您想随时了解情况，请不要忘记关注我并注册我的[电子邮件通知](https://whittle.medium.com/subscribe)。

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***我们上 LinkedIn***](https://www.linkedin.com/in/miwhittle/) 连线吧
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***