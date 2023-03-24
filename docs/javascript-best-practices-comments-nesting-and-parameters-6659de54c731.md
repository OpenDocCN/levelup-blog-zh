# JavaScript 最佳实践—注释、嵌套和参数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-comments-nesting-and-parameters-6659de54c731>

![](img/96231662117f59907f39fdd19a3d466f.png)

[柳德米拉·德尼修](https://unsplash.com/@hedgehog90?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了使代码易于阅读和维护，我们应该遵循一些最佳实践。

在这篇文章中，我们将看看我们应该遵循的一些最佳实践，以使每个人的生活更轻松。

# 我们的代码库中没有注释掉的代码

我们不应该在我们的代码库中注释掉代码。

我们有版本控制来记录我们的变更。

所以我们应该移除它们。

如果我们有:

```
doStuff();
// doMoreStuff();
```

那么我们应该把它改写成:

```
doStuff();
```

# 没有日志注释

我们不应该有日志注释，因为版本控制有代码变更记录。

例如，我们不应该写:

```
/**
 * 2019-02-03: Removed type-checking
 * 2018-03-14: Added add function
 */
function add(a, b) {
  return a + b;
}
```

我们写道:

```
function add(a, b) {
  return a + b;
}
```

# 没有位置标记

位置标记只是噪音，我们不应该在代码中使用它们。

而不是写:

```
////////////////////////////////////////////////////////////////////////////////
// initialize setup data
////////////////////////////////////////////////////////////////////////////////
const init = function() {
  // ...
};
```

我们写道:

```
const init = function() {
  // ...
};
```

# 每个文件的最大类别数

我们可能想要强制每个文件的类的最大数量，以减少文件的复杂性和责任。

例如，我们可以将类的数量限制为最多 3 个:

```
class Foo {}
class Bar {}
class Baz {}
```

# 块可以嵌套的最大深度

嵌套代码很难阅读。

因此，我们应该尽可能地消除它们。

一层或两层嵌套可能是可以容忍的最大值。

例如，不要写

```
function foo() {
  for (;;) { 
    while (true) {
      if (true) { 
        if (true) {
        }
      }
    }
  }
}
```

相反，我们写道:

```
function foo() {
  for (;;) {

  }
}
```

我们可以使用保护条款提前返回。

例如，我们可以写:

```
function foo() {
  if (!cond) {
    return;
  }
  while (true) { 

  }
}
```

而不是:

```
function foo() {
  if (!cond) {
    while (true) { }
  }
}
```

我们也可以使用`break`或`continue`来停止一个循环或移动到它的下一个迭代。

# 最大线路长度

我们必须水平滚动来阅读长行。

因此，我们不应该写它们。

例如，我们不应该写:

```
const foo = { "foo": "This is a foo.", "bar": { "qux": "This is a bar"}, "baz": "This is a baz" };
```

它溢出了线。

相反，我们写道:

```
const foo = {
  "foo": "This is a foo.",
  "bar": {
    "qux": "This is a bar"
  },
  "baz": "This is a baz"
};
```

每行都短得多，也更容易阅读。

我们可以对注释做同样的事情。

# 最大文件长度

大文件很复杂，很难理解。

因此，我们应该将它们分成更小的文件。

通常建议最多 300 到 500 行。

# 最大功能长度

我们应该确保我们的函数只做一件事。

因此，我们应该减少函数的长度。

例如，我们可以写:

```
function foo() {
  let x = 0;
}
```

这是一个简短的函数。

# 回调可以嵌套的最大深度

我们经常需要为同步和异步代码运行回调。

我们必须运行它们中的许多来做我们想要做的所有事情。

最糟糕的写法是把它们深深地嵌套起来。

所以我们不应该写这样的东西:

```
foo(function() {
  bar(function() {
    qux(function() {
      abc(function() { });
    });
  });
});
```

这很难阅读和调试，尤其是如果我们在每个回调中有很多代码。

我们应该使用像承诺这样的替代方案。

# 函数定义中的最大参数数量

我们不应该在一个函数中有太多的参数。

我们拥有的越多，就越难阅读。

例如，不写:

```
function foo(bar, baz, qux, abc) { 
  doSomething();
}
```

我们写道:

```
function foo({ bar, baz, qux, abc }) { 
  doSomething();
}
```

如果我们需要很多参数，我们可以获取一个对象并析构它们。

![](img/7ac67599b012e47e686d0dd28caa2ece.png)

谢恩·杨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们不应该注释掉代码或其他无用的注释。

嵌套、函数长度和参数数量应该最小化。

班级的数量也应该减少。

一个文件不应该有太多行代码。