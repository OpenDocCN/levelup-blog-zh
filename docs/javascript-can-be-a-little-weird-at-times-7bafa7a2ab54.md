# JavaScript 有时可能有点奇怪

> 原文：<https://levelup.gitconnected.com/javascript-can-be-a-little-weird-at-times-7bafa7a2ab54>

![](img/762a9d4c47827176200f548549b462ed.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Stefan Stefaník](https://unsplash.com/@cikstefan?utm_source=medium&utm_medium=referral)拍摄的照片

## 有趣而棘手的 JavaScript 代码示例列表

JavaScript 是一种很棒的语言，在过去的几年里变得越来越流行。由于其简单的语法和庞大的生态系统，对于初学者来说，这是一种很好的入门语言。

同时，我们都知道 JavaScript 是一种非常有趣的语言，有很多复杂的部分。有时我们会嘲笑所有这些棘手的情况，但其中一些会很快让你的日常工作变成地狱。

在本文中，我们将仔细阅读五个有趣而棘手的 JavaScript 示例，向您展示 JavaScript 有时可能有点奇怪——而且大多数时候都没有好的理由。

# 1.`NaN`不是数字

众所周知 *NaN* 代表“不是一个数”。但如果我们仔细研究一下，它真的不是一个数字吗？让我们看看下面的例子。

```
typeof NaN; // 'number'
```

如您所见 *NaN* 是型号*编号*的。嗯，有时候我想不是在名字里…

# 2.`[]`和`null`是对象

*[]* 和 *null* 都是对象。我们可以用下面这段代码来验证这一点:

```
typeof []; // 'object'
typeof null; // 'object'
```

然而，一旦我们使用的*实例来检查 *null* 是否是一个对象，我们就会得到一个 *false* 。*

```
null instanceof Object; // false
```

这种奇怪行为的答案在于 *typeof* 函数的工作方式。

# 3.`Math.max()`小于`Math.min()`

等等，什么？让我们再读一遍。Math.max 小于 Math.min？这完全没有道理。

我们可能都至少用过一次 Math.min 和 Math.max 函数。当您向 min 和 max 函数传递一些参数时，它们都工作得很好——一切都按预期工作。

```
Math.min(1, 4, 7, 2); // 1
Math.max(1, 4, 7, 2); // 7
```

但是一旦你忽略了参数，事情就会有一点点的变化。而且不是变得更好。一旦不向 Math.min 和 Math.max 传递任何参数，函数往往会分别返回*in inity*和*-in inity*。

```
Math.min(); // Infinity
Math.max(); // -Infinity
Math.min() > Math.max(); // true
```

# 4.比较`null`和`0`

下面的表达似乎引入了一个矛盾。当使用等于或大于运算符比较 *null* 和 *0* 时，两者都返回 *false* 。但是当组合这些运算符*时，将返回 true* 。

那没有意义，对吗？

```
null == 0; // false
null > 0; // false
null >= 0; // true
```

# 5.`[]`是真理，但不是`true`

数组是真值。但是，不等于真的。

```
!![]; // true
[] == true; // false
```

这同样适用于 null — null 是 false，但不是 false。

```
!!null; // false
null == false; // false
```

# 就是这样！

您知道 JavaScript 中存在所有这些奇怪的东西吗？一旦你偶然发现这些东西，你真的会被它们弄得头破血流。虽然，有时候笑一笑可能更好。

我相信你自己可能也遇到过这些奇怪的事情。请随意分享。

如果你想知道更多关于奇怪的 JavaScript 小技巧，我有好消息告诉你。有一个完整的 [GitHub 库](https://github.com/denysdovhan/wtfjs)，里面有很多有趣的技巧——我在本文中也使用了这些技巧。