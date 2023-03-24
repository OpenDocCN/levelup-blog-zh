# 一座崛起的图书馆在表演上击败了熊猫

> 原文：<https://levelup.gitconnected.com/a-rising-library-beating-pandas-in-performance-401d246a8569>

## 比较 pypolars 和熊猫的表现

![](img/c4231456c72f1ea3136a2b01d1af621f.png)

照片由[施洋许](https://unsplash.com/@ltmonster?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

**熊猫**最初于 2008 年发布，用 [Python、Cython 和 C](https://en.wikipedia.org/wiki/Pandas_(software)) 编写。今天，我们将这个众所周知的库与用 Rust 编写的新兴数据框架库 [**pypolars**](https://github.com/ritchie46/polars) 的性能进行比较。我们在排序和连接一个 25Mil 记录数据时，以及在连接两个 CSV 时，对这两者进行了比较。

# 正在下载 Reddit 用户名数据

让我们首先从 Kaggle 下载一个包含大约 2600 万 reddit 用户名的 CSV 文件:[https://www.kaggle.com/colinmorris/reddit-usernames](https://www.kaggle.com/colinmorris/reddit-usernames)

让我们创建另一个我们将使用的 CSV 文件，您可以使用您喜欢的文本编辑器或通过命令行来创建它:

```
$ cat >> fake_users.csv
author,n
x,5
z,7
y,6 
```

# 整理

现在，让我们比较两者的排序算法:

## 熊猫

```
$ python sort_with_pandas.py 
Time:  34.35690135599998
```

## pypolars

```
$  python sort_with_pypolars.py 
Time:  23.965840922999973
```

> 大约 24 秒，这意味着 pypolars 比熊猫快 1.4 倍

# 串联

现在，我们将了解在连接两个数据框并将它们堆叠到一个数据框中时，每个数据框的性能如何

## 熊猫

熊猫拍摄的 18 秒

```
$ python concat_with_pandas.py 
Time:  18.12720185799992
```

## pypolars

> 这里 pypolars 快 1.2 倍

```
$ python concat_with_pypolars.py 
Time: 15.001723207000055
```

# 连接

## 下载 COVID 跟踪数据

通过此命令从 [COVID 跟踪项目](https://covidtracking.com/data/download)下载数据:

```
$ curl -LO [https://covidtracking.com/data/download/all-states-history.csv](https://covidtracking.com/data/download/all-states-history.csv`)
```

将在这个 **all-states-history.csv** 文件中为您提供冠状病毒在全美传播的最新数据

## 下载状态数据

这是一个 CSV 文件，表示每个州的缩写，因为我们需要它与之前的 CSV 文件相结合，之前的 CSV 文件只在*州*列中列出了缩写。让我们从这个命令中获取这些数据:

```
$ curl -LO [https://gist.githubusercontent.com/afomi/8824ddb02a68cf15151a804d4d0dc3b7/raw/5f1cfabf2e65c5661a9ed12af27953ae4032b136/states.csv](https://gist.githubusercontent.com/afomi/8824ddb02a68cf15151a804d4d0dc3b7/raw/5f1cfabf2e65c5661a9ed12af27953ae4032b136/states.csv`)
```

这将输出 **states.csv** ，该文件有两列:*状态*和*缩写*

## 熊猫

```
$ python join_with_pandas.py 
Time: 0.9691885059996821
```

让我们用 **csvcut** 过滤出这个结果 **joined_pd.csv** 文件:

```
$ csvcut -c date,state,State joined_pd.csv | head | csvlook 
|       date | state | State       |
| ---------- | ----- | ----------- |
| 2020-11-16 | AK    | ALASKA      |
| 2020-11-16 | AL    | ALABAMA     |
| 2020-11-16 | AR    | ARKANSAS    |
| 2020-11-16 | AS    |             |
| 2020-11-16 | AZ    | ARIZONA     |
| 2020-11-16 | CA    | CALIFORNIA  |
| 2020-11-16 | CO    | COLORADO    |
| 2020-11-16 | CT    | CONNECTICUT |
| 2020-11-16 | DC    |             |
```

看起来连接正在工作，并且是左连接。如果您想知道为什么 AS 和 DC 的相关联的 *State* 值为空，那是因为在 **states.csv** 文件本身中不存在缩写。如果您查看*缩写*列，您将发现 AS 和 DC 都没有值。

这里没有缩写:

```
$ grep AS states.csv 
ALASKA,AK
ARKANSAS,AR
KANSAS,KS
MASSACHUSETTS,MA
NEBRASKA,NE
TEXAS,TX
WASHINGTON,WA
```

这里没有 DC 的价值观:

```
$ grep DC states.csv
```

P.S .如果不清楚 **csvcut** 的用途；我们有一些关于 csvkit 的教程(包含 csvcut 和其他一些有用的命令行工具的命令行实用程序，用于清理、处理和分析 CSV)。

[](https://towardsdatascience.com/how-to-clean-csv-data-at-the-command-line-4862cde6cf0a) [## 如何在命令行清理 CSV 数据

### 关于使用命令行程序清理 CSV 文件的深入教程:csvkit 和 xsv 比较…

towardsdatascience.com](https://towardsdatascience.com/how-to-clean-csv-data-at-the-command-line-4862cde6cf0a) [](https://towardsdatascience.com/how-to-clean-csv-data-at-the-command-line-part-2-207215881c34) [## 如何在命令行清理 CSV 数据|第 2 部分

### 关于在排序和连接时使用命令行程序 csvkit 和 xsv 清理大型 CSV 文件的教程…

towardsdatascience.com](https://towardsdatascience.com/how-to-clean-csv-data-at-the-command-line-part-2-207215881c34) 

## pypolars

```
$ python join_with_pypolars.py 
Time:  0.41163978699978543
```

现在我们来看看连接后的数据框是什么样子的:

```
$ csvcut -c date,state,State joined_pl.csv | head | csvlook 
|       date | state | State       |
| ---------- | ----- | ----------- |
| 2020-11-16 | AK    | ALASKA      |
| 2020-11-16 | AL    | ALABAMA     |
| 2020-11-16 | AR    | ARKANSAS    |
| 2020-11-16 | AZ    | ARIZONA     |
| 2020-11-16 | CA    | CALIFORNIA  |
| 2020-11-16 | CO    | COLORADO    |
| 2020-11-16 | CT    | CONNECTICUT |
| 2020-11-16 | DE    | DELAWARE    |
| 2020-11-16 | FL    | FLORIDA     |
```

所以看起来这里 **pypolars** 遗漏了它所连接的列的空值，但这是因为默认连接是内部连接，而不像 pandas 的默认连接是左连接。要获得与熊猫相同的结果，您需要将第 8 行改为:

```
df_all_states_history.join(df_states, left_on=”state”, right_on=”Abbreviation”, how=”left”).to_csv(“joined_pl.csv”)
```

在我的机器上大约有 317 毫秒，这里的意思是:

> pypolars 的左连接速度快 3 倍

# 最后的想法

最后，我们发现了与熊猫相比，大熊猫的表现如何。当然，**熊猫**更成熟了，因为 12 年过去了，社区仍然在投资它，但是如果在 **pypolars** 上有更多的合作；这个崛起的图书馆将会震撼！

您可能会发现这些教程很有用:

*   [如何在命令行清理文本数据](https://www.ezzeddinabdullah.com/posts/how-to-clean-text-data-at-the-command-line) ‍

[](https://towardsdatascience.com/how-to-clean-text-files-at-the-command-line-ce2ff361a16c) [## 如何在命令行清理文本文件

### 关于使用命令行工具清理数据的基础教程:tr、grep、sort、uniq、sort、awk、sed 和 csvlook

towardsdatascience.com](https://towardsdatascience.com/how-to-clean-text-files-at-the-command-line-ce2ff361a16c) 

*   为什么我们使用 docker

[](https://medium.com/swlh/penguins-in-docker-a-tutorial-on-why-we-use-docker-ce67cebf65f9) [## Docker 中的企鹅——关于我们为什么使用 Docker 的教程

### 关于 docker 以及如何构建 docker 文件、挂载卷和运行 docker 映像的基础教程

medium.com](https://medium.com/swlh/penguins-in-docker-a-tutorial-on-why-we-use-docker-ce67cebf65f9) 

保重，下次教程再见:)

和平！

## [**点击这里**](https://upbeat-crafter-1565.ck.page/0f7fd6d5d6) **获取新内容到你的收件箱**

# 资源

*   [熊猫百科](https://en.wikipedia.org/wiki/Pandas_(software))
*   [熊猫文献](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html)
*   [里奇·温克创作的 pypolars 回购](https://github.com/ritchie46/polars)
*   [pypolars 文档](https://ritchie46.github.io/polars/pypolars/frame.html)
*   [里奇·温克对 Reddit 的评论](https://www.reddit.com/r/rust/comments/jqiwpj/xsv_a_commandline_toolkit_for_csv_data_written_in/gbrfexy?utm_source=share&utm_medium=web2x&context=3)
*   [COVID 跟踪数据](https://covidtracking.com/data/download)
*   [国家缩写数据](https://worldpopulationreview.com/states/state-abbreviations)
*   [Reddit 用户名数据集| Kaggle](https://www.kaggle.com/colinmorris/reddit-usernames)

# 最初共享

[](https://www.ezzeddinabdullah.com/posts/a-rising-library-beating-pandas-in-performance) [## 一个崛起的图书馆在表演中击败熊猫

### pandas 最初是在 2008 年发布的，用 Python、Cython 和 c 编写。今天，我们将比较它的性能…

www.ezzeddinabdullah.com](https://www.ezzeddinabdullah.com/posts/a-rising-library-beating-pandas-in-performance)