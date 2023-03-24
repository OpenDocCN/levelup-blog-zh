# 代码气味 172 —默认参数值不持久

> 原文：<https://levelup.gitconnected.com/code-smell-172-default-argument-values-not-last-484d6efaee72>

## *函数签名应该被错误删除*

![](img/0699dbbdc2fb56bb305d279f7227a59f.png)

> *TL；DR:不要在强制参数前使用可选参数。事实上:根本不要使用可选参数*

# 问题

*   [不倒快原理](https://codeburst.io/fail-fast-3f3f036032b0)违反
*   可读性

# 解决方法

1.  最后移动可选参数。
2.  避免[可选参数](https://blog.devgenius.io/code-smell-19-optional-arguments-c0714855dbbb)。

# 语境

可选参数是一种代码味道。

在强制参数之前定义可选参数是错误的。

# 示例代码

## 错误的

```
<?function buildCar($color = "red", $model){...}  
// First argument with optional argumentbuildCar("Volvo")}}  
// Runtime error: Missing argument 2 in call to buildCar()
```

## 对吧

```
<?function buildCar($model, $color = "Red", ){...}buildCar("Volvo")}} 
// Works as expecteddef functionWithLastOptional(a, b, c='foo'):
    print(a)
    print(b)
    print(c)
functionWithLastOptional(1, 2)def functionWithMiddleOptional(a, b='foo', c):
    print(a)
    print(b)
    print(c)
functionWithMiddleOptional(1, 2)# SyntaxError: non-default argument follows default argument
```

# 侦查

[X]自动

许多 Linters 可以执行这个规则，因为我们可以从函数签名中得到它。

还有，很多编译器直接禁止。

# 标签

*   可读性

# 结论

定义函数时尽量严格，避免耦合。

# 关系

[](https://blog.devgenius.io/code-smell-19-optional-arguments-c0714855dbbb) [## 代码味道 19 —可选参数

### 伪装成友好的快捷方式是另一种耦合气味。

blog.devgenius.io](https://blog.devgenius.io/code-smell-19-optional-arguments-c0714855dbbb) 

# 更多信息

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) 

[声纳声源规则](https://rules.sonarsource.com/php/type/Code%20Smell/RSPEC-1788)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[马努埃尔·托雷斯·加西亚](https://unsplash.com/ja/@matoga)在 [Unsplash](https://unsplash.com/s/photos/precipicio) 上拍摄

> 程序是给人类阅读的，只是偶尔让计算机执行。

唐纳德·克努特

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)