# JavaScript 反模式—即时函数

> 原文：<https://levelup.gitconnected.com/javascript-antipatterns-immediate-functions-3603dd64c5e5>

![](img/808ca01b6d0d4483b27845d9f34ee9bf.png)

由[让-菲利普·德尔伯格](https://unsplash.com/@jipy32?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

在本文中，我们将研究一些在定义和使用函数时应该避免的反模式。

# 返回函数

在 JavaScript 中返回函数是可能的，因为函数只是普通的对象。

例如，我们可以定义一个返回另一个函数的函数，如下所示:

```
const foo = () => {
  return () => {
    console.log('bar');
  }
}
```

我们在上面的函数中返回一个记录`'bar'`的函数。

我们调用`foo`返回函数就可以调用它。那么我们可以称之为:

```
const bar = foo();
bar();
```

# 自定义函数

如果我们把一个函数赋给一个已经有函数的变量，那么旧的函数会覆盖现有的函数。

例如，我们可以写:

```
let foo = () => {
  console.log("foo");
  foo = () => {
    console.log("bar");
  };
};
```

如果我们调用它两次，我们会得到两个不同的结果:

```
foo()
foo()
```

我们会得到:

```
foo
bar
```

在控制台日志输出中。

然而，即使我们能够做到，它在大多数情况下可能也不是很有用。

# 即时功能

我们还可以定义立即调用的函数表达式(IIFEs ),即立即创建并调用的函数。

例如，我们可以这样写生活:

```
(() => {
  console.log('foo');
})();
```

在上面的代码中，我们有一个用括号括起来的匿名函数。

然后我们可以通过在末尾加上括号来调用它。

这样，我们就不必创建一个命名函数并在其他地方调用它。

创造生命的模式如下:

*   使用函数表达式或箭头函数定义函数
*   在末尾添加一组括号，使函数立即运行。
*   用括号将整个函数括起来

这很有用，因为我们可以有外部无法访问的变量。

它也可以作为一个模块，因为它可以返回函数内部的内容。

然后我们可以将返回值赋给一个变量。

然而，现在我们有了模块，我们可以使用它们而不是使用 IIFEs 来隐藏私有变量。

IIFEs 的一个很好的用途是，我们可以在它被定义后立即用它来调用异步函数。

所以我们可以写:

```
(async () => {
  const val1 = await promise1;
  //...
  const val2 = await promise2;
  //...
})();
```

这样，我们可以使用异步函数，而无需定义命名函数。

# 立即函数的参数

即时函数也可以像其他函数一样接受参数。

例如，我们可以添加参数并按如下方式使用它们:

```
((firstName, lastName) => {
  console.log(`${firstName} ${lastName}`);
})('joe', 'smith');
```

参数在生命结束时被传入，并立即使用这些参数运行函数。

所以我们从控制台日志输出中得到`‘joe smith’`。

我们还可以使用 IIFEs 以安全的方式访问全局对象。

它让我们通过在顶层使用`this`来访问全局变量。

所以我们可以写:

```
const global = ((global) => {
  return global
})(this);
```

我们从外部传入`this`，所以我们可以使用一个箭头函数来返回`global`参数。

在浏览器中，`this`应该是`window`对象。

![](img/17d14493767d409bc2116219c34bb48d.png)

奥马尔·弗洛雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 立即函数的返回值

我们可以像之前看到的那样从生活中返回值。

例如，我们可以编写以下内容:

```
const result = (() => {
  return 1 + 2;
})();
```

我们将看到 3 被赋给了`result`,因为我们调用了函数并立即返回值。

它对于存储私有数据同时返回其他数据也很有用。

例如，我们可以写:

```
const result = (() => {
  let x = 1;
  return x + 2;
})();
```

然后我们得到同样的结果。`x`在功能外部不可用。

IIFEs 也可以用来定义对象属性。

所以我们可以这样写:

```
const obj = {
  message: (() => {
    const who = "me",
      what = "call";
    return `${what} ${who}`;
  }()), getMsg() {
    return this.message;
  }
};
```

然后我们得到`message`是`'call me'`由于模板的值，字符串是由 IIFE 返回的。

`getMsg`是一样的，因为`this`是`obj`而`message`是`'call me'`。

# 结论

我们可以使用 IIFEs 来存储私人数据。同样，我们可以用它们来返回我们想要的东西。

它们对于运行异步函数也很方便，无需定义命名函数。

它们像其他函数一样接受参数。

函数也可以返回其他函数。