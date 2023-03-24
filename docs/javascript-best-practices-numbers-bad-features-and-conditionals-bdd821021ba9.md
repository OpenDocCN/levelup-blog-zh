# JavaScript 最佳实践——数字、坏特性和条件

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-numbers-bad-features-and-conditionals-bdd821021ba9>

![](img/d18374706d2eedace3a13a6bf4220bd4.png)

照片由 [Terrah Holly](https://unsplash.com/@tlholly?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将看看在操作数字和条件时应该遵循的一些最佳实践。

我们也看到了我们不应该使用的糟糕的 JavaScript 特性。

# 使用断路器防止开关箱中的故障

我们应该在每个`case`块或语句的末尾添加`break`，以防止匹配案例下面的案例运行。

因此，与其写:

```
switch (val) {
  case 1:
    foo()
  case 2:
    bar()
}
```

我们写道:

```
switch (val) {
  case 1:
    foo();
    break;         
  case 2:
    bar();
    break;
}
```

# 无浮动小数

如果我们想写一个介于 0 和 1 之间的小数，我们应该从一个十进制数的 0 开始。

它让每个人都明白这是一个十进制数，尽管 JavaScript 并不要求这样。

例如，不要写:

```
const tax = .5;
```

我们写道:

```
const tax = 0.5;
```

# 没有重新分配给函数声明

我们不应该给函数声明重新赋值。覆盖函数声明的值通常是一个错误或问题。如果我们希望能够改变这个值，我们应该使用一个函数表达式。

例如，我们不应该写:

```
function foo () { };
foo = otherFunc;
```

# 不要给全局变量赋值

像`window`和`process`这样的全局变量是为了使用。我们不想给它重新赋值，因为它会覆盖对象，我们不会再得到任何我们需要的属性。

因此，我们不应该编写这样的代码:

```
window = {};
```

# 没有隐含的评估调用

除了`eval`，还有`Function`构造函数、`setTimeout`和`setInterval`，它们也接受字符串并作为代码运行。

例如，我们可以写:

```
const fn = new Function('a', 'console.log(a)');
```

或者:

```
setTimeout('console.log("foo")')
```

或者:

```
setTimeout('console.log("foo")', 2000)
```

这些都是有效的，并导致字符串中的代码被运行。这是一个安全风险，因为我们可以从字符串运行代码。字符串中的代码很难调试。此外，它们不能被优化，因为它们在一个字符串中。

因此，我们应该只写代码。

例如，我们可以写:

```
setTimeout(() => console.log("foo"), 2000)
```

# 嵌套块中没有函数声明

函数声明应该只在顶层。

JavaScript 允许它出现在块中，但它不应该出现在那里。

因此，我们不应该有这样的代码:

```
if (authenticated) {
  function doSomething () {}
}
```

# RexExp 构造函数中的正则表达式字符串内没有无效的正则表达式

当我们使用`RegExp`构造函数创建正则表达式时，确保我们在字符串中有有效的正则表达式。

所以我们不应该写:

```
const re =new RegExp('[a-z');
```

但是我们应该写:

```
const re =new RegExp('[a-z]');
```

# 没有不规则的空白

我们应该在代码中使用普通的空白字符，以避免在不同的运行时环境中解析它的问题。

所以我们不应该有这样的事情:

```
function foo () /*<NBSP>*/{}
```

# 不要使用 __ 迭代器 _ _

属性`__iterator__`不是 JavaScript 对象创建迭代器函数的标准属性。

相反，我们应该使用`Symbol.iterator`属性。

所以我们不应该写这样的话:

```
Foo.prototype.__iterator__ = function () {}
```

相反，我们应该写:

```
const obj = {
  *[Symbol.iterator](){
    //...
  }
}
```

或者:

```
class Foo {
  *[Symbol.iterator](){
    //...
  }
}
```

# 没有标签与范围内变量共享一个名称

我们不应该让循环的标签与循环范围内的变量同名。

这给译员和我们都造成了困惑。

所以我们不应该写:

```
let score = 100;
const foo = () => {
  score: while (true) { 
    score -= 10;
    if (score > 0) continue score;
    break;
  }
}
```

# 没有标签语句

标签语句是用一个名字来标记循环，所以我们可以用它来做一些循环操作，比如`continue`和`break`。

这是 JavaScript 中很少使用的特性。

因此，我们很可能不想使用它。

所以我们不应该有这样的事情:

```
label:
  while (true) {
    break label     
  }
```

# 没有不必要的嵌套块

我们不应该有无用的嵌套块。

嵌套代码更难阅读，无用的嵌套更糟糕。

所以与其写:

```
function foo () {
  {                  
    bar()
  }
}
```

我们写道:

```
function foo  () {
  bar();
}
```

![](img/b26d3c561a13a60e6585f0e2d17abd9c.png)

照片由[亚历山大·安德鲁斯](https://unsplash.com/@alex_andrews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们应该避免非标准的和很少使用的结构。

此外，我们不应该有导致混乱的不必要的代码。