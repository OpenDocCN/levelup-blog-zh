# 代码气味 149 —可选链接

> 原文：<https://levelup.gitconnected.com/code-smell-149-optional-chaining-b8830d7206ae>

## 我们的代码更加健壮和易读。但是我们把 NULL 藏在地毯下面

![](img/804debb7f27f6d23c2e7e17029e44204.png)

> *TL；避免空值和未定义。如果你避开它们，你就永远不需要期权。*

# 问题

*   无效的
*   [如果造成污染](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)

# 解决方法

1.  移除空值
2.  处理未定义的

# 语境

可选链接、可选件、合并和许多其他解决方案帮助我们处理臭名昭著的空值。

一旦我们的代码成熟、健壮并且没有空值，就没有必要使用它们了。

# 示例代码

## 错误的

```
const user = {
  name: 'Hacker'
};if (user?.credentials?.notExpired) {
  user.login();
}user.functionDefinedOrNot?.();// Seems compact but it is hacky and has lots
// of potential NULLs and Undefined
```

## 对吧

```
function login() {}const user = {
  name: 'Hacker',
  credentials: { expired: false }
};if (!user.credentials.expired) {
  login();
}// Also compact 
// User is a real user or a polymorphic NullUser
// Credentials are always defined.
// Can be an instance of InvalidCredentials
// Assuming we eliminated nulls from our codeif (user.functionDefinedOrNot !== undefined) {  
    functionDefinedOrNot();
}// This is also wrong.
// Explicit undefined checks are yet another code smell
```

# 侦查

[X]自动

这是一个*语言特色*。

我们可以检测到它并移除它。

# 标签

*   空

# 结论

许多开发人员觉得用空处理污染代码是安全的。

事实上，这比根本不处理空值更安全。

[Nullish 值](https://developer.mozilla.org/en-US/docs/Glossary/Nullish)，Truthy 和 Falsy 也是代码气味。

我们需要更高的目标，做出更干净的代码。

*好的*:从你的代码中删除所有的空值

*不良*:使用可选链接

*丑陋的*:根本不处理空值

# 关系

[](/code-smell-145-short-circuit-hack-606ffb351c2d) [## 代码气味 145 —短路黑客

### 不要使用布尔计算作为可读性的捷径

levelup.gitconnected.com](/code-smell-145-short-circuit-hack-606ffb351c2d) [](https://blog.devgenius.io/code-smell-12-null-64fbd7792a7c) [## 代码气味 12 —空

### 程序员使用 Null 作为不同的标志。它可以提示缺席、未定义的值、错误等。

blog.devgenius.io](https://blog.devgenius.io/code-smell-12-null-64fbd7792a7c) [](https://blog.devgenius.io/code-smell-69-big-bang-javascript-ridiculous-castings-870f20469322) [## 代码气味 69 —大爆炸(JavaScript 荒谬的铸件)

blog.devgenius.io](https://blog.devgenius.io/code-smell-69-big-bang-javascript-ridiculous-castings-870f20469322) 

# 更多信息

[可选链接参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)

[](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0) [## Null:十亿美元的错误

### 他不是我们的朋友。它不会简化生活，也不会让我们更有效率。只是更懒。是时候停止了…

codeburst.io](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0) [](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484) [## 如何永远摆脱烦人的 if

### 为什么我们学习编程的第一条指令应该是最后使用的。

blog.devgenius.io](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)  [## 泰国或高棉的佛教寺或僧院

### 这个谈话不代表任何人的实际意见。为了更严肃地对待软件，尝试销毁所有软件…

www.destroyallsoftware.com](https://www.destroyallsoftware.com/talks/wat) 

# 信用

照片由 [engin akyurt](https://unsplash.com/@enginakyurt) 在 [Unsplash](https://unsplash.com/s/photos/chains) 上拍摄

> 与怪物搏斗的人可能会小心，以免自己因此变成怪物。如果你长时间凝视深渊，深渊也在凝视你。

*尼采*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)