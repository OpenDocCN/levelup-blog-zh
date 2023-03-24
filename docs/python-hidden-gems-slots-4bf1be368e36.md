# Python“隐藏”的宝石:__slots__

> 原文：<https://levelup.gitconnected.com/python-hidden-gems-slots-4bf1be368e36>

![](img/eb1a577099b6cecb2f5fcd41ee707d99.png)

Python 有很多宝石！(图片由作者提供)

# 介绍

当我还是一个年轻的开发人员时，我经常坐在我的电脑前，随意挑选一些 python 官方文档文章(我现在还在做，但是频率降低了)。这个活动是出于好奇——那里有我不知道的东西，因为通常情况下，在你的日常工作中，你多年都不会看到一些特定于语言的结构。

这也是给初级开发人员和潜在开发人员的一个提示——阅读官方文档，这是最全面的学习资源之一，根据我的经验——经常被忽略，这很奇怪，就像人类忽略明显解决方案的一般趋势一样。也许你听过这句名言: **RTFM** ，意思是:**读他妈的手册。**我必须承认，python 和许多框架(Django，FastAPI)的官方文档质量非常好，而且都在那里，你只需要花些时间去熟悉它。

我现在就不说了。下面介绍一下`__slots__`。

# __ 插槽 _ _

我不知道你怎么想，但是当我学习 python 的时候，我对所有这些以`__`开头的特殊 Python 方法感到不知所措，其中一些非常明显:`__repr__, __str__, __dict__, __setattr__, __delattr__, __getattr__`等等。关于 python 编程的大部分课程已经涵盖了基本的一些，但是还有一些是非常特定的用法，并且经常被遗忘，比如`__slots__`(至少这是我的经验，如果你有不同的经验，请在评论部分分享)。那么什么是`__slots__`？

官方文件可以在这里找到[。此外，如果你从未阅读过`datamodel`文档页面——请阅读，稍后你会感谢我的。现在的定义是:](https://docs.python.org/3/reference/datamodel.html#slots)

```
*__slots__* allow us to explicitly declare data members (like properties) and deny the creation of *__dict__* and *__weakref__* (unless explicitly declared in *__slots__* or available in a parent.)
```

和优势:

```
The space saved over using *__dict__* can be significant. Attribute lookup speed can be significantly improved as well.
```

现在，让我们看一个真实的例子:

这两个实现之间的唯一区别是`__slots__`类属性。

现在使用`Point`，您可以:

```
p = Point(x=10.1, y=20.2, z=30.3)
p.name = "To the moon"
```

使用`SlotPoint`，您将获得:

```
sp = SlotPoint(x=10.1, y=20.2, z=30.3)
sp.name = "To the moon"
# AttributeError: 'SlotPoint' object has no attribute 'name'
```

你可以想想，像一个带`__slots__`的对象本质上是静态的，你不能扩展它，你有一套固定的属性。

现在让我们检查一下我们的 python 堆。

输出将是:

```
Partition of a set of 2585566 objects. Total size = 126243057 bytes.
 Index  Count   %     Size   % Cumulative  % Kind (class / dict of class)
     0 500000  19 52000000  41  52000000  41 dict of __main__.Point
     1 1500078  58 36001872  29  88001872  70 float
     2 500000  19 24000000  19 112001872  89 __main__.Point
     3    301   0  4223856   3 116225728  92 list
     4  27401   1  2501396   2 118727124  94 str
     ...
```

对于定义了`__slots__`的点:

输出将是:

```
Partition of a set of 2085569 objects. Total size = 78243173 bytes.
 Index  Count   %     Size   % Cumulative  % Kind (class / dict of class)
     0 1500078  72 36001872  46  36001872  46 float
     1 500000  24 28000000  36  64001872  82 __main__.SlotPoint
     2    302   0  4223976   5  68225848  87 list
     3  27401   1  2501406   3  70727254  90 str
     4  18744   1  1333496   2  72060750  92 tuple
     ...
```

在这两种情况下，我都创建了 50 万个点，如您所见，对于没有使用`__slots__`的点，我们总共使用了 2 585 566 个对象和 126 243 057 个字节的总大小，对于`__slots__`点，我们使用了 2 085 569 个对象和 78 243 173 个字节。如您所见，我们确实使用了较少的资源。此外，如果您自己运行代码，您会发现在第二个示例中创建 0.5 百万个点要快得多。

现在，让我们运行实验，比较两种情况下对象属性的访问时间。

```
p1 = Point(x=10.1, y=20.2, z=30.3)
sp1 = SlotPoint(x=10.1, y=20.2, z=30.3)def test_attr_get(obj):
    def inner_attr_get():
        obj.x
    return inner_attr_getno_slots_repeat = timeit.repeat(test_attr_get(p1))
slots_repeat = timeit.repeat(test_attr_get(sp1))def avg(l):
    return sum(l) / len(l)print(f"NO SLOTS: {avg(no_slots_repeat)}")
print(f"SLOTS: {avg(slots_repeat)}")
```

如果你运行上面的代码，你会得到这样的结果:

```
NO SLOTS: 0.04596721079942654
SLOTS: 0.047078945599787404
```

看起来对于简单的 point 类来说，只访问属性并没有太大的好处，但是让我们稍微修改一下测试方法:

```
def test_attr_get(obj):
    def inner_attr_get():
        obj.x = 90.75
    return inner_attr_get
```

现在输出将是:

```
NO SLOTS: 0.05043860499972652
SLOTS: 0.049037123199741475
```

在这种情况下——仅属性分配，插槽通常在我的 PC 上快 3–5%左右。

现在，让我们运行类似于`full_test`的对象实例创建和一些简单的逻辑:

```
def full_test(point_class):
    def inner_full_test():
        p1 = point_class(x=10.1, y=20.2, z=30.3)
        p2 = point_class(x=15.5, y=16.8, z=1.3)
        distance = calculate_distance(p1, p2)
        return distance
    return inner_full_testno_slots_repeat = timeit.repeat(full_test(Point))
slots_repeat = timeit.repeat(full_test(SlotPoint))# copy avg method from above example;print(f"NO SLOTS: {avg(no_slots_repeat)}")
print(f"SLOTS: {avg(slots_repeat)}")
```

在这种情况下，输出如下所示:

```
NO SLOTS: 0.7609337183999741
SLOTS: 0.71700722400019
```

在这种情况下，`timeit`模块的输出明显不同，我认为这足以证明带有`__slots__`的对象比标准对象表现得快一点，但是您需要知道在哪里、何时以及如何使用它们。

# 摘要

好处:

*   `__slots__`会减少你的内存占用。
*   在某些情况下，代码执行起来会更快。
*   在你知道你使用了大量对象(数百万甚至更多)并且你知道对象不需要动态的地方使用`__slots__`。
*   在大多数“正常”情况下，你永远也不会需要它，但知道这一点很好。

感谢阅读。现在，您可以在需要时使用`__slots__`节省能源和树木。