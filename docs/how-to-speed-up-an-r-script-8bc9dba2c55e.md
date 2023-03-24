# 如何加速一个 R 脚本

> 原文：<https://levelup.gitconnected.com/how-to-speed-up-an-r-script-8bc9dba2c55e>

![](img/5bb5e6076731df7a925fd143afc44c5c.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 拍照

**R** 是一种[编程语言](https://en.wikipedia.org/wiki/Programming_language)和[用于](https://en.wikipedia.org/wiki/Free_software)[统计计算](https://en.wikipedia.org/wiki/Statistical_computing)和图形的自由软件环境。它可以在多种 UNIX 平台、Windows 和 MacOS 上编译和运行。

r 是一种[解释语言](https://en.wikipedia.org/wiki/Interpreted_language)，因此与在主机 [CPU](https://en.wikipedia.org/wiki/CPU) 上直接执行本机[机器码](https://en.wikipedia.org/wiki/Machine_code)相比，其缺点之一是执行速度较慢。

在本文中，我们看到了一个在使用大型数据集时如何加速 R 代码的例子。

当然，还有其他方法可以提高我将要介绍的代码的性能

我注意到，当使用数据帧并在一个周期内改变数据帧本身时，计算成本很高。但是如果你用向量来保存你在循环中所做的改变。在循环结束时，直接用矢量数据替换数据框的列。这样，在性能方面有很大的好处。

然后在循环之前，为新的或要修改的要素创建向量。

对于新要素，首先在数据框中创建列，然后将其复制到矢量中。

```
# I copy the columns in support array to optimize the execution time
longitudine <- dati$Longitude
latitudine <- dati$Latitude
date_time <- dati$Date_Time# new feature
dati$distance <- 0
dati$vel <- 0
dati$delta_time <- 0
dati$angle <- 0

distance <- dati$distance
vel <- dati$vel
delta_time <- dati$delta_time
angle <- dati$angle
```

在循环中，您将直接对向量赋值，如下所示:

```
distance[i_row] <- distGeo(c(longitudine[i_row-1], latitudine[i_row-1]), c(longitudine[i_row], latitudine[i_row]))
```

而不是:

```
dati$distance[i_row] <- distGeo(c(dati$Longitude[i_row-1], dati$Latitude[i_row-1]), c(dati$Longitude[i_row], dati$Latitude[i_row]))
```

在循环结束时，用矢量数据替换数据框的列。

```
# I insert the new calculated values in the dataframe
dati$distance <- distance
dati$vel <- vel
dati$delta_time <- delta_time
dati$angle <- angle
```

## 我还报告了一个带有完整代码和执行时间的示例。

为了比较运行时间，我使用了这个数据集。数据集是通过运行本文[文章](https://medium.com/analytics-vidhya/data-mining-of-geolife-dataset-560594728538)的第一个脚本产生的，取 3，387，304 的前 200，000 行(运行时间因素)。

为了方便起见，我放了下载数据集的链接:

 [## dataset_raw.csv

### 数据集

drive.google.com](https://drive.google.com/file/d/1Lj-FIDLMTGLJNR3oEOtB0kE6qtFEeH3M/view?usp=sharing) 

这是直接修改数据框而不使用支持向量的代码。我们称之为“慢”代码:

这段代码使用支持向量，并在循环结束时修改数据帧。我们称之为“快速”代码:

“快速”代码在我的 Mac 上运行了 55 秒。而“慢”代码在我的 Mac 上花了 16 分钟。超过 16 倍的性能提升。

根据我的经验，在 R 中还有其他优化性能的方法，在这种情况下，这个解决方案极大地提高了我的性能。

如果你想尝试整个数据集，我会给你链接:

 [## 数据集 _ 原始 _ 完整. csv

### 数据集已满

drive.google.com](https://drive.google.com/file/d/1jMawgmzkmRAgJqJP2B5DUeAbrgx3MCBh/view?usp=sharing) 

我希望我的文章对你有用，如果你有任何问题，请写在评论里。

感谢阅读！

*为了获得无限的故事，你也可以考虑只花 5 美元注册成为媒体会员。如果你使用我的* [*链接*](https://pietrocolombo.medium.com/membership) *注册，我会收到一小笔佣金。*