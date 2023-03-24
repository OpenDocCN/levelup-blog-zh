# 阅读熊猫 CSV 中特定栏目的最佳方式

> 原文：<https://levelup.gitconnected.com/best-way-to-read-specific-columns-from-csv-in-pandas-7e58507171b3>

![](img/7fe1966f52474addab4365fead48fbec.png)

我和一个朋友聊天，他正在做有很多专栏的 CSV，他问我最好的方法是只阅读他需要的专栏。在我看来，最好的方法是将数据存储在数据库中，这样你就可以只选择你需要的列，但不幸的是，这对他来说不是一个选择。我也想过在命令行上使用`cut`或`awk`来预过滤数据，但是为了简单起见，我认为 Pandas 是正确的工具。

然而，就像熊猫的其他事情一样，仍然有多种方式来实现这一点。两种显而易见的方法是在用`read_csv`读入数据时过滤数据，以及读取全部内容并只选择您需要的列。我的逻辑告诉我，用`read_csv`过滤将是正确的方法，但我心中的数学家需要证明。这就是:

我发现了一个很酷的库，叫做 [memory-profiler](https://pypi.org/project/memory-profiler/) ，它可以帮助我查看一个特定函数使用了多少内存，并使用 good ol’`timeit`来查看它运行了多长时间。

```
from timeit import timeit
from memory_profiler import profileimport numpy as np
import pandas as pd
```

我使用了一个函数来获得类似电子表格的列名(' A '，' B'…'AA '，' AB'…):

```
def colnum_string(n):
    """Convert number to string like: 1->A, 2->AA"""
    string = ""
    while n > 0:
        n, remainder = divmod(n - 1, 26)
        string = chr(65 + remainder) + string
    return string
```

为了设置我的测试，我创建了一个具有 150 列和 100，000 行的大型 csv:

```
df = pd.DataFrame(
    np.random.randint(0, 100, size=(100000, 150)),
    columns=[colnum_string(i) for i in range(1, 151)],
)
df.to_csv("test.csv")
```

对于我的测试 a，选择 4 个(不连续的)列:

```
test_cols = [colnum_string(i) for i in [1, 50, 103, 150]]
```

我还想看看使用`df[cols]`和更具体的`df.loc[:, cols]`之间是否有明显的区别。下面是我如何设置和运行我的测试:

```
[@profile](http://twitter.com/profile)
def filter_during_read():
    return pd.read_csv("test.csv", usecols=test_cols)[@profile](http://twitter.com/profile)
def filter_after_read():
    df = pd.read_csv("test.csv")
    return df[test_cols][@profile](http://twitter.com/profile)
def filter_after_read_loc():
    df = pd.read_csv("test.csv")
    return df.loc[:, test_cols]pad = "-------------------"
for func in [
    filter_during_read,
    filter_after_read,
    filter_after_read_loc,
]:
    print(pad + "Testing " + func.__name__ + pad)
    print(
        "Took {} seconds to run {}".format(
            timeit(func, number=3), func.__name__
        )
    )
```

结果是(击鼓…):

```
---------------------Testing filter_during_read---------------------
Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    32    206.7 MiB    206.7 MiB   [@profile](http://twitter.com/profile)
    33                             def filter_during_read():
    34    206.7 MiB      0.0 MiB       return pd.read_csv(
    35    226.0 MiB     19.3 MiB           "test.csv", usecols=test_cols
    36                                 )Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    32    226.0 MiB    226.0 MiB   [@profile](http://twitter.com/profile)
    33                             def filter_during_read():
    34    226.0 MiB      0.0 MiB       return pd.read_csv(
    35    230.2 MiB      4.2 MiB           "test.csv", usecols=test_cols
    36                                 )Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    32    230.2 MiB    230.2 MiB   [@profile](http://twitter.com/profile)
    33                             def filter_during_read():
    34    230.2 MiB      0.0 MiB       return pd.read_csv(
    35    233.3 MiB      3.1 MiB           "test.csv", usecols=test_cols
    36                                 )Took 1.0760158769553527 seconds to run filter_during_read---------------------Testing filter_after_read---------------------
Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    33    229.3 MiB    229.3 MiB   [@profile](http://twitter.com/profile)
    34                             def filter_after_read():
    35    337.4 MiB    108.0 MiB       df = pd.read_csv("test.csv")
    36    337.8 MiB      0.4 MiB       return df[test_cols]Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    33    222.4 MiB    222.4 MiB   [@profile](http://twitter.com/profile)
    34                             def filter_after_read():
    35    448.5 MiB    226.1 MiB       df = pd.read_csv("test.csv")
    36    448.5 MiB      0.0 MiB       return df[test_cols]Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    33    333.2 MiB    333.2 MiB   [@profile](http://twitter.com/profile)
    34                             def filter_after_read():
    35    451.4 MiB    118.3 MiB       df = pd.read_csv("test.csv")
    36    451.4 MiB      0.0 MiB       return df[test_cols]Took 4.564797632978298 seconds to run filter_after_read-------------------Testing filter_after_read_loc-------------------
Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    39    336.2 MiB    336.2 MiB   [@profile](http://twitter.com/profile)
    40                             def filter_after_read_loc():
    41    448.4 MiB    112.2 MiB       df = pd.read_csv("test.csv")
    42    448.4 MiB      0.0 MiB       return df.loc[:, test_cols]Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    39    333.2 MiB    333.2 MiB   [@profile](http://twitter.com/profile)
    40                             def filter_after_read_loc():
    41    448.4 MiB    115.2 MiB       df = pd.read_csv("test.csv")
    42    448.4 MiB      0.0 MiB       return df.loc[:, test_cols]Filename: read_csv_profiling.pyLine #    Mem usage    Increment   Line Contents
================================================
    39    333.2 MiB    333.2 MiB   [@profile](http://twitter.com/profile)
    40                             def filter_after_read_loc():
    41    448.4 MiB    115.2 MiB       df = pd.read_csv("test.csv")
    42    448.4 MiB      0.0 MiB       return df.loc[:, test_cols]Took 4.263103098026477 seconds to run filter_after_read_loc
```

因此，正如我们所看到的，在这个测试中，使用`read_csv`中的`usecols`参数过滤我们需要的列大约快了 4 倍，并且使用了几乎一半的内存。与使用`df.loc[:, cols]`相比，使用`df[cols]`选择器似乎不会有太大的性能损失。

对于未来的测试，选择随机数量的随机选择的列而不是 set 4 会很有趣。