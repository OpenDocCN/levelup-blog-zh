# 代码气味 136 —只有一个子类的类

> 原文：<https://levelup.gitconnected.com/code-smell-136-classes-with-just-one-subclass-9406951f9e16>

## (再次)成为普通人并预见未来是件好事。

![](img/9e689e4611e5049e55380159b8f4e964.png)

> *TL；博士:不要过度概括*

# 问题

*   投机设计
*   复杂性
*   过度工程化

# 解决方法

1.  删除抽象类，直到你得到更多的例子

# 语境

过去，程序员告诉我们要为变化而设计。

如今，我们继续遵循科学的方法。

每当我们发现重复，我们删除它。

以前没有。

不是用接口，不是用类。

# 示例代码

## 错误的

```
class Boss(object):
    def __init__(self, name):
        self.name = name class GoodBoss(Boss):
    def __init__(self, name):
        super().__init__(name)# This is actually a very classification example
# Bosses should be immutable but can change their mood
# with constructive feedback
```

## 对吧

```
class Boss(object):
    def __init__(self, name):
        self.name = name # Bosses are concrete and can change mood
```

# 侦查

[X]自动

这对我们的 linters 来说非常容易，因为他们可以在编译时跟踪这个错误。

# 例外

一些框架创建了一个抽象类作为占位符来构建我们的模型。

子类化不应该是我们的第一选择。

一个更优雅的解决方案是将[声明为接口](/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2)，因为它耦合性更低。

# 标签

*   过度设计

# 关系

[](https://mcsee.medium.com/code-smell-114-empty-class-66041acd0c43) [## 代码气味 114 —空类

### 你遇到过没有行为的班级吗？上课是他们的行为。

mcsee.medium.com](https://mcsee.medium.com/code-smell-114-empty-class-66041acd0c43) [](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [## 代码味道 11 —代码重用的子类化

### 代码重用是好的。但是子类化会产生静态耦合。

blog.devgenius.io](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [](https://blog.devgenius.io/code-smell-43-concrete-classes-subclassified-7cccf1545ab5) [## 代码气味 43——具体分类

### 遗产。具体的类。重用。奇妙的混合。

blog.devgenius.io](https://blog.devgenius.io/code-smell-43-concrete-classes-subclassified-7cccf1545ab5) [](https://blog.devgenius.io/code-smell-92-isolated-subclasses-names-3b9ac28922f1) [## 代码气味 92 —独立的子类名称

### 如果你的类是全局的，使用完全限定的名字

blog.devgenius.io](https://blog.devgenius.io/code-smell-92-isolated-subclasses-names-3b9ac28922f1) [](/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2) [## 代码气味 135 —只与一个实现接口

### 通用和预见未来是好的。

levelup.gitconnected.com](/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2) 

# 结论

我们需要等待抽象，而不是创造性和投机性。

# 信用

本杰明·戴维斯在 Unsplash 上拍摄的照片

> 编写一个没有合同的类就像生产一个没有规范的工程组件(电路、VLSI(超大规模集成)芯片、桥、引擎……)。没有专业工程师会考虑这个想法。

*伯特兰·迈耶*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)