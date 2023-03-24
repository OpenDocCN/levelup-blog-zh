# 用于市场指标分析的神经网络

> 原文：<https://levelup.gitconnected.com/neural-networks-for-market-indicators-analysis-dae2678270eb>

![](img/eb0b1609eb11a4a3c8c8ef4f29dc9f7c.png)

神经网络已经被成功地用于解决科学和工程中的一系列具有挑战性的问题。本文中的示例使用 Keras 构建一个执行多输出回归的神经网络，用于股票和黄金价格预测。您将配置和训练此神经网络，然后使用平均绝对误差(MAE)评估模型的准确性，这是预测准确性的最简单的度量方法。

# 使用 Google Colab

也许用 Keras 实现神经网络最简单的方法是在 [Google Colab](http://colab.research.google.com) 中运行一个笔记本，在其中你可以将你的代码组织成几个代码单元。您不需要在笔记本中安装 Keras，以及示例中使用的 NumPy。但是，您仍然需要安装 yfinance 和 quandl 库来分别获取股票和黄金价格。

```
!pip install yfinance
!pip install quandl
```

之后，您可以使用这些库来获取用于训练和评估神经网络的数据。

# 获取数据

在下面的代码单元格中，您获取了某个股票(在本例中为 Tesla)在过去五年中的历史股价(当然，您也可以选择另一个股票):

```
import yfinance as yf
tkr = yf.Ticker('TSLA')
hist = tkr.history(period="5y") 
import pandas_datareader.data as pdr
from datetime import date, timedelta
end = date.today()
start = end — timedelta(days=5*365+1)
index_data = pdr.get_data_stooq('^SPX', start, end)
df = hist.join(index_data, lsuffix = '_tkr', rsuffix = '_idx')
df = df[['Close_tkr','Volume_tkr','Close_idx','Volume_idx']]
```

正如您可能从上一个单元格的最后一行中猜到的那样，您将只使用股票的收盘价和销售量数字来进行进一步的分析。

然后，你会得到同一时期的黄金价格。在此之前，您需要创建一个 [Quandl 帐户](https://help.quandl.com/article/320-where-can-i-find-my-api-key)来获得一个免费的 API 令牌:

```
import quandl
gold_price = quandl.get("LBMA/GOLD",start_date=start, end_date=end, authtoken="your_quandl_token")
df = df.join(gold_price['USD (AM)'])
df = df.rename(columns={'USD (AM)': 'Gold'})
```

现在您已经获得了数据，您可以为要训练的网络生成要素和输出。

# 从数据中生成特征

在下一个像元中，在训练网络之前对数据进行预处理，执行要素缩放。实际上，您只需计算每个指标与前一天相比的百分比变化:

```
import numpy as np
df['priceRise_tkr'] = np.log(df['Close_tkr'] / df['Close_tkr'].shift(1))
df['volumeRise_tkr'] = np.log(df['Volume_tkr'] / df['Volume_tkr'].shift(1))
df['priceRise_idx'] = np.log(df['Close_idx'] / df['Close_idx'].shift(1))
df['volumeRise_idx'] = np.log(df['Volume_idx'] / df['Volume_idx'].shift(1))
df['priceRise_gold'] = np.log(df['Gold'] / df['Gold'].shift(1))
df = df.dropna()
```

然后，通过获取第二天价格指标的百分比变化来生成输出:

```
df['tkrPred'] = df['priceRise_tkr'].shift(-1)
df['goldPred'] = df['priceRise_gold'].shift(-1)
df['tkrPred'] = df['tkrPred']*100
df['goldPred'] = df['goldPred']*100
df = df.dropna()
```

请注意，您将 tkrPred 和 goldPred 列中的值调整为实际百分比，而不是-1.0 和 1.0 之间的实数。

现在，您需要将带有特征和目标变量的 df 数据帧转换为相应的 NumPy 数组，以用于模型训练和评估:

```
features = df[['priceRise_tkr','volumeRise_tkr','priceRise_idx','volumeRise_idx','priceRise_gold']].to_numpy()
features = np.around(features, decimals=2) 
target = df[['tkrPred','goldPred']].to_numpy()
```

# 配置模型

在此示例中，您将创建一个神经网络，它遵循一个简单的顺序模型，具有一堆密集层:

```
from keras.models import Sequential
from keras.layers import Dense
```

您可以在单独的函数中获得模型:

```
def get_model(n_inputs, n_outputs):
  model = Sequential()
  model.add(Dense(n_inputs, input_dim=n_inputs, activation='relu'))
  model.add(Dense(100, kernel_initializer='he_uniform', activation='relu'))
  model.add(Dense(n_outputs))
  model.compile(loss='mae', optimizer='adam')
  return model
```

如您所见，该模型有三层:输入、隐藏和输出。您将 mae 定义为损失函数和 adam 优化器。

# 训练和评估模型

在这里，您使用重复的 K-fold 交叉验证器来训练和评估模型，该验证器为每个 split 调用返回不同的结果:

```
from sklearn.model_selection import RepeatedKFold
n_inputs, n_outputs = features.shape[1], target.shape[1]
cv = RepeatedKFold(n_splits=10, n_repeats=3, random_state=1)
for train_ix, test_ix in cv.split(features):
  X_train, X_test = features[train_ix], features[test_ix]
  y_train, y_test = target[train_ix], target[test_ix]
  model = get_model(n_inputs, n_outputs)
  model.fit(X_train, y_train, verbose=0, epochs=20)
  mae = model.evaluate(X_test, y_test, verbose=0)
  print('>%.3f' % mae)
```

输出可能是这样的:

```
>1.540
>1.527
>1.551
>1.606
>1.481
>1.495
>1.731
>1.380
…
```