# 如何更快地写代码

> 原文：<https://levelup.gitconnected.com/how-to-write-code-faster-6ffbc8d09d31>

## 和压缩代码的快速方法

![](img/64e2b9f2d48446cb1b90f3f3c91e1d88.png)

有人在 Quora 上问了一个问题， ***你怎么写代码更快*** ，并给我发来了一个要求回答的请求。[我的回答](https://www.quora.com/How-do-I-increase-the-speed-of-writing-and-coding-like-a-good-developer/answer/Matthew-Croak-1)可能会让你震惊…

现实是……没有秘方。

> 你写得越多，就越容易，你写得越快。

本质上，这是你在学习编码时应该努力做到的…

*继续编码*即可。

把它想象成在手机上发短信或在笔记本电脑上打字。曾几何时，当你第一次知道如何发短信的时候。它花了你一点时间，因为你搜索某些字母和数字的时间更长。

而且，如果你像我一样是个老人，你可能记得必须点击手机上的按钮 ***4 次，才能使用字母 Z*** 。

![](img/18c55ea1aafaa56b5cd2005f7f1d86d4.png)

但是看看你现在。

我敢打赌，你根本不用看键盘就能发短信和打字。你做得太多了，以至于你的大脑和手指几乎自主地工作来制作短信、电子邮件，甚至像这样的博客帖子。

# 额外的优势

也就是说，在某些语言中使用最新的(和精简的)语法还有一个额外的好处 。

在这篇文章中，我将展示如何用 JavaScript 压缩代码的例子。这背后的哲学是，当你必须写更少的代码时，作为一个副作用，你会写得更快。

结合第一个原则 ***继续编码*** ，你会更好(更快)地写出已经更好(更快)的缩短语法。这两个原则将会相互结合——导致您可能成为编码速度恶魔！

![](img/34288aaade82b33f49e4d3610515631f.png)

这里有几种方法可以简化您的 JavaScript 语法。

## For 循环

不要像下面这样写你的 for 循环…

```
for (var i = 0; i < arr.length; ++i){ 
  console.log(arr[i])  
}
```

…你实际上可以写…

```
for (var i = 0; i < arr.length; ++i) console.log(arr[i])
```

对于更简单的循环逻辑，您可以去掉花括号，只将逻辑放在同一行循环语句的右括号后面。让我们看看下一个压缩代码的*方法。*

## Array.map()

当涉及到数组循环时，有时你可以使用`map`而不是创建一个`for`循环。除了`map`有时比写出一个`for`循环更有效之外，你可以进一步压缩你现有的`map`。

而不是写…

```
var newArr = oldArr.map((x)=>{ 
  return x.property  
}) 
```

…你可以写…

```
var newArr = oldArr.map(x=>x.property)
```

就像使用`for`循环一样，您可以删除花括号和新行(在本例中，还可以删除整个`return`关键字)，并将所有内容保留在一行中。你也可以去掉你的`map`论点周围的括号。

> 通过创建一个[中型合作伙伴计划账户](https://matt-croak.medium.com/membership)和[订阅我的电子邮件](https://matt-croak.medium.com/subscribe)，获取我所有的最新内容。:)

## If/Else

毫无疑问，在您的编码生涯中，您会在某个时候使用条件验证。这意味着您可能会遇到一个`if/else`语句。

而不是写…

```
var y;
if (x >= 0){
  y = x
} else {
  y = x * -1
}
```

…你可以这样写…

```
var y = x >= 0 ? x : x * -1
```

你可以完全去掉`if/else`,使用[三元运算符](https://stackoverflow.com/questions/30179850/one-line-if-else-in-javascript),将所有内容放在一行。

## 否则如果

您也可以在`else if`语句的情况下使用上面的语句。而不是写…

```
var x;

if (y === 1){
  x = 'peach'
} else if (y === 2){
  x = 'banana'
} else if (y === 3){
  x = 'pear'
} else {
  x = 'apple'
} 
```

…你可以写…

```
var x = y === 1 ? x = 'peach' :
  y === 2 ? x = 'banana' : 
  y === 3 ? x = 'pear' : 'apple'
```

基本上可以在已有三元运算的条件内 ***嵌套*** 三元运算，避免不得不写一堆`else if`语句。

更少的行，更少的代码，**花在写作上的时间更少。**

然而，在这种情况下，有些人可能会认为 `*else if*` *语句比嵌套的三元运算更具可读性——尤其是如果你有很多这样的语句的话。这是一个很好的例子，说明编写“更快”的代码不一定那么重要。注意不要过度设计你的缩写语法！*

## 未定义的处理

您可能会遇到需要处理值为`undefined`的情况。与其写这样的东西…

```
var x = “test”  

if (y) { 
  x = y 
} 
```

…你可以这样写…

```
var x = y || “test” 
```

通过使用逻辑 OR 运算符，您可以检查是否定义了`y`。如果是，则使用该值。如果不是，则使用管道右侧的值(OR 语句的第二个参数)。

## 可选链接

另一种简洁地检查`null/undefined`值的方法是使用所谓的[可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)。而不是写…

```
const dogName = adventurer.dog ? adventurer.dog.name : undefined;
console.log(dogName);
// expected output: undefined
```

…你可以写…

```
const dogName = adventurer.dog?.name;
console.log(dogName);
// expected output: undefined
```

通过使用可选的链接操作符(`?.`)，我们可以检查一个属性是否存在，并寻找那个属性的*属性，而不用破坏代码(也不用写出完整的三元运算)。*

## 无效合并

压缩代码的下一个方法是通过[无效合并](https://javascript.info/nullish-coalescing-operator)。而不是写…

```
result = (a !== null && a !== undefined) ? a : b;
```

…你可以写…

```
result = a ?? b
```

通过使用无效合并操作符(`??`)，我们不必使用逻辑与操作符(`&&`)来检查`a`是`null`还是`undefined`。这是由我们的无效结合固有地完成的。

# 摘要

总之，编写简洁的语法可以帮助你更快地编写代码，但是我认为最大的好处是经常编写代码*。你做得越多，它就会变得越自动。再加上精炼的语法，你将比以前更快(更好)地编码。*

*这些只是在 *JavaScript* 中编写精简语法的几个例子。在 JavaScript 中，还有哪些方法可以缩短语法？用另一种语言？*

*请在评论中告诉我！*

*[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。**

*你也可以通过电子邮件 *订阅，当我发布新内容时，你会收到通知！**

# *参考*

*[](https://stackoverflow.com/questions/30179850/one-line-if-else-in-javascript) [## JavaScript 中的一行 if/else

### 我有一些用开/关来切换(用 and else/if)真/假的逻辑，但我想让它更简洁一些…

stackoverflow.com](https://stackoverflow.com/questions/30179850/one-line-if-else-in-javascript) [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) [## 可选链接(？。)- JavaScript | MDN

### 那个？。运算符类似于。链接运算符，不同之处在于，如果引用为 nullish(或者…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) [](https://javascript.info/nullish-coalescing-operator) [## 无效合并运算符？?'

### nullish 合并运算符写成两个问号？？。因为它同样对待 null 和 undefined，我们将…

javascript.info](https://javascript.info/nullish-coalescing-operator)*