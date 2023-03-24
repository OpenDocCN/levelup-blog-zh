# 代码气味 177 —丢失小物件

> 原文：<https://levelup.gitconnected.com/code-smell-177-missing-small-objects-747833a30446>

## *我们到处都能看到小的原始数据*

![](img/5d4c972a82166fe0112106b0123aaf88.png)

> *TL；DR:别忘了给最小的模型*

# 问题

*   原始痴迷

# 解决方法

1.  在[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)中查找小对象的职责
2.  具体化它们

# 语境

自从早期计算以来，我们将所有看到的数据映射到熟悉的原始数据类型:字符串、整数、集合等。

映射到日期违反了抽象和[快速失效](https://codeburst.io/fail-fast-3f3f036032b0)原则。

在 [Wordle TDD Kata](/how-to-create-a-wordle-with-tdd-in-javascript-926d7947bd90) 中，我们将 Wordle word 描述为不同于*字符串*或 *Char(5)* ，因为它们没有相同的职责。

# 示例代码

## 错误的

```
public class Person {
    private final String name;     public Person(String name) {
        this.name = name;
    }
}
```

## 对吧

```
public class Name {
    private final String name;     public Name(String name) {
        this.name = name;
        // Name has its own creation rules, comparison etc.
        // Might be different than a string
    }
}public class Person {
    private final Name name;     public Person(Name name) {
        // name is created as a valid one,
        // we don't need to add validations here 
        this.name = name;
    }
}
```

# 侦查

[X]手册

这是一种语义气味。它与设计活动有关

# 例外

在极少数任务关键型系统中，我们需要从抽象到性能进行权衡。

这不是通常的情况。我们不依赖现代计算机和虚拟机优化来进行过早优化。

像往常一样，我们需要坚持在现实世界中的证据。

# 标签

*   原始的

# 结论

寻找小物件是一项非常艰巨的任务，需要经验来做好工作并避免过度设计。

在选择如何以及何时绘制地图方面，没有什么灵丹妙药。

# 关系

[](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) [## 代码气味 122——原始痴迷

### 物品就在那里等待挑选。即使是最小的。

mcsee.medium.com](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) 

# 更多信息

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) [](/how-to-create-a-wordle-with-tdd-in-javascript-926d7947bd90) [## 如何在 Javascript 中用 TDD 创建一个 Wordle

### 我们不断练习这个惊人的形并学习。可以按照步骤来！

levelup.gitconnected.com](/how-to-create-a-wordle-with-tdd-in-javascript-926d7947bd90) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

肖恩·奥尔登多夫在 [Unsplash](https://unsplash.com/s/photos/magnifying-glass) 上拍摄的照片

> 构建大型应用的秘诀是永远不要构建大型应用。将你的应用分成小块。然后，将这些可测试的小部分组装到您的大应用程序中。

贾斯汀·梅耶

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)