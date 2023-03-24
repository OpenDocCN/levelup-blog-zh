# 代码气味 135 —只与一个实现接口

> 原文：<https://levelup.gitconnected.com/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2>

## 通用并预见未来是好事。

![](img/7c10bbc8d222526a49ea5fb8bf82b67c.png)

> *TL；博士:不要过度概括*

# 问题

*   投机设计
*   复杂性
*   过度工程化

# 解决方法

1.  移除接口，直到获得更多示例

# 语境

过去，程序员告诉我们要为变化而设计。

如今，我们遵循科学的方法。

每当我们发现重复，我们删除它。

以前没有。

# 示例代码

## 错误的

```
public interface Vehicle {
    public void start();
    public void stop();
}public class Car implements Vehicle {
    public void start() {
        System.out.println("Running...");
    }
    public void stop() {
        System.out.println("Stopping...");
    }
}// No more vehicles??
```

## 对吧

```
public class Car {
    public void start() {
        System.out.println("Running...");
    }
    public void stop() {
        System.out.println("Stopping...");
    }
}// Wait until more vehicles are discovered
```

# 侦查

[X]自动

这对我们的 linters 来说非常容易，因为他们可以在编译时跟踪这个错误。

# 例外

这条规则适用于内部系统定义和业务逻辑。

一些框架将接口定义为要实现的协议。

在我们的[双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)中，我们需要对现有的真实世界协议进行建模。

接口是与协议相对应的[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)。

依赖注入协议声明用它们的实现来完成的接口。在那之前，它们可以是空的。

# 标签

*   过度设计

# 更多信息

[](https://mcsee.medium.com/code-smell-130-addressimpl-89f5209870a7) [## 代码气味 130 —地址

### 很高兴看到一个实现接口的类。更好的是理解它做什么

mcsee.medium.com](https://mcsee.medium.com/code-smell-130-addressimpl-89f5209870a7) 

# 结论

我们需要等待抽象，而不是创造性和投机性

# 信用

Brian Kostiuk 在 Unsplash 上拍摄的照片

> 我喜欢软件，因为如果你能想象一些东西，你就能创造它。

*雷·奥茨*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)