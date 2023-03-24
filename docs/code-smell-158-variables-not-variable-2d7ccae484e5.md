# 代码气味 158 —变量不是变量

> 原文：<https://levelup.gitconnected.com/code-smell-158-variables-not-variable-2d7ccae484e5>

## 你给一个变量赋值并使用它，但从不改变它

![](img/91fa596ed1660708b7d9f387a9c260cc.png)

> *TL；DR:声明可变性。*

# 问题

*   可读性
*   尊重[双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)的可变性。
*   潜在的性能和内存问题。

# 解决方法

1.  将变量改为常量，并明确其范围

# 语境

我们总是从领域中学习。

有时我们猜测一个值会随着映射器的变化而变化。

后来，我们知道它不会改变。

因此，我们需要将其提升为常数。

这也将避免[魔法常数](https://medium.com/dev-genius/code-smell-02-constants-and-magic-numbers-d6c320eef90b)

# 示例代码

## 错误的

```
<?phpfunction configureUser() {
  $password = '123456';
  // Setting a password on a variable is another vulnerability
  // And Code Smell
  $user = new User($password);
  // Notice Variable doesn't change
}
```

## 对吧

```
<?phpdefine("USER_PASSWORD", '123456')function configureUser() {  
  $user = new User(USER_PASSWORD);
}// or function configureUser() {  
  $user = new User(userPassword());
}function userPassword() : string {
  return '123456';
}// Case is oversimplification as usual
```

# 侦查

[X]自动

许多 linters 检查变量是否只有一个赋值。

我们还可以执行突变测试，并尝试修改变量，看看测试是否会中断。

# 标签

*   易变性

# 结论

当变量范围清晰，我们了解了更多关于它的属性和[可变性](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)时，我们必须挑战自己并重构。

# 关系

[](https://mcsee.medium.com/code-smell-127-mutable-constants-a43e26c60d5b) [## 代码气味 127 —可变常量

### 你声明一个常数。但是你可以让它变异。

mcsee.medium.com](https://mcsee.medium.com/code-smell-127-mutable-constants-a43e26c60d5b) [](https://blog.devgenius.io/code-smell-107-variables-reuse-222a15b1a819) [## 代码味道 107 —变量重用

### 重用变量使得作用域和边界更难遵循

blog.devgenius.io](https://blog.devgenius.io/code-smell-107-variables-reuse-222a15b1a819) [](https://blog.devgenius.io/code-smell-02-constants-and-magic-numbers-d6c320eef90b) [## 代码气味 02——常数和幻数

### 一种方法用大量的数字进行计算，而不描述它们的语义。

blog.devgenius.io](https://blog.devgenius.io/code-smell-02-constants-and-magic-numbers-d6c320eef90b) 

# 重构

[](https://mcsee.medium.com/refactoring-003-extract-constant-cd15db177983) [## 重构 003 —提取常数

### 你需要用一些值来解释它们的意义和来源

mcsee.medium.com](https://mcsee.medium.com/refactoring-003-extract-constant-cd15db177983) 

# 更多信息

[](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) [## 变种人的邪恶力量

### 变异就是进化。它是由查尔斯·达尔文爵士提出的，我们在软件行业中使用它。但是有些事情是…

codeburst.io](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[诺亚·布舍尔](https://unsplash.com/@noahbuscher)在 [Unsplash](https://unsplash.com/s/photos/tied) 上拍摄

> 一个有效的复杂系统总是被发现是从一个有效的简单系统进化而来的。

约翰·高尔

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)