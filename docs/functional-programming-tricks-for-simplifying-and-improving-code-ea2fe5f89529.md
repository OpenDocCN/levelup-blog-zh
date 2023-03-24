# 简化和改进代码的函数式编程技巧

> 原文：<https://levelup.gitconnected.com/functional-programming-tricks-for-simplifying-and-improving-code-ea2fe5f89529>

![](img/13790cdaf9c5173fa4ebe75bf1bd816c.png)

约尔格·安格利在 [Unsplash](https://unsplash.com/s/photos/ski-trick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

函数式编程可以让你的代码更简单。简单性意味着代码易于阅读和理解、可测试和可维护。

在本文中，我描述了一些函数式编程(FP)技巧，您可以通过简化代码来提高代码质量。

# 去掉临时变量

函数式编程支持不变性。这个不变性原则意味着在你初始化变量之后，你不能改变它们的值。同样，在创建了对象或字符串之后，也不能改变它们。

如果你用 JavaScript 编程，你所有的变量定义都应该使用`const`。对于任何阅读您代码的人来说，常量定义让他们安心:它保证了变量永远不会被重新赋值。因为重新分配是不可能的，所以读者的大脑没有从代码中跟踪和识别重新分配的负担。

当你*需要*改变你的价值观的时候呢？首先，你可以有一个数组:

```
const fruits = ['apple', 'orange', 'banana']
```

当你想把一种新的水果加入这个列表时，你会怎么做？解决方案是用新的或更新的值初始化新的常量变量。我们可以使用 spread 运算符复制现有的`fruits`值:

```
const allFruits = [...fruits, 'pineapple']
```

你应该在你的代码中处处遵循**不变性原则**。因为如果你这样做，你的代码会变得更干净。

您知道您的`fruits`变量的值永远不会改变，并且您在第一次看到变量的声明时就知道这个事实。初始化之后，无论你在哪里看到`fruits`变量，你都知道它仍然有相同的三个果实。

# 摆脱循环

功能代码告诉**什么**发生，而不是指示**它应该如何发生。**

循环是不起作用的，我们应该摆脱它们。

考虑以下循环:

```
var list = [];
var i = 0;
while (i < rows.length) {
    var row = rows[i];
    var message = {
        childAddress: row[1],
        action: 'switchToBackupNode2',
        role: 'edge'
    };
    list.push(message);
    i += 1;
}
```

**功能**替代摆脱了*而*循环，并使用*映射*将行处理成一个列表。

```
const list = rows.map(r => ({ 
    childAddress: r[1],
    action: 'switchToBackupNode2',
    role: 'edge'
 }))
```

除了去掉循环，这段代码还删除了临时变量`row`和`message`以及循环变量`i`。

结果更具可读性。

非常清楚。

你可以*相信*只要看一看这段代码就能工作。公平地说，要获得这种程度的信任，您需要首先理解 **map** 函数。在函数式编程中，map 函数无处不在，因此学习它是过渡到 FP 的必要步骤。

*map* 是程序员用于列表处理的函数之一。处理列表中的数据是 FP 的一大部分，您还应该学习其他的列表处理函数:最重要的是`reduce`和`filter`函数。

# 移除 ifs

删除 ifs 是简化代码的一个有用策略。有几种策略可以用来从代码中移除`if`语句，应用其中任何一种通常都会产生更清晰、更容易理解的结构。

让我们来看看去除`if`语句的一些策略。

# 三元运算符

三元运算符是我从变量赋值中去掉 if 语句的主要工具。

考虑以下情况:

```
let message;
if (person !== null) {
  message = `hello, ${person}!`
} else {
  message = 'hey there!'
}
```

上面的代码中有两个问题:

1.  我必须使用非常数变量`message`，因为使用`if`-结构，我无法在变量声明中立即给消息赋值。
2.  考虑到它只完成声明一个变量并有条件地给它赋值的简单任务，这段代码相当冗长和复杂。

使用三元运算符`?`可以在一行中完成相同的工作:

```
const message = person !== null ? `hello, ${person}!` : 'hey there!'
```

# 布尔运算符&&和||

布尔运算符`&&`和`||`为 if 语句提供了有效的替代方法。

与其这样做，

```
if (value) {
  doStuff(value)
} else {
  doStuff(1)
}
```

您可以这样做:

```
doStuff(value || 1)
```

这里的逻辑 OR 操作符`||`提供了一种向函数传递默认值的快速方法。每当您不知道或者不保证变量有值时，您可以使用相同的技巧:通过在前面加上`|| <default>`来提供默认值。

以下是如何使用 AND 运算符`&&`的示例。一、使用`if`的版本:

```
if (data) {
  sendData(data.value);
}
```

然后我们使用`&&`移除 if:

```
data && sendData(data.value)
```

这里，我们使用`&&`首先检查数据变量是否有值。如果没有这种检查，当没有值时(换句话说，值是`null`或`undefined`)，代码会崩溃。

在这里使用这些操作符时，我们依靠布尔表达式的*短路评估*。使用`&&`和`||`操作符，当表达式的第一部分为 falsy 时，JavaScript 不会计算表达式的后一部分。你可以在这篇文章中阅读更多关于[短路的内容。](https://codeburst.io/javascript-short-circuit-conditionals-bbc13ac3e9eb)

# 映射和查找

使用映射作为查找表是替换一系列 if 语句的有效方法。请考虑以下情况:

```
let language;
if (country === 'FI') {
	language = 'Finnish'
} else if (country === 'SE') {
	language = 'Swedish'
} else if (country === 'USA') {
  language = 'English (American)'
} else if (country === 'UK') {
  language = 'English (UK)'
} // etc...
```

更干净的方法是使用国家/语言对的地图。

```
const languages = Map([
  ['FI', 'Finnish'],
  ['SE', 'Swedish'],
  ['USA', 'English (American)'],
  ['UK', 'English (UK)'],
])const language = languages.get(country)
```

第二个实现要简单得多。代码立刻揭示了它的意图。作为奖励，它将`language`变量转换成使用`const`

# 小功能

函数式编程都是关于函数的。写很多小函数比写少量长函数好。将代码分解成更小的模块的主要原因是更小(更短)的部分更容易孤立地理解。较小的部分也更容易测试和维护。

使用我在这里展示的技巧，您的代码可以更加紧凑。这是一个重要的目标，也是学习这些技巧的绝佳理由，更重要的是，学习函数式编程。

像这样的招数和策略还多着呢。如果你想让我再写一篇关于其他类似策略的文章，请告诉我。

*原载于 2020 年 3 月 24 日 https://anssipiirainen.com**的* [*。*](https://anssipiirainen.com/)