# 检查 Javascript 中是否存在对象属性

> 原文：<https://levelup.gitconnected.com/checking-for-existence-of-object-properties-in-javascript-5b94b68443da>

![](img/d1655bcc273c5295949de6ac00377d4e.png)

[克里斯托夫辊](https://unsplash.com/@krisroller?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我最近参加了一个由技术娴熟的[组织的模拟技术面试，在面试中，我与一名面试官配对，并接受了 Javascript 和 React 方面的代码挑战。](https://www.skilledinc.com/)

我对其中一个问题的部分解决方案包括检查一个对象中是否存在一个键。

我实现这一点的方法是使用以下代码:

```
if (myObj["keyName"]){  // or myObj.keyName
  // do something
}else{
  // do something else
}
```

我的解决方案很有效，但是面试官告诉我另一种检查方法是使用`myObj.hasOwnProperty(“keyName”)`。

之前没遇到过`hasOwnProperty()` 法，很想了解一下。

根据 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty):

> 方法返回一个布尔值，表明对象是否将指定的属性作为自己的属性(相对于继承它)。

使用`hasOwnProperty()`方法的好处是，如果键存在，但有一个 false 值，(如 0、空字符串、false 等)，它仍将计算为`true`，而在我的实现中，它将计算为`false`。

(对于我的代码挑战来说，这并没有什么不同，因为我的场景永远不会有 falsey 值的键，但是我仍然很高兴了解这种方法。)

面试结束后，我开始研究`hasOwnProperty()`方法，试图了解更多。在我的研究中，我遇到了第三种检查对象中是否存在键的方法，使用`in`操作符。

```
if ("keyName" in myObj){
  // do something
}
```

[MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in)解释:

> 如果指定的属性在指定的对象或其原型链中，则`**in**` **操作符**返回`true`。

这里的不同之处在于，与如果键在原型链中而不是在对象自身的属性中则返回`false`的`hasOwnProperty()`方法不同，在这种情况下，`in`操作符将返回`true`。

例如， `"keyname" in myObj`对于像`constructor`和`toString`方法这样的继承属性是真的，但是`myObj.hasOwnProperty(“constructor”)`返回`false`，因为它是继承属性而不是`myObj`自己的属性。

总而言之，我们强调了三种不同的方法来检查一个对象是否具有指定的属性以及它们行为的细微差别。我们讨论了`myObj.keyname`、`myObj.hasOwnProperty("keyname")`和`"keyname" in myObj`。有了这些知识，我们现在可以更好地处理出现的各种情况。

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [Skilled.dev 编码面试课程](https://skilled.dev/)。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)