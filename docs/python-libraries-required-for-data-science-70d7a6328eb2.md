# 数据科学所需的 Python 库

> 原文：<https://levelup.gitconnected.com/python-libraries-required-for-data-science-70d7a6328eb2>

## Python 库及其在数据科学中的应用

![](img/4bd376ee82d706c1f09e8f0260315803.png)

照片由来自[佩克斯](https://www.pexels.com/photo/city-man-people-street-5811175/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[乌戈·佩雷斯·内托](https://www.pexels.com/@hugoperezneto?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

一段时间以来，Python 一直是数据科学家最喜欢的语言之一。由于其丰富的库用于整个数据驱动的决策过程，它在数据科学领域获得了巨大的欢迎。

这些库用于各种数据科学步骤，如加载数据、数据处理、数据可视化和建模。在 python 中，这些库用于自动化任务、预测结果和获得商业智能见解。

在本文中，我将介绍一些对数据科学家、数据工程师和机器学习工程师有用的 Python 库。我们开始吧！

# 1.熊猫

*Pandas* 是一个开源的 Python 包，它提供了高性能和灵活的数据结构，用于处理结构化和时间序列两种类型的数据。

这个包是一个游戏改变者，是执行数据分析和数据争论过程的完美工具。它是用于数据操作、数据清理和数据汇总的强大工具。

Pandas 在处理表格数据、时间序列数据和统计数据时表现出色。它有两种类型的主要数据结构，**系列**(一维)和**数据框架**(二维)。

**熊猫有什么用途？**

1.处理数据集中存在的缺失数据(NaN 值、缺失值)

2.更新、插入和删除数据框中的列

3.合并、连接和重塑数据框

4.灵活的分组功能，用于聚合和转换数据

从数据探索到数据科学中的可视化- Pandas 是一个必须掌握的 Python 库。

**代码示例:** 使用 pandas 加载 CSV 文件:

```
import pandas as pddf = pd.read_csv('data.csv')print(df.to_string())
```

# 2.NumPy

NumPy 是 Python 中最基本的包之一。这是一个通用的数组处理包。它在处理多维数组对象时提供了高性能。NumPy 代表*数值 PYthon。*

它在 GitHub 上有一个超过 700 名贡献者的活跃社区。

NumPy 库用于处理存储相似数据类型值的数组。它还对数组执行数学运算，并使它们的矢量化变得更加容易。

**Numpy 有哪些用途？**

1.  创建和处理 N 维数组
2.  高级阵列操作，如分割成段、堆叠阵列和广播阵列。
3.  调整数组的大小
4.  基本切片和高级索引

**代码示例:** 创建 2D 数组:

```
import numpy as nparr = np.array( [[ 1, 2, 3],
                [ 4, 2, 5]] )
print(arr)
```

# 3.Matplotlib

Matplotlib 是 Python 强大的数据可视化工具。这是一个跨平台的库，用于在数组的帮助下绘制 2D 图。这个库也用于在 Python 中创建静态和交互式可视化。

Matplotlib 是数据科学项目中使用的一个有用的绘图库。它生成数据可视化，如二维图表，图表和图形。

它还提供了一个面向对象的 API，用于将绘图嵌入到应用程序中。

**Matplotlib 有哪些用途？**

1.  可视化数据的分布以获得洞察力
2.  绘制图表，如直方图，散点图，折线图等。
3.  可视化 2D 平面上的数据点

Matplotlib 还提供了可以在图形上绘制的标签、图例和网格等功能。

**代码示例:**

```
frommatplotlib import pyplot as pltx = [2, 7, 9, 5, 7]y =[8, 5, 11, 4, 2]plt.plot(x,y)plt.show()
```

# 4.SciPy

这个 *SciPy* (科学 Python)库是一个开源 Python 库，广泛用于数学领域的高级计算。这个科学图书馆包括线性代数，积分，统计和优化模块。

SciPy 库的大量文档使使用它变得非常容易。它在 Github 上有一个超过 600 名贡献者的活跃社区。

**SciPy 有哪些用途？**

1.  线性代数和数学运算
2.  算法优化
3.  求解不同的微分方程
4.  图像处理

SciPy 库构建在 NumPy 库之上。

**代码示例:** 指数和三角函数:

```
from scipy import speciala = special.exp10(3)  #exponentialb = special.sindg(90)  #trigonometricprint(a)
print(b)
```

# 5.Scikit 学习

*Scikit-learn* 是一个基于 SciPy 构建的用于机器学习的 Python 库。2007 年，大卫·古纳波启动了谷歌代码之夏项目。

数据科学家使用这个库进行机器学习任务，如回归、分类、聚类、模型选择和降维。

Scikit-learn 库的大部分是用 Python 语言编写的，它广泛使用 NumPy 来实现线性代数和数组操作的高性能。

**sci kit-Learn 有哪些用途？**

1.  回归，例如线性和逻辑回归
2.  分类，如 K-最近邻
3.  聚类，如 K-Means
4.  型号选择
5.  降维
6.  交叉验证

**代码示例:**

```
sklearn.linear_model.LinearRegression()
```

# 6.PyCaret

*PyCaret* 是 Python 中的一个开源机器学习库，它允许你自动化一些主要步骤来评估和比较机器学习算法。它是一个端到端的机器学习和模型管理工具。

这个库自动化了机器学习工作流，只用几个步骤就取代了数百行代码。作为一个低代码库，PyCaret 使您更加高效。虽然花在编码上的时间更少，但您现在可以专注于业务问题和理解。

**py caret 有哪些用途？**

1.  自动定义要执行的数据转换
2.  它自动调整模型超参数
3.  它自动评估和比较机器学习模型
4.  用几行代码执行端到端的 ML 实验

**代码示例:**

```
from pycaret import classificationdata = pd.read_csv('data.csv')classification.setup(data=data, target='Personal Loan')classification.create_model('xgboost') #xgb modelclassification.tune_model('catboost') #Hyperparameter tuning
```

你可以在这里了解更多关于[的信息。](https://pycaret.org/)

# 7.张量流

Tensorflow 由 **Google Brain** 团队开发，是最受欢迎的机器学习和深度学习 Python 框架之一。开发它的目的是进行机器学习和深度神经网络研究。

TensorFlow 是一个 Python 库，允许数据科学家在深度学习中开发深度神经网络。它在短时间内成为最受欢迎的深度学习库。已经用 C++、Python、CUDA 写好了。

TensorFlow 允许数据科学家开发深度学习模型，推动机器学习的最新发展，并快速构建和部署 ML 支持的应用。

**tensor flow 有哪些用途？**

1.  用于时间序列分析
2.  图像分类
3.  语音和图像识别
4.  视频检测、运动检测

**代码示例:**

```
import tensorflow as tfx1 = tf.constant([1,2,3,4])
x2 = tf.constant([5,6,7,8])result = tf.multiply(x1, x2)print(result)
```

# 8.克拉斯

*Keras* 是一个用于构建和训练神经网络的高级深度学习 API。它运行在机器学习框架 TensorFlow 之上，并使用 TensorFlow 和 Theano 等其他深度学习包作为其后端。

Keras 使用起来非常简单，而 TensorFlow 要复杂得多。Tensorflow 同时提供高级和低级 API，Keras 只提供简单一致的高级 API。

因此，如果你不想进入 TensorFlow 框架的复杂性，Keras 是一个很好的选择。

**Keras 有哪些用途？**

1.  计算损失函数
2.  创建图像分类模型
3.  确定模型的准确性
4.  图像处理

**代码示例:**

```
from keras import models
from keras import layersmodels.Sequential()models.add(layers.Dense(784**,** activation='relu'**,** input_shape=(28 * 28**,**)))models.compile(optimizer='adam'**,** loss='categorical_crossentropy'**,** metrics=['accuracy'])
```

# 结论

这就是这篇文章的全部内容。在本文中，我们讨论了对数据科学有用的不同 python 库。

在数据科学之旅中，每个图书馆都有自己的重要性。最好是在正确的时间使用合适的库来完成任务。

感谢阅读！

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)