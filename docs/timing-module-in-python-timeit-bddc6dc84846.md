# Python 中的计时模块— timeit

> 原文：<https://levelup.gitconnected.com/timing-module-in-python-timeit-bddc6dc84846>

## 理解如何使用“timeit”模块为 Python 代码计时的指南。

![](img/b08e08da1199487a4884f0f04f806544.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[尹新荣](https://unsplash.com/@insungyoon?utm_source=medium&utm_medium=referral)拍摄的照片

Python 有一个名为“timeit”的内置计时模块，可以帮助用户知道代码运行需要多长时间。它还可以帮助找出是否有任何一行代码降低了整个程序的速度。

这个模块有一个命令行界面和一个可调用函数。

```
**>>> import** timeit
```

让我们从计时创建字符串的代码开始。我们试图创建的字符串是“0，1，2，3，…”。,99"

```
>>> ",".join(str(n) **for** n **in** range(100))'0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99'
```

现在我们将通过向 time it 函数传递两个参数来计时。第一个参数是字符串格式的代码，第二个参数是希望运行多少次。

```
>>> timeit.timeit('",".join(str(n) for n in range(100))', number **=** 10000)0.5468854000000647
```

现在让我们尝试使用列表理解创建相同的字符串。

```
>>> timeit.timeit('",".join([str(n) for n in range(100)])', number **=** 10000)0.48502080000002934
```

现在使用 map 函数创建相同的字符串。

```
>>> timeit.timeit('",".join(map(str,range(100)))', number **=** 10000)0.3379802000000609
```

因此可以看出，使用 map 函数花费的时间最少。

# 使用 timeit 内置的神奇功能

如果使用 jupyter 笔记本，可以使用内置魔法。当使用 timeit 作为内置魔术函数时，不需要将代码作为字符串传递。

```
**>>> %**timeit ",".join(str(n) **for** n **in** range(100))
58.5 µs ± 3.58 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)**>>> %**timeit ",".join([str(n) **for** n **in** range(100)])
46.4 µs ± 4 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)**>>> %**timeit ",".join(map(str,range(100)))
30.4 µs ± 3.48 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)
```

参考笔记本[这里](https://github.com/jayashree8/Python_guide/blob/master/Python%20modules%20and%20packages/Timing%20module%20in%20Python%20-%20timeit.ipynb)。

## 学习 Python 可以参考的入门书籍:

[](https://amzn.to/3yDY4To) [## Python 速成班，第二版:基于项目的编程入门实践

### 世界上最畅销的 Python 书籍的第二版。一个快速的，没有废话的 Python 编程指南…](https://amzn.to/3yDY4To) [](https://amzn.to/3vtvQZv) [## 艰难地学习 Python:一个非常简单的介绍可怕的美丽世界…

### 你会学习 Python！Zed Shaw 完善了世界上最好的学习 Python 的系统。遵循它，你会…](https://amzn.to/3vtvQZv) [](https://amzn.to/3urluYI) [## 思考 Python，2e:如何像计算机科学家一样思考

### 思考 Python，2e:如何像计算机科学家一样思考](https://amzn.to/3urluYI) 

## 学习 Python 可以参考的高级书籍:

[](https://amzn.to/3fMzVBn) [## 编程 Python:强大的面向对象编程

### 如果你已经掌握了 Python 的基础，你就可以开始使用它来完成真正的工作了。编程 Python 将…](https://amzn.to/3fMzVBn) [](https://amzn.to/34oFFMl) [## 高级 Python 编程:使用以下工具构建高性能、并发和多线程应用

### 关键特性使用 Dask 和 PySpark Master 技能在集群上设置和运行分布式算法，以准确地…](https://amzn.to/34oFFMl) 

> *联系我:* [*LinkedIn*](https://www.linkedin.com/in/jayashree-domala8/)
> 
> *查看我的其他作品:* [*GitHub*](https://github.com/jayashree8)