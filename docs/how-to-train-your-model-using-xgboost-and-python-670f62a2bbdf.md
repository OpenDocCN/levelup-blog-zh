# 如何使用 XGBoost 和 Python 训练您的模型

> 原文：<https://levelup.gitconnected.com/how-to-train-your-model-using-xgboost-and-python-670f62a2bbdf>

![](img/f073c2093bebbd80fa8e96ac58e33a6b.png)

克里斯里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**XGBoost(极限梯度提升)**是梯度提升算法的高级实现。它与其他增强算法的区别在于它的速度和准确性。

为了完全掌握 XGBoost，您将需要大量的时间和不同的项目，但我们希望这是一个坚实的起点。

# I **安装 XGBoost**

要在 Python 中安装 XGBoost 库，只需在命令提示符下键入 **pip install xgboost** 。

在本教程中，我们将使用其他库，因此它们也是一样的。如果尚未安装，请在命令提示符下键入**pip install[library name]**。

# **读取数据集**

首先，我们需要从文件中读取数据，并为训练我们的模型做准备。为此，我们将使用 **pandas** 库及其 **read_csv** 方法。

```
import numpy as np
import matplotlib.pyplot as plt
import pandas as pddataset = pd.read_csv('train.csv')
```

一旦我们将数据集存储到 DataFrame 变量中，我们需要指定哪些列将用作输入参数(X ),哪些将用作输出(y)

```
X = dataset.iloc[:, 0:9].values
y = dataset.iloc[:, 9].values
```

这意味着我们的数据集有 10 列，索引为 0-8 的列将作为输入，而最后一列是输出。

# 一个热编码

XGBoost 算法适用于数值类型，如果数据集只包含数值，可以跳过这一步。当然，并不是所有的数据集都只包含数字，但幸运的是有一个解决方法。

基本上，一种热门的编码是将分类值表示为二进制向量。这需要将分类值映射到整数值。

然后，每个整数值都被表示为一个二进制向量，除了用 1 标记的整数索引之外，其他都是零值。

```
from sklearn.preprocessing import LabelEncoder, OneHotEncoderlabelencoder_X_1 = LabelEncoder()
X[:, 5] = labelencoder_X_1.fit_transform(X[:, 5])labelencoder_X_2 = LabelEncoder()
X[:, 7] = labelencoder_X_2.fit_transform(X[:, 7])onehotencoder = OneHotEncoder(categorical_features = [1])
X = onehotencoder.fit_transform(X).toarray()
```

在本例中，数据集中包含分类数据的列的索引是 5 和 7。对于您希望编码的每一列，您必须使用一个 **LabelEncoder** 及其***fit _ transform***方法。您将希望转换为整数的列作为参数传递，方法会完成这项工作。只需将返回值赋给与你传递的完全相同的列，用整数覆盖字符串。

一旦这一步完成，我们需要做的就是实例化 **OneHotEncoder** 并对整个输入数据调用它的 **fit_transform** 方法。

# 分割数据集

现在是时候训练和测试我们的模型了。如果您已经有单独的数据集用于训练和测试，那就更好了。您将加载测试数据，类似于我们上面提到的训练数据。如果你只有一个数据集，不用担心，有一种方法可以把这个数据拆分成训练样本和测试样本。

```
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
```

这意味着我们将使用 20%的数据集进行测试，其余的用于训练。这个参数 **random_state = 0** 意味着将随机选择哪个数据进入哪个样本。如果希望对该过程有更多的控制，请指定一个非零值。

# 培训和测试

首先，我们让 XGBoost 适合训练数据。注意，我们使用的是不带任何参数的 **XGBClassifier** ，这意味着我们使用的是默认参数。如果你想获得更高的精度，你将不得不调整参数，但这是因数据集而异的事情，这里不讨论。

```
import xgboostclassifier = xgboost.XGBClassifier()
classifier.fit(X_train, y_train)
```

现在我们可以进行测试了。

```
y_pred = classifier.predict(X_test)
```

现在我们可以评估准确性。

```
from sklearn.metrics import accuracy_scoreaccuracy = accuracy_score(y_test, y_pred, normalize=False)
print(accuracy)
```

这意味着我们正在比较存储在 **y_test** 中的“基本事实”和存储在 **y_pred** 中的预测。在 **normalize=False** 的情况下，精确度将是一个介于 0 和 1 之间的值，理想情况下应该尽可能接近 1。

# 结论

如果你坚持到了最后，我们想感谢你阅读本教程，我们希望我们能够澄清 XGBoost 的基础知识。如果您想进一步探索这个算法，我们建议您先检查一下参数调整。