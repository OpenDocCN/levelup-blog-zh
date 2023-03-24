# 检查 Javascript 数组是否为空的 5 种方法

> 原文：<https://levelup.gitconnected.com/5-ways-to-check-if-a-javascript-array-is-empty-77eff962a232>

## 外加打字提示

![](img/feb2977da12846b199cd7f0dbf24baf3.png)

Pawel Czerwinski 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我将简要描述什么是 Javascript (JS)数组，我们如何使用它，然后我将描述我们如何检查它是否为空。有更多的方法可以做到这一点，所以我将逐步描述它们，因为 JS 语言正在发展，现在我们有更好更简洁的方法来做到这一点。如果您使用 Typescript (TS ),还有一种更有趣、更可靠的方法来检查空数组。

# JS 数组简介

JS 数组用于在一个列表中存储多个值。JS 数组是 JS 对象的子类型。这意味着它以某种方式扩展了 JS 对象的行为。数组的文字语法使用方括号:

```
let myArray = []
```

当对象使用花括号时:

```
let myObject = {}
```

JS 数组使用数字索引，从索引`0`开始。它们也有`length`属性，这是不言自明的。这里有一个例子:

```
let myArray = [1, 245, 'apple', { type: 'fruit' }]myArray.length
// 4
```

这个数组的长度是 4，你可以看到我们可以混合任何我们想要的值(数字，字符串，对象等等。)这是我们获取个体价值的方式:

```
myArray[0]
// 1myArray[2]
// 'apple'
```

**警告**

JS 数组中有所谓的“[空槽](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/objects-classes/ch2.md#empty-slots)”。

> 如果你给数组的一个索引赋值超过了数组的当前末端一个位置，JS 会把中间的槽留为“空”，而不是像你所期望的那样自动把它们赋值为 undefined。

给定我们上面定义的数组，我们可以执行以下操作:

```
myArray[10] = 'carrot'
myArray.length
// 11
```

所有其他职位都是不确定的。

关于数组有很多方法，我们可以做得更多，但作为一个介绍，这就够了。文章的主要部分如下:

# JS 数组是空的吗？存在吗？

让我们描述 5 种检查 JS 数组是否为空以及是否存在的方法。

## 1.长度属性

你可能首先想到的是`length`地产，我们已经介绍过了。

```
myArray.length ? true : false
```

如果长度为 0，将返回 false。比如`myArray`未定义怎么办？或者，如果我们得到`null`，因为一个值来自后端呢？我们需要确保`myArray`存在，否则我们会得到以下错误:

![](img/e19ffdb494097955ec3ad8fe38fd98ec.png)

未捕获的类型错误:无法读取未定义的属性(读取“长度”)

## 2.And (&&)运算符和长度

我们可以使用`&&`运算符来避免以前的错误。

```
myArray && myArray.length ? true : false
```

这很好，但是使用[可选链接操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) `.?`有一个更短的方法。

## 3.可选链接(。？)

我们可以不使用`&&`操作符来检查`myArray`是否存在，同时是否具有长度属性，而是编写:

```
myArray?.length ? true : false
```

说到操作符，我还想提一下[无效合并操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)(?？).我们的数组也包含一个对象，所以我们可以检查它的属性。

```
let myArray = [1, 245, 'apple', { type: 'fruit' }]
myArray?.[3]?.type ?? 'No type property'
// 'fruit'
```

如果它是真的，我们在左边得到我们评估的任何东西，否则我们得到在右边的东西。

## 4.逻辑 NOT(！)

这取决于你真正需要什么，但这里有另一种方法。逻辑 not 运算符对值求反。如果`myArray`为空，下面将返回`true`，即`[]`，或`undefined`或`null`。

```
!myArray?.length ? true : false
```

## 5.isArray()方法

但是，我们如何真正知道我们是否使用了数组呢？字符串上也存在长度属性。我们可以使用 [isArray()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray) 方法:

```
let myArray = [1, 245, 'apple', { type: 'fruit' }]Array.isArray(myArray)
// true
```

显然，您可以将上面列出的方法结合起来。例如:

```
if (Array.isArray(myArray) && myArray.length) {
  // array exists and is not empty
}
```

# 类型脚本解决方案

我推荐阅读卢卡·德尔·普波的这篇文章，看看下面的片段是如何详细工作的。

```
export interface NonEmptyArray<A> extends ReadonlyArray<A> {
  // tslint:disable-next-line: readonly-keyword
  0: A;
}
type ReadOnlyNotEmptyArray<T> = Readonly<NotEmptyArray<T>>;function isNotEmptyArray<T>(as: T[]): as is NotEmptyArray<T> {
  return as.length > 0;
}
```

# 结论

我列出了几种利用操作符来处理数组并检查它们是否为空的方法。我们从属性`length`开始，引入了操作符，如`&&`、可选链接`.?`、无效合并操作符`??`以及最后的`isArray()`方法。如果使用 TypeScript，还可以将检查空数组的代码片段合并到应用程序中。

根据您的需要，您可以选择一种或多种方法并将它们组合在一起，或者您也可以编写类似于 Typescript 的助手函数。希望这篇文章对你有用。

## 参考

*   [你还不知道 JS:对象&类—第二版](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/objects-classes/ch2.md#arrays)
*   [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)
*   [我在检查空数组错误(Youtube)](https://www.youtube.com/watch?v=ULniqxZ8ueI)
*   [Typescript—(ReadOnly)NotEmptyArray](https://dev.to/this-is-learning/typescript-readonlynotemptyarray-2id7)