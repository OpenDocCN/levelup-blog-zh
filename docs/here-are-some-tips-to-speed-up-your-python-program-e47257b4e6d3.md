# 这里有一些加快你的 Python 程序的技巧

> 原文：<https://levelup.gitconnected.com/here-are-some-tips-to-speed-up-your-python-program-e47257b4e6d3>

## 思维程序员

## 学习新概念

![](img/d3649f9fca0b2cc79ac351b4725254de.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Towfiqu barb huya](https://unsplash.com/@towfiqu999999?utm_source=medium&utm_medium=referral)拍摄的照片

Python 是发展最快的编程语言，这里有一些方法可以加速你的 python 代码。

首先，我们可以利用定时器来计算程序中任意部分的执行时间。

```
import time
start = time.time()
....
print("% s sec" % (time.time() - start))
```

## 1.LISP 理解

这是 python 中一个奇妙的概念，取自 LISP。如果你能使用列表理解，不要使用任何其他技术，因为它需要更少的时间来执行。

*   方法正确，耗时:【0.00012302398681640625 秒。

```
L = [i for i in range (1, 1500) if i%5 == 0]
```

*   方式错误，耗时: *0.00015282630920410156 秒*。

```
L = []
for i in range (1, 1500):
    if i%5 == 0:
        L.append (i)
```

## 2.使用'。“join”运算符代替“+=”运算符

因为 Python 认为字符串是可变的，所以每次将字符串赋给变量时，都会在内存中创建一个新对象并赋予新值。

*   方法正确，耗时: *3.266334533691406e-05 秒*。

```
concatenated_string = "".join (["Python ", "is ", "fun."])
```

*   方式错误，耗时:*3.45681152344 e-05 秒*。

```
concatenated_string = "Python " + "is " + "fun."
```

## 3.不要用“.”操作

*   方法正确，耗时:*0.00648679351806641 秒*。

```
from math import sqrt
val = sqrt(100)
```

*   方式错误，耗时:*0.00627764892578125 秒*。

```
import math
val = math.sqrt(100)
```

## 4.尽量避免 if-else 条件

让我们尝试新的方法来高效地编写代码。例如，您可以避免在程序中使用 if-else 条件。

*   正确方法

```
if (not a_condition) or (not b_condition):
    raise exception
do_something()
```

*   错误方式

```
if a_condition:
    if b_condition:
        do_something()
else:
    raise exception
```

## 5.使用列表切片

通常，列表切片操作比使用传统循环要快。以下是如何从自然数列表中生成奇数的方法。

*   方法正确，耗时:*0.00399848937988281 秒*。

```
N = [*range(1000)]
print(N[1:len(N):2])
```

*   方式错误，耗时:*0.005199909210205078 秒*。

```
N = [*range(1000)]
odd_num = []
for i in N:
    if i % 2 != 0:
        odd_num.append(i)
print(odd_num)
```

正如我们所看到的，正确的方法花费的时间是错误方法的一半。

在这篇文章中，我介绍了一些有助于提高 Python 代码性能的最佳方法。很简单，对吧？请将它们应用到你的工作中。

以下是我的 10 篇最佳文章

1.  在我 15 年的软件工程师生涯中，我学会了避免的事情
2.  [如何设计一个可以扩展到你的第一个 1 亿用户的系统](/how-to-design-a-system-to-scale-to-your-first-100-million-users-4450a2f9703d)
3.  [每个人都应该知道的关于软件工程师的名言](https://medium.com/geekculture/famous-quotes-about-software-engineer-everyone-should-know-9b946cd6ec66)
4.  [停止在代码中使用‘else’关键字](https://javascript.plainenglish.io/stop-using-the-else-keyword-in-your-code-907e82b3054a)
5.  [我是如何根据目的对 50 种图表类型进行分类的？](https://towardsdatascience.com/how-did-i-classify-50-chart-types-by-purpose-a6b0aa5b812d)
6.  [不要做愚蠢的程序员](/dont-be-a-stupid-programmer-be53ed49f99)
7.  [软件架构:你需要知道的最重要的架构模式](/software-architecture-the-important-architectural-patterns-you-need-to-know-a1f5ea7e4e3d)
8.  [重新思考 JavaScript 中的坚实原则](https://javascript.plainenglish.io/rethinking-solid-principles-in-javascript-7effdd4dc37d)
9.  [这些瓶颈正在扼杀你的代码](https://towardsdatascience.com/how-did-i-classify-50-chart-types-by-purpose-a6b0aa5b812d)
10.  [每个开发者都必须知道的 20 大+ Github 回购](/top-10-github-repos-every-developer-must-know-9da14292e284)