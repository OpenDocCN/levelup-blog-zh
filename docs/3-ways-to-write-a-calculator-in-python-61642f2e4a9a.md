# 用 Python 编写计算器的 3 种方法

> 原文：<https://levelup.gitconnected.com/3-ways-to-write-a-calculator-in-python-61642f2e4a9a>

Python 计算器:好的，坏的。和丑陋的

![](img/649c5a358549ddff5b742ab41c43959b.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[stellweb](https://unsplash.com/@stellrweb?utm_source=medium&utm_medium=referral)拍摄的照片

众所周知，根据 Python 的[道](https://www.python.org/dev/peps/pep-0020/)，对于任何给定的任务:

> 应该有一种——最好只有一种——显而易见的方法来做这件事

然而，在编写一个简单的计算器程序的情况下，有许多不同的方法可以做到。哪一个是“最好的”，很大程度上取决于上下文。

在本文中，我将讨论三种常见的方法，包括每种方法的优缺点。

## 解决方案 1:条件分支

我想如果有一个“明显”的方法来用 Python 构建计算器，那就是使用条件分支。

基本步骤是:

1.  要求用户输入
2.  根据所选的运算符有条件地选择一个操作
3.  使用选定的运算符执行计算
4.  打印结果

代码看起来会像这样:

这种方法没有错，事实上大多数教程都选择了这种方法。但是缺点包括:

*   仅支持[二元运算](https://en.wikipedia.org/wiki/Binary_operation)
*   对于一个简单的任务来说相当多的代码

可能会有更好的解决方案，这取决于你想要达到的目标。

[这里的](https://repl.it/@jamiebullock/calculator1)是工作代码的链接。

## 解决方案 2: eval()

另一种方法是使用 Python 内置的`eval()`函数。`eval`允许将任何字符串作为 Python 表达式进行计算。这非常强大，因为它允许在运行时动态生成和执行代码。

利用这一点，上述代码可以简化为:

```
calc = input("Type calculation:\n")

print("Answer: " + str(eval(calc)))
```

`eval()`与解决方案 1 相比有一些优势:

*   它可以接受任意表达式(例如`1+2-3*4`，而不仅仅是[二元运算](https://en.wikipedia.org/wiki/Binary_operation)
*   代码明显更少

这里的是工作代码的链接。

如果你只是在自己的电脑上本地使用它，这可能是“一个显而易见的方法”,因为它非常方便和强大。但是，在生产环境中应该非常小心地使用它。原因是`eval`可以执行任意 python 表达式，因此可能被用于恶意目的。

一篇关于`eval`安全风险的好文章可以在这里找到[。](https://nedbatchelder.com/blog/201206/eval_really_is_dangerous.html)

## 解决方案 3:字典查找和递归

在此版本的 Python 计算器中，与解决方案 1 相比，我们将进行以下增强:

*   字典查找用于排除条件分支。这使得代码更干净，可读性更强，可伸缩性更好
*   Python 的`operator`模块被用来代替定义我们自己的数学函数
*   递归用于支持复杂的数学表达式(不仅仅是二元运算)

生成的代码如下:

这种方法比解决方案 1 更强大，比解决方案 2 更安全。

其工作方式是，当用户传入一个字符串(例如 1+2*3)时，我们的`calculate()`函数遍历我们在`operators`字典中定义的每个操作符，并在该操作符第一次出现时调用字符串[分区方法](https://www.programiz.com/python-programming/methods/string/partition)。在`1+2*3`的情况下，这会给我们:

```
left = '1'
operator = '+'
right = '2*3'
```

然后在子表达式上递归调用`calculate()`，直到`s.isdigit()`返回 true。所以在上面的例子中，我们的递归调用应该是这样的:

```
return operators['+'](calculate('1'), calculate('2*3'))
```

其解析为:

```
return add(1, 6)
```

如果我用 Python 写一个“真实世界”的计算器，我可能会以这样的东西作为起点。

[这里的](https://repl.it/@jamiebullock/calculator3)是工作代码的链接。

## 额外解决方案:e 模块！

最后一个“奖励”方法，可能也是我最喜欢的，是使用优秀的 e 模块。这样，我们可以从命令行*直接计算 Python 表达式，而不需要*打开 Python 解释器。

要安装:

```
pip install e
```

那么为了使用，我们可以简单地写:

```
$ python -me 1 + 1
2$ python -me "10 * 5 - (6**2) + 6 / 3"
16
```

非常得心应手！

我希望这篇文章对你有用。如果你对 Pythonic 计算器的实现有任何其他想法，我很乐意在下面的评论中听到。

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## Python | Skilled.dev 中的编码面试课程

### 掌握编码面试的过程

技术开发](https://skilled.dev)