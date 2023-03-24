# Python 中声明式和命令式编程风格的映射、归约和过滤

> 原文：<https://levelup.gitconnected.com/map-filter-reduce-in-declarative-imperative-programming-styles-in-python-c7affa73542a>

通过声明式和命令式编程方法了解 Python 中的高级函数

![](img/25eb04a12fa4c8a01a69a56d07980814.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是声明式编程？

这是一种编程方法，在这种方法中，编写程序的目的是描述**它必须做什么/ **它打算解决什么**问题，而不是它应该如何解决问题。**

声明式编程方法没有详细描述控制流。

例如，为了在 Python 中对列表进行排序，可以如下使用声明性编程方法:

```
nums = [2,5,4,1,6,3]nums.sort()# Sorts the list to: [1,2,3,4,5,6]
```

# 什么是命令式编程？

这是一种显式编写程序控制流的编程方法。

它提到了如何解决问题/如何完成任务。

例如，通过冒泡排序对列表进行排序的命令式编程方法如下:

```
nums = [2,5,4,1,6,3]**def** bubblesort(elements):
    swapped **=** False
    **for** n **in** range(len(elements)**-**1, 0, **-**1):
        **for** i **in** range(n):
            **if** elements[i] > elements[i **+** 1]:
            swapped **=** True
            elements[i], elements[i **+** 1] **=** elements[i **+** 1], elements[i]
        **if** **not** swapped:
            **return**bubblesort(nums)# Sorts the list to: [1,2,3,4,5,6]
```

![](img/e31b98817c21de4ec6fc39615bb303f7.png)

照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Python 中的高阶函数

这些函数接受一个 iterable(元组、列表等)。)作为参数，并返回另一个 iterable 作为输出。

我们来说说 Python 中的三个高阶函数，把它们分解一下。

# 地图功能

`map`函数接受一个 iterable 并在应用给定函数后返回一个 map 对象(另一个 iterable)。

## 声明式编程方法

让我们使用 Python 中的`map`函数返回列表`nums`中数字的立方。

```
nums = [1,2,3,4]cube_nums = map(lambda x : x*x*x, nums)print(list(cube_nums)) 
```

## 命令式编程方法

在命令式方法中，这可以按如下方式完成:

```
nums = [1,2,3,4]def map_function(function, iter_list):
    lst = []    
    for i in iter_list:
        lst.append(function(i))
    return lstcube_nums = map_function(lambda x : x*x*x, nums)print(cube_nums)
```

![](img/337eec8711450d3ca6cf8bf46d241def.png)

[Marjan Blan | @marjanblan](https://unsplash.com/@marjan_blan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 减少功能

`reduce`函数将作为 iterable，并递归地处理它的内容，将它们缩减为一个值。

## 声明式编程方法

让我们使用 Python 中的`reduce`函数返回 list `nums`中所有数字的总和。

要使用该功能，必须从`functools`库中导入。

```
from functools import reducenums = [1,2,3,4]sum = reduce(lambda x,y : x+y, nums)print(sum)
```

## 命令式编程方法

在命令式方法中，这可以按如下方式完成:

```
nums = [1,2,3,4]sum = 0for i in nums:
    sum += iprint(sum)
```

![](img/b93c64d6840c05a963907a72ed053d39.png)

由[伊利亚·巴甫洛夫](https://unsplash.com/@ilyapavlov?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 过滤功能

`filter`函数接受一个 iterable，并根据给定的函数对其进行过滤。

## 声明式编程方法

让我们使用`filter`函数来过滤列表`nums`，以包含值大于 50 的元素。

```
nums = [1,30, 59, 67, 64, 73, 2]filtered_list = filter(lambda x: x > 50, nums)print(list(filtered_list))
```

## 命令式编程方法

在命令式方法中，这可以按如下方式完成:

```
nums = [1,30, 59, 67, 64, 73, 2]def filter_function(function, lst):
    new_lst = []
    for i in lst:
        if function(i):
            new_lst.append(i)
    return new_lstfiltered_list = filter_function(lambda x: x > 50, nums)print(filtered_list)
```

![](img/6863ad03868557ec7f3a2a8a8074176f.png)

[纳吉布·卡利尔](https://unsplash.com/@nkalil?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我希望这使声明式和命令式编程对您来说更容易。

干杯！

*感谢您阅读本文！*

*如果你是 Python 或编程的新手，可以看看我的新书，书名为“* [**”《没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book)**”***下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)