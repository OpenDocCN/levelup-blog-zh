# 使用 Python 中的 Matplotlib 实践散点图

> 原文：<https://levelup.gitconnected.com/scatter-plot-with-matplotlib-in-python-abb1a6ad042>

## 数据可视化教程

## 如何使用绘图和散点图方法绘制优秀散点图的实用指南。

![](img/f105556d4ecfd5b961e2e2b6d49f5b9d.png)

照片由 [Count Chris](https://unsplash.com/@countchris?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

散点图是一种用于显示两个变量的值的图。散点图也称为散点图、散点图或散点图。在这篇文章中，我将解释以下主题:

*   什么是散点图？
*   用绘图法绘制散点图
*   使用散点图方法的散点图
*   散点图法与图法

让我们开始吧！

[](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) [## 蒂伦达兹学院

### 嗨，欢迎来到提伦达兹学院。Tirendaz 学院是一个在线教育平台，制作视频和写博客…

www.youtube.com](https://www.youtube.com/channel/UCFU9Go20p01kC64w-tmFORw) 

# 什么是散点图？

散点图是一种数据显示类型，它使用点来表示两个不同数值变量的值。您可以绘制一个散点图来查看两个数值变量(如体重和身高)之间的关系。你可以用散点图找到变量之间的相关性。

让我们使用 matplotlib 绘制一个散点图。为了展示这一点，让我们导入必要的库。

![](img/68c6189f83a7c3f30c590cfb5bca17d3.png)

你可以在这里找到笔记本和数据集[。现在，让我们使用`%matplotlib inline` magic 命令来启用内嵌绘图。](https://github.com/TirendazAcademy/DATA-VISUALIZATION-WITH-PYTHON/blob/main/06-Scatter%20Plot%20with%20Matplotlib.ipynb)

![](img/a16b3de9b4fdcb23cf1064b770712d42.png)

让我们用 seaborn 指定图形的样式。

![](img/12260536311576fd39f67a739e05eb90.png)

我将使用虹膜数据集来显示散点图。让我们用 seaborn 加载 iris 数据集。

![](img/a0d3367e6b4e7b7690de37bb8599a2f3.png)

让我们看看数据集的第一行。

![](img/5073913c8d704d0ee9ddad81f11b0376.png)![](img/c05b534749ef1ebd2a4dcc2a8c575170.png)

数据集中有 4 个数值变量，它们是萼片高度和宽度、花瓣高度和宽度，还有一个变量表示鸢尾花的三种类型。您可以使用`plot`方法和`scatter`方法绘制散点图。首先，我将展示如何使用`plot`方法。

# 用绘图法绘制散点图

为了展示如何用`plot`方法绘制散点图，让我们创建一个图形对象和图形区域。

![](img/77070c234daa4871aafbfbca840d2e13.png)![](img/038dbedd30ac2bd38e27631dc10f5efe.png)

让我们画一个萼片长度和萼片宽度变量的散点图。

![](img/cee86c6a0021e550b50dc6c5e67a18a9.png)![](img/c01ef767e8ef047a3ef03d18d23f14d8.png)

萼片长度和萼片宽度变量的散点图

你可以改变点的形状。为此，让我们使用`+`符号。

![](img/997d7aed8e61be74b367ced9997c765b.png)![](img/6ada071b2d83908ef5aad7f9f58a1943.png)

带+的散点图

让我们使用`v`选项查看三角形散点图。

![](img/134a400abbb587eda163b18df7afde67.png)![](img/bf141cb5bb97e80c89759aed824105e7.png)

三角形散点图

默认情况下，点的颜色是蓝色。你可以改变颜色。例如，让我们使用带有`rv`选项的红色。

![](img/f33b0e86c6eb8ffca82786e05b809da4.png)![](img/b83720ac334e7862ecd473199e3daae9.png)

三角形和红色散点图

您也可以使用`marksize`参数指定点的大小，如下所示:

![](img/01363563fb1f8886ab1a494dd8537d63.png)![](img/ad550d426e0c91ac54b17eab662744da.png)

三角形和红色散点图

# 使用散点图方法的散点图

你也可以用`scatter`方法画散点图。该方法类似于`plot`方法。让我们用这种方法画出萼片宽度和花瓣宽度的散点图。

![](img/68775b583c36760858f8b819027fc341.png)![](img/9cd135b4319841d8d8ea4b1bd4e06cf7.png)

使用散点图方法的散点图

您可以使用`s`参数调整圆点的大小。

![](img/a274f6b6489c8001b1f70369e1e70b13.png)![](img/e958af35a2123c0aca98b79ec14374e2.png)

带 s 参数的散点图

# 散点图法与图法

对于大样本，绘图法比分散法更快。此外，点的属性在散点法中单独输入。`c`参数用于点的颜色。例如，让我们使用紫色。

![](img/39abb14f9028938c1078363f44d77132.png)![](img/8ce29d08f2678a80bc57c0ed10cfcdce.png)

紫色散点图

您可以将`marker`参数用于点的形状。例如，让我们用三角形来画散点图。

![](img/c980fb5b29b4d256e96e29ab72e249d1.png)![](img/71f477c74b439ae8375fabe87551a064.png)

带三角形的散点图

您可以使用`edgecolor`参数表示圆点的边框颜色，使用`linewidth`参数表示粗细。

![](img/86c3d086b224501a9a5f3a874ef3b3f4.png)![](img/8281cc48ce0c3c3122e1f28914bb4331.png)

带点边框颜色的散点图

您也可以使用`alpha`参数调整点的可见性。

![](img/dda0cb6c0790cacf98a16aae0f0b5f5d.png)![](img/ff11a4d06318a55f6e4fcf2f1933b587.png)

使用 alpha 参数的散点图

您可以为每个点指定一种颜色。为了展示这一点，让我们为此创建一个颜色变量，并用`random.randint`方法生成 150 个介于 0 和 10 之间的数字。

![](img/af80a30c0aa0c00cfad5c10f104a8016.png)

接下来，让我们将这个颜色变量设置为`c`参数。

![](img/1682bb1ccc025a3e4d83519ac39bb575.png)![](img/9b07209f83c35b6825ae49bb97e92e68.png)

带颜色的散点图

您可以使用`cmap`方法调整点的颜色。在设置`cmap`方法的颜色时，需要以大写字母开始第一个字母，以字母 s 结束。

![](img/c5e5fa66d7b32e1bd219e8162dc38fe0.png)![](img/8a14ee53df38866f66c370ce32f34996.png)

散点图

对于更多不同的颜色，您可以使用`"viridis"`选项。

![](img/661347a74057efa8a3ce8a1a68158499.png)![](img/ef34492692bb8dd694f78351292ee315.png)

带`viridis`选项的散点图

您也可以使用`colorbar`方法添加一个显示颜色数值的条。

![](img/ca863846a8fc56a5ab97cb1ec2beb37f.png)![](img/96d2a788bc8ea726e4d7af9117340daa.png)

带颜色条的散点图

您也可以使用`set_label`方法命名颜色条。

![](img/9e40ceb1c02f8261e848548330a2cca1.png)![](img/7bb3169e6232710ba35d093ae3c4caba.png)

带颜色条的散点图

此外，您可以调整圆点的大小。为此，让我们用`random.randint`方法创建一个变量。

![](img/90d45ee2b0124e8a3d20023b1bdedce1.png)

接下来，让我们将这个变量设置为参数 s。

![](img/3cc48f89ba243a57861541f39d3054c7.png)![](img/4785e1eaf4d63d8aecc98752554e1ec0.png)

不同大小点的散点图

您可以使用`xlabel`和`ylabel`方法来命名轴。

![](img/238502ecf7982f7d42353df391d4e5a9.png)![](img/b17abc8fa56de15c8fea0e97111cd628.png)

带轴名的散点图

最后，让我们用`title`的方法给这个情节起一个标题。

![](img/76acdc0090ae09b594892c1dc13ed88d.png)![](img/9a2d62820d350db1a340209389ee7c6e.png)

带标题的散点图

# 结论

您可以使用散点图来查看两个变量之间的关系。在这篇文章中，我谈到了使用`plot`和`scatter`方法的散点图。就是这样。我希望你喜欢它。感谢您的阅读。你可以在这里找到这个笔记本。别忘了在 YouTube 上关注我们

![Tirendaz AI](img/384c46a0053e6856ca4df470ae6476d6.png)

[提伦达兹艾](https://tirendazacademy.medium.com/?source=post_page-----abb1a6ad042--------------------------------)

## 用 Python 实现数据可视化

[View list](https://tirendazacademy.medium.com/list/data-visualization-with-python-72919ad57b84?source=post_page-----abb1a6ad042--------------------------------)11 stories![](img/c488dead590dd5010c1227364ba2701f.png)![](img/76b23c08a8d0b95169094b46bd2f6251.png)![](img/f0183d951fb93667dc3324d16a64366a.png)

*如果这篇文章有帮助，请点击拍手👏按钮几下，以示支持👇*