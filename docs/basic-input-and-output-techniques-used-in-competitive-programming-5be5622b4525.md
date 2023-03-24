# python 竞争性编程中使用的基本输入和输出技术

> 原文：<https://levelup.gitconnected.com/basic-input-and-output-techniques-used-in-competitive-programming-5be5622b4525>

所以你开始了竞争性编程，但是即使在最简单的编程中也很难接受输入和格式化输出？没问题，你来对地方了，这篇文章将告诉你在 python 竞争性编程中最重要的输入和输出技术。

![](img/b0eda76db9ad647de3a3c2631209042b.png)

[法比安·格罗斯](https://unsplash.com/@grohsfabian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 投入

大多数问题需要你接受不同类型的输入，所以这里有一些重要的技巧

## 1.接受单一输入

如果你想只接受一个输入，那么你只需要使用`input()`函数，然后你可以把它转换成你想要的类型。比如说-

对于字符串，使用`n = input()`

对于整数，使用`n = int(input())`

对于浮点数或十进制数，使用`n=float(input())`

## 2.取已知数量的输入

如果你想获得固定数量的空格分隔的输入(例如矩阵的维数),那么你可以使用下面的方法

为弦乐`m,n = input().split()`

这将把两个空格分隔的字符串中的一个分别分配给 m 和 n。

对于整数和浮点数`m,n = map(int,input().split())`或`m,n = map(float,input().split())`

我们知道`input().split()`返回一个 iterable，所以我们所做的是使用`map()`将每个对象转换成整数，它将一个函数(在我们的例子中是`int()`)应用于 iterable 的每个对象。

如果你有两个参数，但是你不希望其中一个浪费一个变量，那么你可以用`_`代替 eg-

你想只取变量 m 和 n 中的第一个和第三个整数，在下面的例子中，你可以使用下面这段代码

```
1 2 3m,_,n = map(int,input().split())
```

## 3.接受可变数量的输入

对于数量可变的空格分隔的输入，我们通常把它们分配给一个列表，但是你也可以根据自己的需要使用 set 或 tuple。

对于字符串-

```
I love pythonl = list(input().split())
```

l 的值将是`l=['I','love','python']`

对于整数或浮点数-

```
1 2 3 4 5 6 7l = list(map(int,input().split())
```

l 的值将是`l = [1,2,3,4,5,6,7]`

如果您想要集合或元组，那么使用`tuple()`或`set()`而不是`list()`。

## 4.接受固定数量和可变数量的输入

如果你想在变量中存储起始值，在列表中存储剩余值，那么我们使用`*`考虑下面的例子

```
hello 1 2 3 4s,*l = input().split()
l = list(map(int,l))
```

我们会得到`s='hello'`和`l=[1,2,3,4]`

一个稍微复杂一点的例子是

```
hello 3 1 2 3_,n,*l = input().split()
n = int(n)
l = list(map(int,l))
```

我们会得到`n=3`和`l=[1,2,3]`。

我希望到现在为止，您已经理解了在 python 中接受任何空格分隔的输入。

# 输出

输出数据并不困难，唯一可能的情况是

## 1.在不同的行上输出

几乎 90%的问题都需要你这样输出。很简单，考虑下面的例子

```
result = [1,2,3,4,5]
for i in result:
   print(i)
```

输出将是-

```
1
2
3
4
5
```

## 2.在同一行中输出

如果您想在同一行中输出结果，那么您必须使用`print()`的`end`属性

```
result = [1,2,3,4,5]
for i in result:
   print(i, end=' ')
```

输出将是`1 2 3 4 5`

## 3.高级输出技术

一些像 Google 的 codejam 和 kickstart 这样的问题需要你在输出时提到案例号，我们使用类似 C 的技术来打印输出。

比如说-

```
result = [15,23,32]
for i in range(len(result)):
   print("Case #{}: {}".format(i+1, result[i]))
```

这将产生以下输出:

```
Case #1: 15
Case #2: 23
Case #3: 32
```

这里发生的是`{}`作为一个变量的占位符，这个变量在`format()`中提供，就像 c 语言中的`%d`。这里有更多的例子-

```
print("I love {}".format("python"))l=['python','is','the','best','language']
print("I love python because {}".format(' '.join(l)))
```

输出-

```
I love python
I love python because python is the best language
```

到目前为止，我希望您已经理解了 python 中竞争性编程中使用的所有输入和输出技术。

如果你想在 [Hackerearth](http://hackerearth.com) 和 [Hackerrank](http://hackerrank.com) 上看到一些解决的问题，那么这里有我在这两个平台上解决的一些问题的 github 库的链接(几乎所有的解决方案都有一个链接指向第一行注释掉的问题)。

[](https://github.com/prateek3255/hackerearth_python) [## prateek 3255/hackere earth _ python

### 我在 hackereaeth 上解决的问题

github.com](https://github.com/prateek3255/hackerearth_python) [](https://github.com/prateek3255/hackerrank_python) [## prateek3255/hackerrank_python

### 我在 python 中解决的关于 hackerrank 的问题

github.com](https://github.com/prateek3255/hackerrank_python) [](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)