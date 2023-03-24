# 熊猫分级索引

> 原文：<https://levelup.gitconnected.com/hierarchical-indexing-in-pandas-94ff198b7f35>

## 关于如何对 Pandas 使用层次索引(MultiIndex)的指南。

![](img/317be2172d295105282fbd586f431a36.png)

[钳工](https://unsplash.com/@benchaccounting?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

分级索引允许我们在一个轴上使用多个索引级别。分层索引也称为多重索引。在这篇文章中，我将展示如何使用层次索引。简而言之，我将涵盖以下主题:

*   什么是 MultiIndex？
*   什么是拆分？
*   什么是层次索引？
*   分层索引中的选择
*   什么是 Swaplevel？
*   分级索引中的排序
*   分层索引中的汇总统计信息
*   数据框中的分层索引

让我们开始吧。

首先，我打算进口熊猫。

![](img/c2b65144cfacb82baea0be859d9a4a63.png)

让我们从正态分布中生成随机数据。

![](img/ef880222471ae1c32dee3719a5fc7c21.png)

# 什么是 MultiIndex？

MultiIndex 允许您在索引中选择多个行和列。为了理解 MultiIndex，让我们看看数据的索引。

![](img/41d68b4aa18b6ccab43e9a9ff9741f88.png)

MultiIndex 是一种用于数据帧的高级索引技术，它显示了多个级别的索引。我们的数据集有两个层次。您可以使用索引获得数据的子集。例如，让我们看看索引为 a 的值。

![](img/e19176fe5ccbfbb21978f68515bc7244.png)

你可以把数据切片。

![](img/ae35686060d762be46affb2c6ac25623.png)

你可以看到不止一个指数。例如，让我们看看索引为 a 和 c 的值。

![](img/0d4d82393f2d0625ec3956eb03288e63.png)

您可以从内部索引中选择值。让我们看看内部索引的第一个值。

![](img/0209ef5ccd8c968a57433b3e68407736.png)

# 什么是拆分？

stack 方法将列名转换为索引值，unstack 方法将索引值转换为列名。您可以使用拆分方法以表格的形式查看数据。

![](img/878f55531401de96b39cf8b2ea72d1a9.png)

要恢复数据集，可以使用 stack 方法。

![](img/1fb712981646253eb00ced3e1c5370ff.png)

# 什么是层次索引？

分层索引是一种在数据集中创建结构化组关系的方法。数据框可以有等级索引。为了展示这一点，让我创建一个数据集。

![](img/c434eaccafc4196b6241e6c967dcb940.png)

注意，在这个数据集中，行和列都有层次索引。您可以命名层次级别。让我们看看这个。

![](img/9f82e0b15c781105c6ca90cf62fc8dc5.png)

# 分层索引中的选择

您可以选择数据的子组。例如，让我们选择名为 num 的索引。

![](img/52fe68ae5ca98e749d72be36c5c6d530.png)

# 什么是 Swaplevel？

有时，您可能想要交换索引的级别。为此，您可以使用 swaplevel 方法。swaplevel 方法采用两个级别并返回一个新对象。例如，让我们交换数据集中的课程和考试索引。

![](img/ccd01ab29733fe1f3c6d67b125e3dbf0.png)

# 分级索引中的排序

要按级别对索引进行排序，可以使用 sort_index 方法。例如，让我们按级别 1 对数据集进行排序。

![](img/d66c80a7c619678cf51b47da0c227c6f.png)

# 分层索引中的汇总统计信息

系列或数据帧中的汇总统计数据可以通过一个级别找到。如果您有多个级别的数据，您可以根据级别计算汇总统计数据。例如，让我们根据数据集中的考试级别来查看总和值。

![](img/7dbfbd25e8300a98839e64558b87f8af.png)

让我们根据字段级别来查看总值。

![](img/bee3517bbca12d39955d8159cb7cea21.png)

# 数据框中的分层索引

您可以将数据帧的列移动到行索引。为了展示这一点，让我们创建一个数据集。

![](img/bb40dafed7649b55408a7a876a744095.png)

让我们将这个数据集的 a 列和 b 列转换成一个行索引。

![](img/8af45aa3495e4a8b464c12bf907536b3.png)

在 set_index 方法中，移动到行的索引将从列中删除。可以使用 drop = False 将获得的列作为索引保留在同一位置。

![](img/fddd406f0d4fcfc9675c77e4aed474f5.png)

让我们首先看一下 data2 来演示 reset_index 方法。

![](img/ae34233e2e63b3b67a8cf01abe0cb9cb.png)

您可以使用 reset_index 方法来恢复数据集。

![](img/68880640b1a6b5909925e090d2d02ce5.png)

就是这样。在这篇文章中，我解释了熊猫的层次索引。我希望你喜欢这篇文章。感谢阅读。在这里可以找到笔记本[。](https://github.com/TirendazAcademy/PANDAS-TUTORIAL/blob/main/13-Hierarchical%20Indexing.ipynb)

别忘了在[YouTube](http://youtube.com/tirendazacademy)|[Twitter](http://twitter.com/tirendazacademy)|[GitHub](http://github.com/tirendazacademy)|[Linkedin](https://www.linkedin.com/in/tirendaz-academy)|[ka ggle](https://www.kaggle.com/tirendazacademy)上关注我们

[](https://medium.com/codex/python-pandas-tutorial-42be3e827e2a) [## Python 熊猫教程

### Pandas 是 Python 最重要的库之一。在这篇博文中，我将谈论熊猫图书馆并展示…

medium.com](https://medium.com/codex/python-pandas-tutorial-42be3e827e2a) [](/practical-data-analysis-with-pandas-c40fbd2955fa) [## 熊猫的实用数据分析

### 在我的上一篇文章中，我提到了在熊猫图书馆使用数据。Python 最重要的库之一是熊猫…

levelup.gitconnected.com](/practical-data-analysis-with-pandas-c40fbd2955fa) 

如果这篇文章有帮助，请点击拍手👏按钮几下，以示支持👇