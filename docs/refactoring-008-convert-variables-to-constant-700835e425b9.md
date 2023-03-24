# 重构 008 —将变量转换为常量

> 原文：<https://levelup.gitconnected.com/refactoring-008-convert-variables-to-constant-700835e425b9>

## 如果我看到一个不变的变量。我称这个变量为常数

![](img/85ab168e739588b7bf53865c96bcd940.png)

> *TL；DR:明确哪些发生了变异，哪些没有。*

# 解决的问题

*   [易变性](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)
*   代码优化

# 相关代码气味

[](/code-smell-158-variables-not-variable-2d7ccae484e5) [## 代码气味 158 —变量不是变量

### 你给一个变量赋值并使用它，但从不改变它

levelup.gitconnected.com](/code-smell-158-variables-not-variable-2d7ccae484e5) [](https://mcsee.medium.com/code-smell-127-mutable-constants-a43e26c60d5b) [## 代码气味 127 —可变常量

### 你声明一个常数。但是你可以让它变异。

mcsee.medium.com](https://mcsee.medium.com/code-smell-127-mutable-constants-a43e26c60d5b) [](https://mcsee.medium.com/code-smell-116-variables-declared-with-var-1c9ecf42bb9f) [## 代码味道 116 —用“var”声明的变量

### Var，Let，Const:都一样，不是吗？

mcsee.medium.com](https://mcsee.medium.com/code-smell-116-variables-declared-with-var-1c9ecf42bb9f) 

# 步伐

1.  找到变量的范围
2.  定义一个具有相同范围的常数
3.  替换变量

# 示例代码

## 以前

```
let lightSpeed = 300000;
var gravity = 9.8;// 1\. Find the scope of the variable
// 2\. Define a constant with the same scope
// 3\. Replace the variable
```

## 在...之后

```
const lightSpeed = 300000;
const gravity = 9.8;// 1\. Find the scope of the variable
// 2\. Define a constant with the same scope
// 3\. Replace the variable // If the object is compound, 
// we might need Object.freeze(gravity);
```

# 类型

[X]自动

我们的 ide 可以检查一个变量是否被写了，但是从来没有更新过。

# 安全

这是一个安全的重构。

# 为什么代码更好？

代码更紧凑，更具声明性。

我们可以进一步使用像*var**let**const*等运算符。

范围更加明确。

# 标签

*   易变性

# 相关重构

[](https://mcsee.medium.com/refactoring-003-extract-constant-cd15db177983) [## 重构 003 —提取常数

### 你需要用一些值来解释它们的意义和来源

mcsee.medium.com](https://mcsee.medium.com/refactoring-003-extract-constant-cd15db177983) 

# 请参见

[](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) [## 变种人的邪恶力量

### 变异就是进化。它是由查尔斯·达尔文爵士提出的，我们在软件行业中使用它。但是有些事情是…

codeburst.io](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) 

本文是重构系列的一部分。