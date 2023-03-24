# 代码气味 176 —本质上的变化

> 原文：<https://levelup.gitconnected.com/code-smell-176-changes-in-essence-433898f2e77c>

## *突变是好事。世事变迁*

![](img/cc458b84b1f03f30ec3400371db7e3f6.png)

> *TL；博士:不要改变本质属性或行为*

# 问题

*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)违例
*   [可变性](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)
*   [涟漪效应](https://blog.devgenius.io/code-smell-16-ripple-effect-c5a2db81f37e)

# 解决方法

1.  保护基本属性不被改变。
2.  [移除设定器](https://blog.devgenius.io/refactoring-001-remove-setters-e5acdf60462c)

# 重构

[](https://blog.devgenius.io/refactoring-001-remove-setters-e5acdf60462c) [## 重构 001 —移除设置器

### Setters 违反了不变性并增加了意外耦合

blog.devgenius.io](https://blog.devgenius.io/refactoring-001-remove-setters-e5acdf60462c) 

# 语境

赫拉克利特说:

> 没有人会两次踏入同一条河。因为这不是同一条河，他也不是同一个人。”

这个人本质上保持不变。但是他的身体在进化。

# 示例代码

## 错误的

```
const date = new Date();
date.setMonth(4);
```

## 对吧

```
const date = new Date("2022-03-25");
```

# 侦查

[X]手册

这是一种语义气味。我们需要建模哪些属性/行为是必要的，哪些是偶然的。

# 标签

*   [可变性](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)

# 结论

我们需要偏爱不可变的对象。

对象可以以偶然的方式变异，而不是本质的方式。

# 关系

[](https://blog.devgenius.io/code-smell-16-ripple-effect-c5a2db81f37e) [## 代码气味 16 —连锁反应

### 微小的变化会产生意想不到的问题。

blog.devgenius.io](https://blog.devgenius.io/code-smell-16-ripple-effect-c5a2db81f37e) 

# 更多信息

[](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) [## 变种人的邪恶力量

### 变异就是进化。它是由查尔斯·达尔文爵士提出的，我们在软件行业中使用它。但是有些事情是…

codeburst.io](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e) 

# 放弃

代码气味只是我的观点。

# 信用

照片由[缺口缺口](https://unsplash.com/@jannerboy62)在[防溅板](https://unsplash.com/s/photos/heart-arrow)上拍摄

> 软件设计的变化最终意味着“前进一步，后退两步”。这是必然的。

萨尔曼·阿尔沙德

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)