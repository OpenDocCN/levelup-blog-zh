# 熊猫和海牛的数据可视化🎓

> 原文：<https://levelup.gitconnected.com/data-visualization-with-pandas-and-seaborn-5de444b567a0>

## 学习本帖中的方法，轻松画出统计图。

![](img/38316d554d3052eefd9d6ccbd0db2399.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 拍摄的照片

数据可视化是数据分析的重要步骤之一。Matplotlib 库和 Pandas 中的 plot 方法不足以绘制高级图形。为了更好和更容易地可视化数据，您可以使用 Seaborn 库，它在 Matplotlib 库上工作，并与 Pandas 兼容。许多统计图表很容易用 Seaborn 绘制。

在这篇文章中，我将向你展示如何用 Pandas 和 Seaborn 进行数据可视化。我将讨论以下主题:

*   线形图
*   条形图
*   直方图和密度图
*   散点图
*   如何绘制分类变量？

让我们开始吧！

[](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) [## 蒂伦达兹学院

### 嗨，欢迎来到提伦达兹学院。Tirendaz 学院是一个在线教育平台，制作视频和写博客…

www.youtube.com](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) 

# 线形图

在讲线条图之前，我们先导入 Matplotlib，Pandas，Seaborn，和 Numpy 库。

![](img/755865a1e745f45c448fe432308e4218.png)

为了内联查看图形，我将编写% matplotlib notebook magic 命令，并使用 sns.set()命令来设置图形样式。

![](img/e5aad123376e15232d7e2333ee84a6c5.png)

> 你可以在下面的 GitHub 页面访问我在这里使用的 Jupyter 笔记本和数据集。

为了显示线图，让我们首先导入著名的 iris 数据集。

![](img/0d0c8b0c73defe162e921b727673fb83.png)

让我们在屏幕上打印数据集的前 5 行。

![](img/5b1734c7e38d4fba6c98d6ffc6334981.png)

让我们在虹膜数据集中画一个花瓣长度的线图。

![](img/c96c4737e9fa3625d27835e91256504a.png)![](img/393fc4ecf938f7e15dc76d9a5e940b80.png)

现在让我们画出所有列的线图。

![](img/527dac7d3c6b0373cdece2ac13b6eb64.png)![](img/055ef7257d5de59ebd3878abba24ca08.png)

如您所见，iris 数据集的数字列是使用 plot 方法绘制的，并且会自动添加一个图例。

# 条形图

条形图是可视化数据时最常用的图表之一。该图表通常在 y 轴上包含数值，在 x 轴上包含分类值。

bar 方法用于垂直显示条形，barh 函数用于水平显示条形。为了说明这一点，让我们首先创建一个系列数据。

![](img/72fe35a00ae2bb1220c2a3b30193f886.png)

我们来看看数据。

![](img/b4e887e59ffa8ed890a1af9ce8cd42e5.png)

现在我们来画这个数据的柱状图。

![](img/840ad70b2cbe3cc1b583b4663f9799f9.png)![](img/74882afda1d6947acdc1888075e47fc9.png)

这里的 color 参数用于确定列的颜色和可见性的 alpha 参数。您还可以横向查看数据列。让我们将列的颜色设为红色，并降低一点可见度。

![](img/1d29efe54c6eb91d6599a058a73326f2.png)![](img/f68f6d711e1494ea361dafc1e86005d0.png)

现在让我们展示数据框的条形图。为了展示这一点，让我们创建一个数据集。

![](img/e4e0f6b91b8db84c5c341208bf023210.png)

让我们看一下 df 数据集。

![](img/ab163e7d3fa4c69eb046b52ed12f4da1.png)

现在让我们画出这个数据集的柱状图。

![](img/de3c066d950b7b13cd08716827cc0c52.png)![](img/da4628ab180d8e6f235e9947c496834f.png)

您可以使用 stacked = True 参数创建堆积条形图。

![](img/0da9aa4209167bd0c584c39124b70974.png)![](img/75a1676db29933c3055f0c7b7f6195ff.png)

请注意，在绘制条形图时，有时条形显示数据集中的值，有时显示数据集中的统计数据。现在，让我们使用数据集中变量的一些计算来绘制条形图。为了展示这一点，让我们导入 tips 数据集。您可以使用 Seaborn 库中的 load_datasets()方法加载该数据集。

![](img/6c71a5642cf9b9daf9c67270cab71e82.png)

让我们看看数据集的前五行。

![](img/74dfdaa3afae6dc8d0e12b300e66520b.png)

让我们用交叉表方法处理数据集中的 size 和 days 列。

![](img/9fbae44119b058db752ac4a5c724dd9d.png)

让我们来看看 day_size 变量。

![](img/80af36365721b1210998fda27a7eef9d.png)

让我们画出这个变量的柱状图。

![](img/98a5a849ad138b552857d9cc6fffd23b.png)![](img/654a591bd37abaffc6d7f451e689507c.png)

你可以很容易地画出统计计算的图表。为了说明这一点，让我们计算每日小费的百分比，并将这些值添加到数据集中。

![](img/8c02c62941dd248d2c306aa2ec44cc85.png)

让我们来看看这个新的数据集。

![](img/acae4f1d894bf5f6e16c7f8620ee0be1.png)

您可以使用柱状图方法查看带误差线的每日小费百分比。

![](img/432161bc60ebb4afe80af4d3cb385572.png)![](img/8ce5dd5aa9c91d6f546748ed0d087bb9.png)

从图中可以看出，大部分小费是在周日和周五支付的。条形图方法具有色调参数。您可以使用此参数来允许分类值。

![](img/8ef177d29fc867548ece67506b105ec0.png)![](img/4547937fd1c4e70bce27f95d4f26ccf0.png)

# 直方图和密度图

直方图是一种条形图，指示数据集中值的出现频率。我们来看看数据百分比出现的频率。

![](img/0e1e0b29d9ddffa19f09826ec3077e02.png)![](img/5f51fa0e21ff0b5ace363a3688511ead.png)

请注意，这里的每个条形代表观察值的数量。
如果你想绘制带有密度曲线的直方图，你可以使用带有 *kde=True* 参数的 histplot 方法。

![](img/ddb42d2dba0ab2b80640d8c36775acfe.png)![](img/e9d9905a7d559f1873258e126a2c3683.png)

# 散点图或点状图

散点图用于查看两个变量之间的关系。例如，让我们看看虹膜数据集中萼片长度和花瓣长度之间的关系。为此，可以在 Seaborn 中使用 regplot 方法。

![](img/e5ccceae8f6858d65220d7aa0739de02.png)

您可以使用 pairplot()方法查看数据集中所有数值变量的二进制关系。

![](img/d133a4b257e0babe3d0ccf4cef5bead4.png)![](img/d2321cdbff6c8d547e804107c3e0d131.png)

您可以看到带有色调参数的类别。

![](img/76765926f55d2bab52b50a4d6f7da47b.png)![](img/de3effaf4577713c9ad75d1605e928aa.png)

# 分面网格和分类数据

catplot 方法允许您绘制多个分类变量的图。例如，让我们处理 tips 数据集，并再次查看这个数据集。

![](img/2a4e3c8b71141d81afb13d01d06339a6.png)

如您所见，吸烟者、日期和时间变量是分类变量。现在让我们看看使用这些分类变量的小费百分比图。

![](img/171b1eda386679f18b34288f46a36e98.png)![](img/c482982af7580d0679086587c3f57c23.png)

我们按时间来分别看吸烟者和不吸烟者的情节。

![](img/bee8c9423ec946bfb2e257d0ad041379.png)![](img/5220a668eaf3fc20dfca8cd06dade0cb.png)

请注意，晚上吸烟者的小费比例高于其他人，但这些人的置信区间相当宽，所以他们给的小费或高或低。

您可以在 catplot()方法中更改绘图类型。例如，您可以使用 kind = 'box '参数来查看箱线图。

![](img/0a2e9ebe72196ee7f1a40337b2ea816c.png)![](img/1f6ff56efbf84a9fdb555574fffdad13.png)

箱线图也显示了异常值。您可以将框外的值视为异常值。

# 结论

数据可视化是数据分析最重要的状态之一。在这篇文章中，我谈到了如何用 Seaborn 和 Pandas 进行数据可视化。就是这样。我希望你喜欢它。感谢您的阅读。你可以在这里找到这个笔记本[。](https://github.com/TirendazAcademy/DATA-VISUALIZATION-WITH-PYTHON/blob/main/04-Data%20Visualization%20with%20Pandas%20and%20Seaborn.ipynb)

别忘了在[YouTube](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw)|[GitHub](https://github.com/tirendazacademy)|[Twitter](https://twitter.com/TirendazAcademy)|[ka ggle](https://www.kaggle.com/tirendazacademy)|[LinkedIn](https://www.linkedin.com/in/tirendaz-academy)上关注我们

[](/top-10-python-libraries-and-5-best-books-for-data-science-fa0d0cf171a6) [## 数据科学的 10 个最佳 Python 库

### 数据科学家应该知道的图书馆和学习它们的前 5 本书。

levelup.gitconnected.com](/top-10-python-libraries-and-5-best-books-for-data-science-fa0d0cf171a6) [](https://medium.com/geekculture/8-best-seaborn-visualizations-20143a4b3b2f) [## 8 个最好的 Seaborn 可视化

### 使用企鹅数据集与 Seaborn 一起动手绘制统计图。

medium.com](https://medium.com/geekculture/8-best-seaborn-visualizations-20143a4b3b2f) 

*如果这篇文章有帮助，请点击拍手👏按钮几下，以示支持👇*