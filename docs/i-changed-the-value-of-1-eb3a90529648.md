# 我改变了 1 的值

> 原文：<https://levelup.gitconnected.com/i-changed-the-value-of-1-eb3a90529648>

## 打破 Python

## 彻底摧毁 Python 的最有趣的方法

![](img/5b8403c88bb4e8edfdc7450107b7fa1c.png)

照片由[穆罕默德·诺哈西](https://unsplash.com/@coopery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fire?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我在为上一篇文章做研究时，看到了 Python 文档中一段非常有趣的引文:

> 当前的实现为`-5`和`256`之间的所有整数保留了一个 integer 对象数组，当你在这个范围内创建一个 int 时，你实际上只是得到了一个对现有对象的引用。所以应该可以改变`1`的值。我怀疑 Python 在这种情况下的行为是不确定的。:-)

Python 中的一切都是对象——包括数字。这很重要，因为数字`-5...256`是在运行时分配的，访问它们会返回对对象的引用，因此允许您永久更改这些数字的值(在 Python 实例中)。现在这有多少实际用途，我真的不知道，但它肯定是有趣的。

# 热身

你需要一些基本的 C 语言知识和`ctypes`库。让我们从改变一个相对不用的数字的值开始，比如说 31:

```
>>> import ctypes
>>> def changeNum(oldNum, newNum):
...     ctypes.cast(id(oldNum), ctypes.POINTER(ctypes.c_int))[6] = newNum

>>> changeNum(31, 100) # changes 31 to 100
>>> 31
100
```

让我们尝试一些基本的算术:

```
>>> 31 + 31
200
>>> 31 ** 0.5
10.0
>>> 31 ** 2
10000
```

这种输出让我存在不舒服，如果你也有这种感觉，我对即将发生的事情感到抱歉。让我们现在真正挑战一下:

```
>>> 31 == 100
True
>>> changeNum(100, 200)
>>> 31
100
>>> 100
200
>>> 31 == 100
False
>>> 31 == 200
False
>>> 31 * 2 == 200
True
>>> 31 * 2 == 100
True
```

一旦你改变了价值，它就没了。您可以尝试恢复原始值，但您已经删除了它。

```
>>> changeNum(100, 500 // 5)
>>> 100
200
>>> changeNum(100, 50 * 2)
>>> 100
200
```

# 绝对的混乱

如果你还没有意识到，对象本身是全局改变的。这意味着任何与该数字的交互都是“未定义的”。让我们看看`for`循环中的行为是如何“定义”的:

```
>>> changeNum(5, 100)
>>> for i in range(5):
...     print(i)
...
0
1
2
...
99
```

挺标准的；这是一个不一致的数字系统所能达到的标准。更奇怪的是:如果我改变`5`的值，`5`在技术上不再存在。这可能导致基本操作中非常奇怪的交互:

```
>>> changeNum(5, 20)
>>> 5 - 7 == 13
True
>>> 5 - 7 - 8 == 5
True
```

如果你真的想伤害你的大脑，那就再弄乱几个数字，做些数学计算:

```
>>> changeNum(29, 100)
>>> changeNum(5, 20)
>>> changeNum(120, 200)
>>> 5 + 9
100
>>> 5 + 9 + 5
200
>>> 5 + 9 + 5 + 5
220
>>> (5 + 9) * 5
2000 
```

你也可以做一个非常混乱的无限循环:

```
>>> while 5 // 4 == 5:
...     pass
...     # Do loop stuff
```

# 崩溃的 Python

现在让我们去抓大鱼；我们已经处理了其他不太重要的数字，但是如果我们改变`1`会发生什么？

```
>>> changeNum(1, 2)
>>> 1
Segmentation fault (core dumped)
```

至少可以说，不为所动

它崩溃了。这并不奇怪，因为在非常重要的计算中,`1`是一个非常重要的数字。我不确定改变`1`是否会影响`True`，但如果会，我不敢想象后果。

# 最后的话

Python 很神奇，即使这些交互看起来很痛苦，我仍然觉得它们很酷，我希望你也一样。你可以用这个来捉弄朋友或同事，让他们发疯，因为他们试图找出为什么`5`不是`5`。

如果没有这篇 [Reddit 帖子](https://www.reddit.com/r/Python/comments/2441cv/can_you_change_the_value_of_1/)和令人惊叹的 [GitHub 库](https://github.com/satwikkansal/wtfpython)，这篇文章是不可能完成的，我强烈建议去看看它们。

谢谢你陪我度过这段时间。