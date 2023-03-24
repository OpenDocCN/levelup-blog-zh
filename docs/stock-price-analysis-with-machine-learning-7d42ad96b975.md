# 基于机器学习的股票价格分析

> 原文：<https://levelup.gitconnected.com/stock-price-analysis-with-machine-learning-7d42ad96b975>

![](img/c7da0c0780ef116b2b8b2da8b9aeb767.png)

老式收音机。来自 https://porphyry.online/的伊戈尔·沙巴林的照片

机器学习是一种强大的数据分析方法，能够构建能够从数据中学习的应用程序，它被用于许多领域，包括预测分析、学习关联、异常检测、自然语言处理(NLP)和图像分析。本文举例说明了如何用 Python 创建一个用于股票价格分析的机器学习模型。

# 准备您的工作环境

出于本文示例的目的，您需要获得股票的历史价格数据，以及股票市场指数的数据，比如 S&P500 指数。这些数据可以通过具有 Python 包装器的 API 获得。特别是，您需要安装以下库:

```
pip install yfinance
pip install pandas-datareader
```

yfinance 库是一个用于 Yahoo Finance API 的 Python 包装器，它允许您获取特定时间段内特定股票的数据。pandas-datareader 库可用于获取 S&P500 指数数据。

# 获取数据

您可能会选择获取某个知名股票的历史数据，比如 TSLA (Tesla Inc .)。在下面的代码片段中，您获得了 TSLA 去年的股票数据:

```
import yfinance as yf
tik = yf.Ticker('TSLA')
hist = tik.history(period="1y")
```

除了股票数据之外，您可能还需要考虑股票市场指数的数字，例如 S&P500，它表示 500 家大公司的平均股票表现。要获得 S&P500 指数，您可以使用 pandas datareader 包中的 get_data_stooq() datareader 方法。在下面的代码中，您将获得去年的 S&P500 数据:

```
import pandas_datareader.data as pdr
from datetime import date, timedelta
end_date = date.today()
start_date = end_date — timedelta(days=365)
index_data = pdr.get_data_stooq('^SPX', start_date, end_date)
```

现在，您已经有了同一时期的股票数据和指数数据，您可以将它们合并到一个数据框架中:

```
df = hist.join(index_data, rsuffix = '_ix')
```

由于并非连接数据框架中的所有列都需要生成用作模型特征的度量，因此您可以只选择需要的列:

```
df = df[['Close','Volume','Close_ix','Volume_ix']]]
```

之后，df 数据帧的内容应该如下所示:

```
Close Volume Close_ix Volume_ix
Date 
2020–04–13 67.79 131022800 2761.63 2930172222
2020–04–14 71.21 194994800 2846.06 3093000000
2020–04–15 70.56 131154400 2783.36 2890772222
2020–04–16 71.12 157125200 2799.55 2877772222
2020–04–17 70.16 215250000 2874.56 3217855556
… … … … …
2021–04–05 125.90 88651200 4077.91 2241318486
2021–04–06 126.21 80171300 4073.94 2147071522
2021–04–07 127.90 83466700 4079.95 1963233249
2021–04–08 130.36 88844600 4097.17 2011933177
2021–04–09 133.00 106513800 4128.80 1878899058
```

现在，您需要处理这些连续的数据，以导出用作正在创建的 ML 模型的输入的特征。

# 从连续数据中提取 ML 模型的特征

通过移动时间序列中的数据点，您可以了解某个时间序列变量是如何随时间变化的。在所讨论的例子中，你可能想要查看指标(价格、交易量)相对于前一天的百分比变化。为此，您可以向 df 数据帧添加几个新列:

```
import numpy as np
df['priceRise'] = np.log(df['Close'] / df['Close'].shift(1))
df['volumeRise'] = np.log(df['Volume'] / df['Volume'].shift(1))
df['priceRise_ix'] = np.log(df['Close_ix'] / df['Close_ix'].shift(1))
df['volumeRise_ix'] = np.log(df['Volume_ix'] / df['Volume_ix'].shift(1))
df = df.dropna()
```

在上面的代码片段中，您创建了将被用作模型特性的指标。您可以从 df 数据帧中删除所有其他列，只留下具有新创建的指标的列:

```
df = df[['priceRise','volumeRise','priceRise_ix','volumeRise_ix']]
```

因此，df 数据帧现在应该是这样的:

```
priceRise volumeRise priceRise_ix volumeRise_ix
Date 
2020–04–14 0.049219 0.397602 0.030114 0.054080
2020–04–15 -0.009170 -0.396598 -0.022277 -0.067618
2020–04–16 0.007905 0.180668 0.005800 -0.004507
2020–04–17 -0.013590 0.314757 0.026441 0.111699
2020–04–20 -0.021029 -0.504149 -0.018043 -0.103974
… … … … …
2021–04–05 0.023304 0.167790 0.014335 0.028571
2021–04–06 0.002459 -0.100544 -0.000974 -0.042959
2021–04–07 0.013302 0.040282 0.001474 -0.089512
2021–04–08 0.019051 0.062441 0.004212 0.024503
2021–04–09 0.020049 0.181386 0.007690 -0.068410
```

# 生成目标变量值

虽然价格预测的任务是一个回归任务，但是如果您只想预测价格是上涨、下跌还是大致保持不变，您可以将其简化为分类任务。在这个特定的分类任务中，输出变量(也称为因变量或目标变量)的可能值可能分别为-1、0 和 1。在下面的代码片段中，您为目标变量添加一个新列并填充它:

```
conditions = [
 (df['priceRise'].shift(-1) > 0.01),
 (df['priceRise'].shift(-1)< -0.01)
]
choices = [1, -1]
df[‘Pred’] = np.select(conditions, choices, default=0)
```

上述算法假设如下:

*   第二天的价格涨幅超过 1%被视为上涨(1)
*   与第二天相比，价格下跌超过 1%被视为下跌(-1)
*   其余的被认为是停滞(0)

# 为机器学习准备数据

现在，目标变量在 Pred 测向列中，特征(输入变量)在数据帧的其他列中。在使用这些数据训练模型之前，需要将其转换为 NumPy 数组(分别为目标和特征)。这可以通过以下方式完成:

```
target = df['Pred'].to_numpy() 
features = df[['priceRise','volumeRise','priceRise_ix','volumeRise_ix']].to_numpy()
features = np.around(features, decimals=2)
```

# 训练和评估模型

现在，您已经准备好训练和评估模型了。在下列程式码中，您会使用逻辑回归演算法来定型模型:

```
from sklearn.model_selection import train_test_split
rows_train, rows_test, y_train, y_test = train_test_split(features, target, test_size=0.2)
from sklearn.linear_model import LogisticRegression 
clf = LogisticRegression()
clf.fit(rows_train, y_train)
```

然后，您可能想要评估模型:

```
print(clf.score(rows_test, y_test))
```

输出显示了模型的准确性:

```
0.5686274509803921
```

当然，您的结果看起来会有所不同。您可以尝试不同的股票，并从股票和指数数据中发明新的指标。

重要提示:交易中使用的真实世界模型要复杂得多。本例中讨论的模型是研究级别的，不是用于生产的。提供该模型只是为了说明如何从机器学习模型的时间序列中获取指标的概念。