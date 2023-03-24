# 重构 010 —提取方法对象

> 原文：<https://levelup.gitconnected.com/refactoring-010-extract-method-object-61506f8a2a67>

## *你有一个大的算法方法。让我们打破它。*

![](img/c380c93b5417d12fd311628bca931130.png)

> *TL；DR:长方法不好。移动它们并打破它们。*

# 解决的问题

*   缺乏可测试性
*   偶然复杂性
*   [测试私有方法](https://mcsee.medium.com/ef812f521a36)

# 相关代码气味

[](https://blog.devgenius.io/code-smell-10-too-many-arguments-b44064610789) [## 代码味道 10 —参数太多

### 对象或函数需要太多的参数才能工作

blog.devgenius.io](https://blog.devgenius.io/code-smell-10-too-many-arguments-b44064610789) [](https://blog.devgenius.io/code-smell-21-anonymous-functions-abusers-e6f1e3db6a3f) [## 代码气味 21 —匿名函数滥用者

### 函数，lambdas，闭包。所以高阶，非声明，和热。

blog.devgenius.io](https://blog.devgenius.io/code-smell-21-anonymous-functions-abusers-e6f1e3db6a3f) [](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) [## 代码气味 36 — Switch/case/elseif/else/if 语句

### 第一节编程课:控制结构。高级开发人员的教训:避免它们。

blog.devgenius.io](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) [](https://blog.devgenius.io/code-smell-112-testing-private-methods-ef812f521a36) [## 代码气味 112 —测试私有方法

### 如果你从事单元测试，你迟早会面临这个困境

blog.devgenius.io](https://blog.devgenius.io/code-smell-112-testing-private-methods-ef812f521a36) [](https://blog.devgenius.io/code-smell-03-functions-are-too-long-accea7eb4ae9) [## 代码气味 03 —函数太长

### 人类过了 10 线就烦了。

blog.devgenius.io](https://blog.devgenius.io/code-smell-03-functions-are-too-long-accea7eb4ae9) [](https://blog.devgenius.io/code-smell-112-testing-private-methods-ef812f521a36) [## 代码气味 112 —测试私有方法

### 如果你从事单元测试，你迟早会面临这个困境

blog.devgenius.io](https://blog.devgenius.io/code-smell-112-testing-private-methods-ef812f521a36) 

# 步伐

1.  创建一个对象来表示方法的调用
2.  将大方法移动到新对象
3.  将方法的临时变量转换为私有属性。
4.  使用[提取方法](https://blog.devgenius.io/refactoring-002-extract-method-1478f421b35a)打破新对象中的大方法
5.  通过将参数转换为私有属性，从方法调用中移除参数

# 示例代码

## 以前

```
class BlockchainAccount {
  // ...
  public double balance() {
    string address;    
    // Very long untestable method
  }
}
```

## 在...之后

```
class BlockchainAccount {
  // ...
  public double balance() {
    return new BalanceCalculator(this).netValue();
  }
}// 1\. Create an object to represent an invocation of the method
// 2\. Move the big method to the new object
// 3\. Convert the temporary variables of the method into private attributes.
// 4\. Break the big method in the new object by using The Extract Method
// 5\. Remove parameters from method invocation by also converting them to private attributes class BalanceCalculator {
  private string address;
  private BlockchainAccount account; public BalanceCalculator(BlockchainAccount account) {
    this.account = account;
  } public double netValue() {
    this.findStartingBlock();
    //...
    this computeTransactions();
  }
}
```

# 类型

[X]半自动

一些 ide 拥有将函数提取到方法对象中的工具。

# 安全

这是一个语法和结构的重构。

我们可以以一种安全的方式自动进行更改。

# 为什么代码更好？

我们将逻辑提取到一个新的组件中。

我们可以对它进行单元测试、重用、交换等等。

# 标签

*   膨胀者

# 相关重构

[](https://blog.devgenius.io/refactoring-002-extract-method-1478f421b35a) [## 重构 002 —提取方法

### 找到一些可以分组并被原子调用的代码片段。

blog.devgenius.io](https://blog.devgenius.io/refactoring-002-extract-method-1478f421b35a) 

# 请参见

[维基百科:战略模式](https://en.wikipedia.org/wiki/Strategy_pattern)

[方法对象定义](https://learning.oreilly.com/library/view/smalltalk-best-practice/9780132852098/ch03.xhtml)

[Refactoring.guru](https://refactoring.guru/es/replace-method-with-method-object)

[C2 维基](https://wiki.c2.com/?MethodObject)

# 结论

当我们使用几个提取方法在它们之间传递部分状态作为算法的一部分时，Method-Object 是合适的。

我们将这些部分计算存储在方法对象内部状态中。

方法对象机会的一个强有力的指示器是当计算与宿主方法不紧密相关时。

我们也可以用更多原子的、内聚的和可测试的方法对象具体化匿名函数。

# 信用

图片由 [Manuel de la Fuente](https://pixabay.com/users/mfuente-1590732/) 在 [Pixabay](https://pixabay.com/) 上拍摄

本文是重构系列的一部分。

[](https://mcsee.medium.com/how-to-improve-your-code-with-easy-refactorings-fe80b60e6a8e) [## 如何通过简单的重构来改进代码

### 重构对于增长和改进我们的代码是惊人的

mcsee.medium.com](https://mcsee.medium.com/how-to-improve-your-code-with-easy-refactorings-fe80b60e6a8e)