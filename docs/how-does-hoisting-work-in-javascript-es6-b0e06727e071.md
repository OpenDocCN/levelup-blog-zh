# Javascript ES6 中的提升是如何工作的？

> 原文：<https://levelup.gitconnected.com/how-does-hoisting-work-in-javascript-es6-b0e06727e071>

![](img/609344d9fe0a9437cfea4a30e5da796a.png)

[Guillaume TECHER](https://unsplash.com/@guillaume_t?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/crane?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

让我困惑了一段时间的是`const`、`let`和`var`在提升时的行为方式是否相同。如果你不熟悉什么是提升，我也将在本文中讨论它！

# **范围界定**

在进入细节之前，我们先来谈谈 Javascript 中变量的作用域。范围定义了变量的可见性。在 Javascript 中，变量可以用以下方式声明:

1.  全局范围—变量在函数或{ }之外声明

可以在函数和{}内部看到全局范围变量

2.局部范围—变量在函数或{ }内部声明

局部范围变量只能在其函数或{}中看到

值得注意的是，在局部作用域中，可以有块作用域或函数作用域。

# **吊装**

Javascript 中的提升是指 Javascript 将变量声明( ***而非*** 定义)移动到其全局或局部范围的顶部。这意味着`var`、`const`和`let`变量声明被解释为位于其作用域的顶部。

让我们看看下面的例子:

使用 var 的示例代码

通过提升，我们可以预期会发生的是，`a` 会被提升到其局部范围的顶部，初始值为`undefined`。下面是提升后 Javascript 将如何解释上面的例子:

使用提升的 var 示例代码解释

正如你所看到的，提升允许`var`声明发生在它的调用之后，因为它被 Javascript 解释和初始化为在其作用域顶部的一个`undefined`变量。

# **颞侧死区**

我前面提到过，Javascript 将把`var`、`const`和`let`变量声明提升到其作用域的顶部。虽然这是真的，`const`**`let`*并不会表现得和`var` *一样。如果没有提供定义，* `var`将提升一个声明并将变量初始化为`undefined` 。但是，如果没有提供定义，`const`和`let`将提升变量声明，而**不会**初始化变量***

**为了更好地理解这一点，让我们看一下下面的代码:**

**时间死区示例**

**变量 `a`将被提升到其作用域的顶部。然而，因为`a`是使用`let`声明的，`a`将保持未初始化状态。因此，当您试图执行`a`时，Javascript 将抛出一个引用错误，指出`a`未初始化。**时间死区**是在 *a* 初始化之前执行的代码行。如您所见，这在下面的解释代码中得到了演示:**

**因此，尽管`let`和`const`仍然提升与`var`声明相同的内容，但它们的行为却不同。**

# ****结论****

**我们检查了`var`、`let`和`const`变量的提升。虽然它们都是一样的，但它们的行为却不一样。希望这有助于澄清一些关于提升不同变量声明的混淆。**

# **引用**

**我使用了以下资源来帮助我理解这些主题:**

**[1].泰勒·麦金尼斯。*高级 Javascript 课程。* (2020)。[*https://tylermcginnis.com/courses/*](https://tylermcginnis.com/courses/)**

**[2].MDN Web 文档。(2020).*[*https://developer.mozilla.org/*](https://developer.mozilla.org/)***

**[3].堆栈溢出。*用 let 或 const 声明的变量在 ES6 中不吊吗？(2020).*[*https://stack overflow . com/questions/31219420/are-variables-declared-with-let-or-const-not-hung-in-es6*](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-not-hoisted-in-es6)*。***

**[4].泽尔·刘。 *JavaScript 范围和闭包*。(2020).[*https://css-tricks.com/javascript-scope-closures/*](https://css-tricks.com/javascript-scope-closures/)**

**[5].面试蛋糕。JS-范围。(2020).[*https://www.interviewcake.com/question/javascript/js-scope*](https://www.interviewcake.com/question/javascript/js-scope)**