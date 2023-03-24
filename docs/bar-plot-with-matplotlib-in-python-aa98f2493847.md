# 使用 Python 中的 Matplotlib 动手绘制条形图

> 原文：<https://levelup.gitconnected.com/bar-plot-with-matplotlib-in-python-aa98f2493847>

## 如何绘制有效条形图的实用指南。

![](img/e5994c6b30e48485fc49674c03e1c674.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

假设您有五种不同工作的平均工资数据，并且您想要比较这些工资。您可以使用条形图来可视化这些数据。条形图用于比较不同类别中的值。条形图可以水平或垂直绘制。在这篇文章中，我将讨论以下主题。

*   如何绘制条形图？
*   如何画误差线？
*   如何用误差线画柱状图？

让我们开始吧！

[](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) [## 蒂伦达兹学院

### 嗨，欢迎来到提伦达兹学院。Tirendaz 学院是一个在线教育平台，制作视频和写博客…

www.youtube.com](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) 

# 条形图

首先，让我们导入必要的库。

![](img/65a0e30f99c88e282444749b9018f9d3.png)

你可以在这里找到笔记本和数据集[。接下来，我要用`%matplotlib inline`魔法命令来看看字里行间的情节。](https://github.com/TirendazAcademy/DATA-VISUALIZATION-WITH-PYTHON/blob/main/07-Bar%20Plot%20with%20Matplotlib%20in%20Python.ipynb)

![](img/06b7fbb6f3ef1dd1ba7044ba4e4109f9.png)

我将选择 seaborn-whitegrid 样式作为图形样式。

![](img/85debcb96c4ac5255490e1235086fbbb.png)

让我们首先创建一个数据集，显示数学、统计和计算机类的七个类的平均分数，以显示条形图。

![](img/196c62c0e650f0e6fedfa0de7a10b3b6.png)

接下来，让我们使用列表创建`classes`变量。

![](img/aff102f702cc4cdc2a35dc0a82c81388.png)

让我们为这些类画一个柱状图。这里，`legend`方法显示了图中的标签名称。

![](img/b02930b3c2201fe672b0c51a2fcaa43e.png)![](img/b584289c46b603dbf8c61e6391af687d.png)

线形图

哎呀！运行这些命令时，您会遇到一个线图。如果 x 轴有分类变量，最好绘制条形图。让我们用`bar`方法代替`plot`方法来画一个柱状图。

![](img/47605f3e356530dfb2dcb9e37d450760.png)![](img/2f100073a60c802d15f9538fda51c61b.png)

堆积条形图

如你所见，堆叠的条形被画了出来。要查看数据的单个条形，让我们首先对数据进行索引。我将为此创建一个`x_index`变量。

![](img/7f0c530c0d23d441f9797cc3720e8e21.png)

接下来，让我们将这个变量添加到`bar`方法中，并使用一个`x`变量来防止条形重叠。

![](img/941c12bae146e510e52ac23d6482b4a4.png)![](img/688705a47b73829e1e910aeeb43f7819.png)

条形图(非堆积图)

请注意，条形是重叠的。让我们使用`width`参数来更好地查看条形。

![](img/d35ec95a65487d49b8a3ca3288485d0c.png)![](img/8afa27cc328e793e6e49a58cd0716c9e.png)

条形图(不重叠)

如果你注意 x 轴，有一些数字，如果你想用`classes`变量的值替换这些数字，你可以使用`xtricks`方法。

![](img/6671b749057e0844d324f22573978136.png)![](img/2b963368ffd354089b51a8202a888362.png)

带有类值的条形图

# 误差线

在科学研究中，通过考虑误差来进行测量。误差线是数据可变性的图形表示，用在图形上表示报告测量中的误差或不确定性。所以你可能想用误差线。

让我们为 x 轴和 y 轴创建值。首先，我要在 x 轴上取 30 个 0 到 30 之间的数字。

![](img/8b78ef2111973d3b620c1c4f4f12c40d.png)

接下来我要为 y 轴生成 30 个 0 到 5 之间的整数。

![](img/01af6fbb59315ff208eb03897ecb95f1.png)

让我们画出 x 轴和 y 轴的散点图。

![](img/fed80319d9f147aa35f6696b1f1dc632.png)![](img/ae14de700b8f4a0dbe36e4129ba333a8.png)

散点图

让我们给这些点加上误差线。

![](img/ba5b71d91562a541c1950ed3b0819b08.png)![](img/162fcf8a165007d853afcd765c0e3543.png)

误差线

您可以使用`fmt`参数来调整颜色和外观。要查看水平误差线，您可以利用`xerr`参数。

![](img/0fb0d149bcdf553f84fbccdc71afb8ba.png)![](img/38b0f003e9cdd09363a50edfe3049416.png)

水平误差线

您可以向`errorbar`方法添加其他属性。

![](img/ed09412b55bb2ad0a5cc765010040035.png)![](img/e1f3d267f0cbc789ffb14e6a08180f97.png)

彩色误差线

# 带误差线的条形图

让我们画一个图来一起看条形图和误差线。为了证明这一点，我将使用虹膜数据集。让我们导入`load_iris`方法来读取这个数据集。

![](img/c35bcc985193a091f6cb3d46a9216d28.png)

在鸢尾数据集中，有显示萼片长度和宽度、花瓣高度和宽度以及三种类型的鸢尾花的变量。让我们来看看这个数据集。

![](img/58d329292f3c679818bcb86d920af6d1.png)

接下来，让我们计算数据集中数值变量的均值和标准差。

![](img/1cbc352539df248d06191dcf7ce29da4.png)

让我们取一个区间变量，并将 iris 数据集中的 4 个数值变量赋给这个区间变量。

![](img/cd2f4a34fcd789976dbfde2c3dd28f50.png)

之后我要画两个图，一个是横条，一个是竖条。让我们在第一个图形区域绘制误差线和水平条形图。`bars`方法用于绘制水平条形图。

![](img/3c7a95ab1e3394f006a51df8749cb220.png)![](img/d9525b61b9392b06ccf5ceace0418c1e.png)

带误差线的水平条

给剧情加个标题吧。

![](img/c2db9bbb2a2894e36ad7dd2231a39e3f.png)![](img/71b87834e602520408573d831d6d237c.png)

带误差线和标题的水平条

您也可以使用`ytricks`方法来标记 y 轴上的条形。

![](img/68121b264cfbf34d4a95945825c64bff.png)![](img/6e160e5c3054090fad8d127c635ac4db.png)

标签条形图

接下来，让我们画出竖线。

![](img/97b77f0f0173fc4571b4c8a45be5a80f.png)![](img/e2ba27f9809cab91f434b74a7fc2cf56.png)

带误差线的竖条图

让我们用`xtricks`方法给出 x 轴的标签。我将使用`rotation`参数来设置文本的旋转。

![](img/a457541661315cd91ecbf6335f6d440e.png)![](img/96c0d6bf5a4fa8bb19c53da5a0d63293.png)

带标签的竖条

此外，您可以使用`width`参数调整条形的宽度。

![](img/b2b5ab4227d102c142725572956a51b9.png)![](img/9d3830e824e558097dbefd82c7c183d0.png)

调整了条宽的垂直条

# 结论

条形图用于比较不同类别中的值。条形图可以水平或垂直绘制。在这篇文章中，我谈到了酒吧的情节。就是这样。我希望你喜欢它。感谢您的阅读。你可以在这里找到这个笔记本[。](https://github.com/TirendazAcademy/DATA-VISUALIZATION-WITH-PYTHON/blob/main/07-Bar%20Plot%20with%20Matplotlib%20in%20Python.ipynb)

别忘了在 YouTube 上关注我们

![Tirendaz AI](img/384c46a0053e6856ca4df470ae6476d6.png)

[提伦达兹艾](https://tirendazacademy.medium.com/?source=post_page-----aa98f2493847--------------------------------)

## 用 Python 实现数据可视化

[View list](https://tirendazacademy.medium.com/list/data-visualization-with-python-72919ad57b84?source=post_page-----aa98f2493847--------------------------------)11 stories![](img/c488dead590dd5010c1227364ba2701f.png)![](img/76b23c08a8d0b95169094b46bd2f6251.png)![](img/f0183d951fb93667dc3324d16a64366a.png)

*如果这篇文章有帮助，请点击拍手👏按钮几下，以示支持👇*