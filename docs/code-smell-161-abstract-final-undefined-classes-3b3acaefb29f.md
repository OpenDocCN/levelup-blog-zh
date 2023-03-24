# 代码气味 161 —抽象/最终/未定义的类

> 原文：<https://levelup.gitconnected.com/code-smell-161-abstract-final-undefined-classes-3b3acaefb29f>

## *你的类是抽象的、最终的或未定义的*

![](img/fe08e1b2e3e73be70473c6f903cbd228.png)

> *TL；DR:如果你的语言有合适的工具，你的类应该是抽象的或者最终的。*

# 问题

*   [代码重用的子类化](https://mcsee.medium.com/code-smell-11-subclassification-for-code-reuse-2e2f5b564204)
*   只有一个具体子类的[类](/code-smell-136-classes-with-just-one-subclass-9406951f9e16)
*   利斯科夫替代违反
*   [溜溜球]问题

# 解决方法

1.  将所有的叶子类声明为最终的*和抽象的*。**

# *语境*

*管理层次和组合是一个好的软件设计师的主要任务。*

*保持健康的层级结构对于促进凝聚力和避免耦合至关重要。*

# *示例代码*

## *错误的*

```
*public class Vehicle
{
  // class is not a leaf. Therefore it should be abstract //an abstract method that only declares, but does not define the start 
  //functionality because each vehicle uses a different starting mechanism
  abstract void start();
}public class Car extends Vehicle
{
  // class is leaf. Therefore it should be final
}public class Motorcycle extends Vehicle
{
  // class is leaf. Therefore it should be final
}*
```

## *对吧*

```
*abstract public class Vehicle
{
  // class is not a leaf. Therefore it is be abstract    //an abstract method that only declares, but does not define the start 
  //functionality because each vehicle uses a different starting mechanism
  abstract void start();
}final public class Car extends Vehicle
{
  // class is leaf. Therefore it is final
}final public class Motorcycle extends Vehicle
{
  // class is leaf. Therefore it is final
}*
```

# *侦查*

*[X]自动*

*因为这是由静态分析强制执行的，所以我们不能用大多数可用的工具来完成。*

# *标签*

*   *子分类*

# *结论*

*我们应该回顾一下我们的类，开始将它们定义为抽象类或最终类。*

*对于两个具体的类，没有一个子类化另一个的有效情况。*

# *关系*

*[](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [## 代码味道 11 —代码重用的子类化

### 代码重用是好的。但是子类化会产生静态耦合。

blog.devgenius.io](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [](/code-smell-136-classes-with-just-one-subclass-9406951f9e16) [## 代码气味 136 —只有一个子类的类

### 通用和预见未来是好的(再次)。

levelup.gitconnected.com](/code-smell-136-classes-with-just-one-subclass-9406951f9e16) [](https://blog.devgenius.io/code-smell-37-protected-attributes-4607260edb2d) [## 代码气味 37 —受保护的属性

### 受保护的属性对于封装和控制对我们属性的访问非常有用。他们可能在警告我们…

blog.devgenius.io](https://blog.devgenius.io/code-smell-37-protected-attributes-4607260edb2d) [](https://blog.devgenius.io/code-smell-58-yo-yo-problem-1e092d3c69ff) [## 代码气味 58 —溜溜球问题

### 寻找一个具体的方法实现？来来回回，上上下下。

blog.devgenius.io](https://blog.devgenius.io/code-smell-58-yo-yo-problem-1e092d3c69ff) 

# 更多信息

[](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04) [## 耦合:唯一的软件设计问题

### 对我们软件的所有故障进行根本原因分析，会发现一个有多种伪装的单一罪魁祸首。

codeburst.io](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04) 

[深子类](http://www.laputan.org/drc.html)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[威廉·博森](https://unsplash.com/@william_bossen)在 [Unsplash](https://unsplash.com/s/photos/the-end) 拍摄

> 当最终的设计对你投入的工作量来说似乎太简单时，你就知道你完成了。

布雷迪·克拉克

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)*