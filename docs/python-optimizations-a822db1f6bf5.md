# Python 优化—窥视孔

> 原文：<https://levelup.gitconnected.com/python-optimizations-a822db1f6bf5>

![](img/e47d3ffbc10e52ab069d5c7e61541fd4.png)

Peephole 是 Python 在编译时通过预先计算常量表达式或转换特定数据结构来优化程序的一种方式。

## 常量表达式

优化常量表达式非常简单。Python 所做的基本上是预先计算常数。假设在你的程序中，由于某种原因，你有下面的乘法运算，

```
secondsInADay = 60*60*24
```

python 要做的是预先计算乘法，并替换掉它作为`86400`。你可能想知道为什么不直接用代码写`86400`，答案是清晰。在上面的表达式中，你可以看到，为了计算一天有多少秒，你必须用 60 秒乘以一小时的 60 分钟乘以一天的 24 小时。这样你的代码可能看起来更清晰。Python 不会在每次乘法出现时都进行计算，它只会预先计算并替换为最终值。

短序列也是预先计算的。想象你有这样的代码，

```
myTuple = (2, 4)*5      # -> (2, 4, 2, 4, 2, 4, 2, 4, 2, 4)
myString = "qwerty "*2  # -> "qwerty qwerty "
```

正如你在上面的代码中看到的，我们有两个变量，第一个是一个乘以 5 的元组，第二个是一个乘以 2 的短字符串，这个短序列将被预先计算，Python 将用注释中的值替换原始表达式。值得一提的是，Python 要在存储和计算之间做平衡。如果它预先计算长序列，程序可能会更快，但最终会使用大量内存。

为了看到这种情况，您只需打开一个 Python 控制台并编写以下代码，

```
**def** my_func():
    a = 60*60*24
    myString = (**"querty "**) * 2
    myTupple = (2, 4) *5
    myString = (**"This is a sequence with a lot of characters"**) * 100
```

一旦声明了这个函数，就可以编写下面的代码来访问在这个函数的作用域上声明的所有常量，

```
my_func.__code__.co_consts
```

输出应该如下所示，

```
>>> my_func.__code__.co_consts(None, 
86400, 
'querty querty ', 
(2, 4, 2, 4, 2, 4, 2, 4, 2, 4), 
'This is a sequence with a lot of characters', 
100)
```

正如你所看到的，在上面的输出中，Python 已经预先计算了常量值和短序列，而不是让`60*60*24`函数已经有了常量值`86400`，同样的事情也发生在元组和短字符串上，但是正如你所看到的，长序列没有预先计算，而是我们得到了两个不同的常量，`'This is a sequence with a lot of characters'`和`100`。如上所述，Python 必须在存储和计算之间取得平衡。

## 成员测试:用不可变的数据结构替换可变的数据结构

Python 在这里所做的基本上是将可变的数据结构转换成不可变的版本。*列表*被转换成*元组*和*集*到*冷冻集*。

举个例子，

```
**def** my_func(element):
    **if** element **in** [**"a"**, **"b"**, **"c"**]:
        print(element)
```

上面的代码将被转换成这样，

```
**def** my_func(element):
    **if** element **in ("a"**, **"b"**, **"c")**:
        print(element)
```

这样做只是因为访问数据结构的不可变版本比访问可变版本更快。在运行以下代码之前，您可以通过执行相同的操作来检查这一点:

```
my_func.__code__.co_consts
```

输出应该如下所示，

```
>>> my_func.__code__.co_consts(None, ('a', 'b', 'c'))
```

如您所见，该函数有一个常量值，它是声明的*列表*的不可变版本(一个*元组*)。

最后做和之前一样的事情，但是用*设定*，你会看到它将被转换成*冷冻设定*

```
**def** my_func(element):
    **if** element **in {"a"**, **"b"**, **"c"}**:
        print(element)>>> my_func.__code__.co_consts(None, frozenset({'a', 'b', 'c'}))
```

如果你对 Python 优化感兴趣，你可以看看我关于 [Python 优化(Intering)](https://medium.com/@gmotzespina/python-optimizations-216205001b83) 的文章。