# 如何在 JavaScript 中得到一个完美的深度相等？

> 原文：<https://levelup.gitconnected.com/how-to-get-a-perfect-deep-equal-in-javascript-b849fe30e54f>

## 在反应浅相等的基础上实现几乎完美的深相等。

![](img/9f0eac740e50c6b09aa3814854b5aeb1.png)

萨尔曼·侯赛因·赛义夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 JavaScript 中，我们可以使用`==`、`===`运算符和`Object.is`方法来判断两个变量值相等。但是如果要深入比较两个变量值，它们能满足我们的需求吗？跟着我分析一下。

# ==

`==`操作符是一个松散的等式操作符。当比较两个不同类型的值时，它将首先尝试将它们转换为相同的类型，然后再进行比较。具体使用的算法是[抽象等式比较算法](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)，它的规则复杂难记，你也可以在这里查看它的简短描述[，还可以通过这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Equality#description)[链接](https://felix-kling.de/js-loose-comparison/#[1%2C2]==%221%2C2%22)互动体验它的转换过程。

它可以说是最不严谨的等式运算符，很多结果可能会让你大吃一惊。

# ===

`===`运算符是一个严格的等式运算符。它总是认为不同类型的操作数是不同的。具体使用的算法是[严格相等比较算法](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.6)，它的规则更容易记忆，你也可以在这里查看它的简短描述[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Strict_equality#description)

它是我们最常用的等式运算符。看起来很严格，但在以下情况下有瑕疵:

*   数字必须有相同的数值。`+0`和`-0`被认为是相同的值。
*   如果任一操作数为`NaN`，将返回 false。
*   如果两个操作数都是对象，那么只会判断它们的引用地址是否相同。

# 对象. is

在大多数情况下，`Object.is`方法的行为与`===`操作符相同，但在以下两种情况下，其结果与`===`操作符相反:

*   `+0`和`-0`被认为是不同的值，将返回 false。
*   `NaN`和`NaN`被认为是相同的值，将返回 true。

不代表`Object.is`比`===`运算符严格。我们要根据`Object.is`的具体特点来处理相应的使用要求。

注意，如果两个操作数是对象，那么`Object.is`的行为与`===`操作符相同，或者只确定它们的引用地址是否相同。

# 如何获得深度平等？

对于操作数都是对象的情况，我们期望 Deep Equal 给出我们想要的答案。例如，对于任何非原始对象`x`和`y`，它们具有相同的结构，但是它们本身是不同的对象，我们期望 Deep Equal 返回 true。

我在 [*中解释了 JavaScript 数据类型的特点如何在 JavaScript 中获得完美的深度拷贝？*](/use-pure-javascript-to-get-a-perfect-deep-copy-5fdc2d9e3d42) 文章发表较早。正是因为这些特点，一个完美的深度相等需要考虑很多边缘情况，其性能注定很差。，所以在 React 中，我们不是用深相等来判断前后状态是否有变化，而是浅相等。

那么我们来看看 React 中的 Shallow Equal 是如何实现的？

# 反应迟钝

我这里不改变原来的逻辑，只是去掉兼容性代码，提高可读性。它的原始文件是[这里的](https://github.com/facebook/react/blob/main/packages/shared/shallowEqual.js)，你可以查看一下进行对比。

请注意我在代码中以`P`开头的注释，我将以它为单位进行解释:

**P1:** 通过`Object.is`进行一级比较，如果相等则返回 true，具有一级过滤的作用。

P2: 确保两者都是对象，如果其中一个不是，则返回 false。

**P3:** 此时两者都是对象，但是它们的引用地址不一样。所以下一步就是循环遍历其中一个对象的 key 数组，判断这个 key 是否是另一个对象的自身属性(相对于继承它)，判断这两个对象的 key 对应的值是否可以通过`Object.is`，也就是说这里 React 并不选择递归判断性能，也就是说只比较一层。

看了 React 里的浅等，相信你有想法了。让我们在此基础上实现一个完美的深度平等。

# 在浅相等的基础上获得完美的深相等

这里的测试代码使用了在[上一篇文章](/use-pure-javascript-to-get-a-perfect-deep-copy-5fdc2d9e3d42)中实现的深度复制功能。当然，你也可以直接在 StackBlitz 上修改和测试结果，比如你可以删除代码末尾的注释来查看结果的变化。

另外，请注意我在代码中以`P`开头的注释，我将以它为单位进行解释:

**P1:** 像影相等，用`Object.is`进行一级过滤。

**P2:** 需要对`Date`和`RegExp`进行特殊处理，因此使用`Date.prototype.getTime()`表示日期以获取时间戳并进行比较，使用`RegExp.prototype.toString()`表示 RegExp 以获取字符串并进行比较。

P3: 像影子一样相等，确保两者都是对象，如果其中一个不是，则返回 false。

**P4:** 使用 WeakMap 作为哈希表解决循环引用问题。如果之前已经比较过两者，那么会返回 true，也就是说不会影响最终的结果。

**P5:** 相比于 Shadow Equal，我们升级到`Reflect.ownKeys`得到所有的键。然后我们也判断属性数组的长度，然后循环遍历`objA`的所有属性键，用`Reflect.has`判断`objB`上是否有相同的属性。最后我们把`Object.is`升级为递归处理，不断判断深度值是否相等。

如果你看看之前实现的[深度复制函数](/use-pure-javascript-to-get-a-perfect-deep-copy-5fdc2d9e3d42)，你感觉到它们之间的相似之处了吗？是的，他们在一些逻辑判断上非常相似。可以通过对比来学习。相信可以加深你对 JavaScript 数据结构的理解。

*今天就到这里。我是 Zachary，我会继续输出与 web 开发相关的故事，如果你喜欢这样的故事并想支持我，请考虑成为* [*中级会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。