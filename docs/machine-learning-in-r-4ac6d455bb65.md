# 数据预处理——R 中的机器学习

> 原文：<https://levelup.gitconnected.com/machine-learning-in-r-4ac6d455bb65>

![](img/9fec0319833b4cfc5778815d6a8ea5e3.png)

https://www.flickr.com/photos/mikemacmarketing/30212411048

在过去的几周里，我一直在研究机器学习软件 *R* 和 *Python* 并且还学习了几门课程。我注意到我所有的程序都有一个共同点，那就是对数据进行预处理，以便应用机器学习模型。大多数情况下，数据预处理过程分为以下步骤:

*   导入数据集。
*   完成缺失的数据。
*   编码分类数据。
*   分割数据集。
*   特征缩放。

## 导入数据集

有几种方法可以导入*数据集*。最简单的方法是从一个. csv 文件中导入数据集*。为此，您必须执行以下操作:*

首先你必须设置你的工作目录，

```
#setwd("~/Desktop/ML/Project1") #Change it to your working directory
setwd("<directory_where_your_dataset_is_located>")
```

一旦您建立了工作目录，您必须导入*数据集*，

```
dataset = read.csv('Data.csv')
```

命令`read.csv('filename')`接收不同的可选参数，根据数据集在*上的排列方式，您必须使用其中一些参数。csv* 文件。您可以设置`sep`参数来指示文件上的分隔符。举个例子，

```
dataset = read.csv('Data.csv', sep = ';')
# sep = ';' indicates that the separator between each data is ;
```

## 完成缺失的数据

填写缺失数据是可选的。如果你的*数据集*是完整的，你显然不需要做这部分。但有时您会发现*数据集*缺少一些单元格，在这种情况下，您可以做两件事:

*   删除一整行(不推荐，您可能会删除关键信息)。
*   用该列含义来完成所缺的信息。

拿下面这个不完整的*数据集*，

如您所见，有一些缺失的单元格，一个在**年龄**列，另一个在**收入**列。为了用每一列的平均值 T27 来填充那些缺失的单元格，你必须执行以下操作:

```
dataset$Age = ifelse(is.na(dataset$Age), ave(dataset$Age, FUN = function(x) mean(x, na.rm = TRUE )), dataset$Age)dataset$Income = ifelse(is.na(dataset$Income), ave(dataset$Income, FUN = function(x) mean(x, na.rm = TRUE )), dataset$Income)
```

我们已经检查了每一列上是否有空单元格。如果有，那么空单元格将被替换为列的平均值。

输出如下所示，

既然数据已经完成，我们可以进入下一步。

## 编码分类数据

这一步也是可选的。根据您的*数据集*，您可能从一开始就有一个*数据集*，其中已经编码了分类数据。在那种情况下，你不需要这样做。

在我们的例子中，我们有**毕业生**列，该列有 2 个可能的值，或者**是**或者**否**。为了能够处理这些数据，我们必须对其进行编码，这意味着将标签改为数字。在 *R* 中做到这一点真的很简单，你只需要做到以下几点，

```
dataset$Graduate = factor(dataset$Graduate, 
                         levels = c('yes', 'no'), 
                         labels = c(1, 0))
```

输出如下所示，

## 分割数据集

这一部分是强制性的，也是使用机器学习模型时最重要的部分之一。

分割*数据集*意味着你要将整个数据集分成两部分，即*训练集*和*测试集*。当你想训练一个模型来解决或预测一件特定的事情时，你首先要训练你的模型，然后测试模型是否做了正确的预测。

通常比例为 80% *训练集*和 20% *测试集*，但可能会因您的型号而异。我们将按照这个比例分割数据集。

您首先必须通过执行以下操作来安装一个名为`caTools`的包，

```
packages.install('caTools')
```

一旦安装完毕，你必须告诉用户你将使用这个库，

```
library(caTools)
```

下一步是创建一个*种子*，这将有助于随机化数据分割方式，然后继续分割*数据集*。为此，请键入以下内容，

```
#Creates a seed, you can type any number, not just 123
set.seed(seed = 123)#SplitRatio indicates the size of the training set
split = sample.split(dataset$Purchased, SplitRatio = 0.8)
training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)
```

既然数据已经分割，我们可以继续进行最后一步。

## 特征缩放

这最后一步也不总是必要的。在数据集中，有一些值不在同一个范围内，例如，年龄和收入具有非常不同的范围。

大多数机器学习模型使用两点之间的[欧几里德距离](https://hlab.stanford.edu/brian/euclidean_distance_in.html)工作，但是由于尺度不同，两点之间的距离可能很大，这可能会给你的模型带来问题。

有些模型已经处理了这一点，因此您不必自己动手，但其他一些模型要求您先对要素进行缩放。为了扩展我们的数据，我们必须运行以下代码，

```
training_set[, 1:2] = scale(training_set[, 1:2])
test_set[, 1:2] = scale(test_set[, 1:2])
```

正如您所看到的， *R* 有一个缩放所选列的函数，在我们的例子中，我们从前两列开始缩放所有的行。

编码的分类数据呢？我们也需要扩展它吗？

有人说对分类数据进行编码是有用的，有人说没有必要。我实验过的是，没那么重要，看你自己。

## 结论

在多次完成所有这些数据预处理步骤后，您会注意到，如果您的数据从一开始就做好了准备，这些步骤中的一些可以省略。

为什么所有这些步骤都很重要？机器学习最关键的部分之一是拥有一个准备充分且值得信赖的*数据集*，以正确的方式准备你的信息是拥有一个好的机器学习模型的进一步发展。

查看本文的 python 版本:[https://medium . com/@ gmotzespina/machine-learning-with-python-d 99 f 13 a9 e 395](https://medium.com/@gmotzespina/machine-learning-with-python-d99f13a9e395)