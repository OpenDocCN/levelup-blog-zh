# 在 JavaScript 中查找 call()、apply()和 bind()方法

> 原文：<https://levelup.gitconnected.com/grokking-call-apply-and-bind-methods-in-javascript-392351a4be8b>

## 理解 JavaScript 中的 call()、apply()和 bind()方法。

![](img/b223714be9ede3fd9fa55dbe7bfea0cb.png)

Alex Samuels 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这些函数对每个 JavaScript 开发人员来说都非常重要，几乎在每个 JavaScript 库或框架中都使用到。查看下面的代码片段。

摘自非常受欢迎的图书馆 [Lodash](https://lodash.com/)

再看第 21 行的语句， **return func.apply(this，args.reverse())**

在本文中，我们将了解 JavaScript 的 **call()** 、 **apply()** 和 **bind()** 方法。基本上，这三种方法用于控制函数的调用。ECMAScript 3 中引入了 **call()** 和 **apply()** ，而 **bind()** 是作为 ECMAScript 5 的一部分添加的。

让我们先从一个例子来理解这些。

假设你是 X 大学的一名学生，你的教授要求你创建一个数学库，作为一项计算圆的面积的作业。

```
calcArea.area(4); // prints 50.24
```

您测试并验证其结果，它工作正常，您在截止日期前将库上传到门户网站。然后你让你的同学也来验证，你知道你的结果和他们的结果不匹配小数精度。你查了作业指导，哦不！教授要求你使用一个具有 5 位小数精度的常数 **pi** 。但是你用了 **3.14** 而不是 **3.14159** 作为圆周率的值。现在您不能重新上传库，因为截止日期已经过了。在这种情况下， **call()** 函数会救你。

# 使用 **call()或 Function.prototype.call()**

```
calcArea.area.call({pi: 3.14159}, 4)
```

如您所见，它接受了一个具有新 pi 值的新对象。现在结果将会是

```
50.26544
```

让我们分析一下这里发生了什么。如果您观察**调用()**，它接受两个参数

*   上下文(对象)
*   争论

一个上下文是一个在区域函数中替换 **this** 关键字的对象。然后参数作为函数参数传递。

调用调用

```
cylinder.volume.call({pi: 3.14159}, 2, 4);// 50.26544
```

# 使用 **apply()** 或 Function.prototype.apply()

Apply 完全相同，只是函数参数作为一个**数组**传递，或者您可以使用一个**数组对象** ( **new Array(2，4)** )。

```
cylinder.volume.apply({pi: 3.14159}, [2, 4]);// 50.26544
```

# 使用 bind()或 Function.prototype.bind()

它允许我们将上下文输入到一个函数中，该函数返回一个带有更新上下文的新函数。基本上它意味着 **bind()** 将一个**新的 this** 附加到一个给定的函数上。与 **call()** 和 **apply()** 不同， **bind()** 函数不是立即执行，而是在以后需要时执行。 **bind()** 在处理 JavaScript 事件时很有用。

# 摘要

*   所有这三个函数都有一个相似之处，它们的第一个参数总是**‘this’**值或上下文，您希望将它传递给调用该方法的函数。
*   **call()** 和 **apply()** 被**立即调用**， **bind()** 返回一个绑定的**函数，**与上下文关联，稍后**将被**调用。
*   **call()** 和 **apply()** 与**相似**唯一不同的是， **apply()** 中的**参数**作为**数组传递。**

阅读更多关于 MDN [call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) ， [apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 和 [bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 的内容

如果你在项目中使用了这些方法，请在评论中告诉我。

—阿尼基特·库代尔