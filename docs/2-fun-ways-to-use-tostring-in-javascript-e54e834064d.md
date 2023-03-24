# 在 JavaScript 中使用“toString()”的两种有趣方式

> 原文：<https://levelup.gitconnected.com/2-fun-ways-to-use-tostring-in-javascript-e54e834064d>

![](img/987a11b236f444c335fc191709ae5d96.png)

如果你是 JavaScript 新手，这篇文章将有助于你熟悉它最常用的方法之一——`toString()`！如果没有，那么这可能是一个很好的复习，让您了解使用这种方法的替代方法。理解`toString()`的这两个有趣的用例可以帮助你节省时间和编写更少的代码。

# `toString()`法的定义

在 JavaScript 中，方法`toString()`是`Object`类的原型方法，它返回被调用对象类型的字符串表示。[因为 JS 中的几乎所有东西都是一个对象的后代](https://towardsdatascience.com/everything-about-javascript-object-part-1-854025d71fea)，所以它们可以用一个字符串来表示`toString()`:

`toString()`函数是使用最广泛的内置 JS 函数之一，因为它可以灵活处理多种数据类型。在本文中，我们将探索一些不太常见的`toString()`用例，它们将把你的编程带到下一个层次。

# 用例 1:将整数从十进制改为二进制

当要对整数执行字符串转换时，可以将一个可选的`radix`参数传递给`toString()`来将基数为 10 的整数转换为其他基数。每个以 10 为基数的整数都有一个以 2 为基数的等价二进制数*。虽然有很多其他方法可以将十进制整数转换成二进制，但是当您将 2 作为`radix`传入时，`toString()`可以使这项任务无缝完成。*

```
let testNum = 42 
let binaryTestNumStr = testNum.toString(2) console.log(binaryTestNumStr) // "101010"
```

只要与`toString()`一起使用的数据类型是一个整数，当你传入 2 时，你将得到一个二进制形式的整数的字符串表示。

# 用例 2:将颜色从 RGB 转换成十六进制

`toString()`功能也可用于将 RGB 值改为十六进制。

在 web 开发中，程序员使用 CSS 为他们的网站添加样式，包括颜色。在 CSS 中表示颜色的一种常见方式是使用 *RGB 值*。RGB 是三个数字的组合，范围从 0 到 255，分别代表红色、绿色和蓝色的光谱。

另一种常见的写网页颜色值的方法是用十六进制的*值。它们与 RGB 相似，在技术上由三个值组成。然而，不同之处在于十六进制值的每个组成部分的长度总是 2 个字符。有了`toString()`，我们可以通过传入 16 作为我们的`radix`值，将一个单独的 RGB 值转换成它的 16 进制等效值:*

```
// our RGB values
let rValue = 240 
let gValue = 133 
let bValue = 79 // converted hex values with toString()
rValue = rValue.toString(16) 
gValue = gValue.toString(16) 
bValue = bValue.toString(16) let hexValue = rValue + gValue + bValue console.log(hexValue) // "f0854f"
```

当您将 16 传递给`toString()`时，它将期望要被字符串化的值是一个整数。它接受整数并将其转换为 2 个字符的值。

用`toString()`转换成 16 进制时，有一些重要的东西需要指出。为了正确转换 RGB 值，它必须介于 0 和 255 之间。否则，该函数将返回令人困惑的结果。

# 总结

这篇文章并不打算让你远离学习这些转换是如何在算法上工作的(你应该！).它是关于演示一个流行的 JavaScript 函数的一些有趣的用例。在一天结束时,`toString()`函数返回的只是一个字符串，表示它所调用的类似对象的值。

感谢你的阅读，我希望你觉得这是有趣的，也许有用。

继续编码。

# 参考

[Deepak Gupta 关于 JavaScript 对象的一切→(中型文章)](https://towardsdatascience.com/everything-about-javascript-object-part-1-854025d71fea)

[object . prototype . tostiring()→(MDN docs)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)

*原载于*[*https://blog.mydevdiary.net*](https://blog.mydevdiary.net/2-fun-ways-to-use-tostring-in-javascript-ckoheqebh0cwkqcs19euzcusm)*。*