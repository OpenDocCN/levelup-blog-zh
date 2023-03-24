# JavaScript 的 isNaN()函数

> 原文：<https://levelup.gitconnected.com/the-isnan-function-for-javascript-955a424bbd36>

![](img/2b49d1e7768989a92f44cd699eebaf9f.png)

不是…猫头鹰？

在`JavaScript`中，有一个全局对象的属性叫做`[NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN)`。`NaN`是“不是数字”的缩写，更确切地说，是首字母缩略词，不要与美味的大饼混淆: [naan](https://en.wikipedia.org/wiki/Naan) 。`NaN`很少用于编写程序或脚本。在现代 web 浏览器中，它是一个不可配置和不可写的属性。当一个数学(`[Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)`)函数失败时，或者当一个函数无法解析一个数字时，它通常作为返回值被接收，例如*`parseInt()`。`NaN`古怪而不常见，但偶尔出现，需要处理。*

*在最近的一个项目中，使用`React`客户端，用户帐户包括收集的股票和相应价格的投资组合。投资组合必须包括总收入的总和。听起来很简单:从投资组合中收集所有的股票价格，并将它们加在一起。当然，事情永远不会像最初想象的那么简单。*

*当返回的用户登录到他们的帐户时，状态被加载该用户的股票，但是这些股票的价格——以及总收益——是通过获取当前市场价格的外部`API`来动态呈现的。困难在于正确地定时和调度从服务器端数据库和外部`API`获取数据的动作，同时在客户端正确地呈现组件。在这个事件链中，会调度一个动作来计算用户总收入的总和，这个动作由一个名为`setTotalEarnings`的函数来解析。*

*最好的解决方案是首先正确地对获取、承诺和组件呈现进行排序，或者精心设计`setTotalEarnings`函数以更好地控制返回值，或者更简单地在有条件呈现时处理虚假的返回值，但是，由于我是在很短的时间内为评估构建这个应用程序的，所以我向前推进并解决了及时工作所需的问题！长话短说，`setTotalEarnings`函数间歇地返回`NaN`，我在处理它以在适当的`React`组件中正确呈现时遇到了麻烦。*

*我意识到等式运算符不能可靠地确定`setTotalEarnings`的返回值是否为`NaN`，因为`NaN === NaN`的计算结果为`false`！这是`JavaScript`中离奇而独特的一幕。`NaN`是`JavaScript`中唯一不等于自身的值。有一个有用的快捷方式可以更准确地处理`NaN`:函数`isNaN()`。这很容易雇用。只需向函数传递一个参数:*

```
*isNaN(NaN) // true
isNaN({})  // true
isNaN(5)   // false*
```

*虽然这个函数足够简单，但是仍然存在一些奇怪和有问题的特例行为。例如:*

```
*isNaN(true) // false*
```

*换句话说，“难道`true` **不是**是一个数字？”，你会认为它是正确的，来评估`true`。`true`是不是**不是**一个数字，然而`isNaN()`函数返回:`false`，暗示`true`是**不是**不是一个数字，*即* `true` *是*一个数字，这是不正确的。发生这种行为是因为`isNaN()`函数打算接受数值，即`number`类型的值。当一个非数字值被传递给`isNaN()`函数时，该值首先被强制转换成一个数字，然后进行计算。关于前面的例子，参数`true`首先被强制转换为`1`的二进制表示。`1`是一个数字，**不是** `NaN`，所以函数返回`false`。*

*为了给对`NaN`值的评估增加额外的保险和可预测性，有一个对`isNaN()`函数的继承:`Number.isNaN()`方法。该方法在评估值的类型是否为`NaN`之前，额外评估被评估值的类型是否为`number`。*

*重温之前的例子:*

```
*isNaN({}) // true
Number.isNaN({}) // false*
```

*正如第一种情况所确认的，输入值`{}`不是一个数字，然而，在第二种情况下，它的计算结果为`false`，因为它属于`object`类型，而不是`number`类型，这意味着输入值不是数字，不能正确地与`NaN`进行比较。*

*`isNaN()`和`Number.isNaN()`是您可能永远都不需要使用的特殊工具，但是当有一天您需要使用时，它们是正确处理数据的便利工具。当面对一个特殊的场景时，通过一种编程语言的较少使用的能力和最大化你的分辨率总是有趣的！*

*[github.com/dangrammer](https://github.com/dangrammer)
linked.com/in/danieljromans
danromans.com*