# 您可能没有用过的 JavaScript 特性

> 原文：<https://levelup.gitconnected.com/javascript-features-we-probably-havent-used-adc4ca6711b5>

![](img/fedf81da2e8ab0404b58a6f0ac2aba4f.png)

杰森·哈弗索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 有很多经常使用的特性。然而，也有一些功能可能我们大多数人都没有用过。

在本文中，我们将研究其中的一些特性以及它们可能的用例。

# 标记的模板文字

标记模板文字是处理模板字符串的函数。

它们经常在 React 库中使用，如`style-components`库。这个功能其实很有用。

它让我们以一种简单的方式解析模板字符串。它让我们用函数解析模板文字。

例如，我们可以如下使用它:

```
const tag = (strings, ...vals) => {
  console.log(strings);
  console.log(vals);
}const name = 'foo';
tag `Hi ${name}`;
```

在上面的代码中，我们有`tag`函数和`vals`数组，前者有包含字符串片段的`strings`函数，后者有插入到字符串中的值。

在上面的例子中，我们有一个带有`'Hi'`的数组，并且`''`是`strings`的值。

在`vals`数组中，我们有`'foo'`。

我们可以用它来返回一个我们想要返回的对象。例如，我们可以将`tag`函数重写如下:

```
const tag = (strings, ...vals) => {
  return `${strings[0]}! ${vals[0]}`
}
```

在上面的代码中，我们从`strings`和`vals`中获取字符串的各个部分，并在两个表达式之间放置一个`!`。

然后我们从返回值中得到`Hi ! foo`。

# 逗点算符

逗号运算符总是返回由逗号运算符分隔的项目列表中的最后一个项目。

例如，我们可以如下使用它:

```
const foo = (1, 2, 3);
```

那么我们得到 3。

它可以接受任何表达式，并且总是返回最后一个计算的表达式。

# 随着

`with`操作符绝对是我们不应该使用的。在严格模式下是禁止的。

这个操作符在语言中增加了一些性能和安全问题。它用于扩展语句的作用域链。

它的用法如下:

```
with(expression)
  statement
```

或者:

```
with(expression) {
 statement
 statement
  ...
}
```

在上面的代码中，我们在`expression`周围创建了一个新的范围。

块内的所有`statements`都将`expression`作为父作用域。

例如，我们可以如下使用它:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
}
with(obj) {
  console.log(a, b, c);
}
```

在上面的代码中，`with`块中的表达式的作用域都是相对于`obj`对象的。

所以`a`、`b`、`c`其实就是`obj.a`、`obj.b`、`obj.c`。

块范围的变量在`with`块之外不可用，就像任何其他块范围的变量一样。

例如，如果我们有:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
}
with(obj) {
  console.log(a, b, c);
  let x = 1;
}console.log(x);
```

然后，如果我们试图在`with`块之外引用`x`，如果我们试图在`x`上调用`console.log`，我们会得到一个错误。

![](img/6c057739df8e8754e2417f3998c12adb.png)

杰里米·斯图尔特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `in`

`in`操作符对于检查一个属性是对象的一部分还是原型链上的任何一个原型都很有用。

如果属性在对象或原型链上的任何属性中，它将返回`true`，否则返回`false`。

例如，如果我们有下面的类结构和对象:

```
class Foo {
  constructor() {
    this.a = 1;
  }
}class Bar extends Foo {
  constructor() {
    super();
    this.b = 2;
    this.c = 3;
  }
}const bar = new Bar();
```

然后，如果我们记录以下表达式的返回值:

```
console.log('a' in bar);
console.log('b' in bar);
console.log('c' in bar);
```

我们得到所有日志`true`。

这是因为`bar`有一个`Foo`实例作为其原型，而`in`操作符检查对象本身及其原型的属性。

因此，`a in bar`也就是`true`。如果我们只想检查属性是否为非继承属性，那么我们必须使用`obj.hasOwnProperty`方法，其中`obj`是任何没有`null`原型的 JavaScript 对象。

# 结论

带标签的模板文字对于将模板字符串转换成我们想要的值很有用。

逗号运算符总是返回逗号分隔列表中的最后一项。

操作符检查一个属性是在一个对象中还是在它的原型中。

`with`是 JavaScript 代码中不应该有的东西。