# 在 Python 上。append()时间复杂度

> 原文：<https://levelup.gitconnected.com/on-pythons-append-time-complexity-19dfbff1c87>

## 有这么简单吗？

Python 中最常用的函数之一是[列表的追加函数](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)。它所做的非常简单，它会将指定的元素添加到列表的末尾。

```
 >>> a = [1,2,3]
    >>> a.append(4)
    >>> a
    [1, 2, 3, 4]                                                                                                                                     >>> a.append(4)                                                                                                                                                                      >>> a                                                                                                                                                                                [1, 2, 3, 4]
```

然而，我们真的知道它是如何工作的吗？在这里，我将尝试解释它，以及为什么这是一个比我们想象的更昂贵的操作。

![](img/7561551280a544ef1fdd0563324e5c36.png)

[图片来源](https://pixabay.com/images/id-1544373/)

尽管这对我们来说是透明的，python 的列表有一个固定的分配长度，它总是 2 的幂。

每次我们追加一个新元素，如果它合适，这个操作只需要 O(1)时间。毕竟， [Python 会跟踪列表](https://www.geeksforgeeks.org/internal-working-of-the-len-function-in-python/)的长度，所以它不需要遍历列表来了解它的长度。这给我们带来的好处是，它只需要计算需要存储新值的内存地址，这需要恒定的时间。

相反，如果我们达到容量，我们将需要加倍分配给列表的空间。这将意味着我们需要将列表的当前内容复制到新的内存空间中，这将花费 O(n)时间。

不过，这种操作只是偶尔发生(更具体地说，每次长度都是 2 的幂)，而且大多数时候。append()将花费 O(1)时间，也就是说，这些 O(n)操作被分摊到更常见的 O(1)操作中。

使用[聚集方法](https://www.cs.cornell.edu/courses/cs3110/2011sp/Lectures/lec20-amortized/amortized.htm)，我们可以确定一个列表的摊余时间复杂度。append()运算是 O(1)。对此的直觉可能如下:为了达到最坏的情况，即 O(n)，我们需要执行 n 次操作(在这种情况下，n 是 2 的幂):直到那时，每次操作花费 O(1)时间。这意味着这 n 次操作大约需要 O(2n)时间，也就是 O(n)。最后我们可以确定每项操作的摊余成本为 O(n) / n = O(1)。