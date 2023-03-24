# 为什么是时候停止使用 JavaScript 了

> 原文：<https://levelup.gitconnected.com/why-its-time-to-stop-using-javascript-iifes-b62602f25bfc>

![](img/864800332620e1f6f265b75c32de7ed8.png)

照片由[阿格巴洛斯](https://unsplash.com/@agebarros?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 语言中，IIFE 代表立即调用的函数表达式。

这是一个被定义然后立即执行的函数。

在这篇文章中，我们将看看为什么是时候停止在我们的代码中写生命了。

# 我们可以在 JavaScript 中定义块范围的变量

因为 ES6 是作为标准发布的，所以我们可以用`let`和`const`声明块范围的变量和常量。它还引入了独立的块，将变量和常量隔离到它们自己的块中，外部无法访问。

例如，我们可以写:

```
{
  let x = 1;
}
```

那么`x`就不会对外公开。

它比:

```
(()=>{
  let x = 1;
})();
```

现在几乎所有现代浏览器都支持 ES6，我们应该停止使用 IIFEs 来分离变量与外界。

隔离变量的另一种方法是模块，它也得到广泛的支持。只要我们不导出它们，其他模块就无法使用它们。

# 我们不再那么需要闭包了

闭包是返回另一个函数的函数。返回的函数可能运行在其外部但在封闭函数内部的代码。

例如，它可能会产生如下副作用:

```
const id = (() => {
  let count = 0;
  return () => {
    ++count;
    return `id_${count}`;
  };
})();
```

同样，现在我们有块和模块来隔离数据，这变得更加复杂和不必要。

我们可以将所有这些放在他们自己的模块中，这样我们就不必担心暴露数据。

它还会产生副作用，这并不好，因为我们应该尽可能避免产生副作用。这是因为它们使得函数难以测试，因为它们不纯。

当我们可以避免嵌套时，返回函数的函数也会引入嵌套，所以它比不返回函数的函数更容易混淆。

更好的选择是用一个模块代替它。

使用模块，我们可以编写:

```
let count = 0;export const id = () => {
  ++this.count;
  return `id_${count}`
}
```

在上面的代码中，我们有相同的`count`声明，并且我们导出了`id`函数，这样其他模块也可以使用它。

这隐藏了`count`并暴露了我们想要的函数，就像生命一样，但是嵌套更少了，我们不必定义另一个函数并运行它。

# 别名变量

同样，我们曾经写过这样的东西:

```
window.$ = function foo() {
  // ...
};(function($) {
  // ...
})(jQuery);
```

现在，我们绝对不应该仅仅为了给变量创建别名而编写生命，因为我们可以使用模块来做到这一点。

有了模块，我们可以导入不同名字的东西。

今天的方法是这样写:

```
import { $ as jQuery } from "jquery";const $ = () => {};
```

同样，我们不应该给`window`对象附加新的属性，因为这会污染全局范围。

# 捕获全局对象

有了`globalThis`，我们不必担心不同环境中全局对象的名字，因为它正在成为一种标准。

因此，我们不需要通过在顶层编写以下代码来捕获全局对象:

```
(function(global) {
  // ...
})(this);
```

甚至在`globalThis`之前，通过编写以下代码来设置全局对象并不太难:

```
const globalObj = self || window || global;
```

或者如果我们想更精确些，我们可以写:

```
const getGlobal = () => { 
  if (typeof self !== 'undefined') { return self; } 
  if (typeof window !== 'undefined') { return window; } 
  if (typeof global !== 'undefined') { return global; } 
  throw new Error('unable to locate global object'); 
};
```

那么我们就不需要添加额外的函数调用和嵌套。

![](img/c5cee6682e02c3aa93e25f691ab03489.png)

由[凯拉·奥托](https://unsplash.com/@kaylahotto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 缩小优化

有了 JavaScript 模块，我们不再需要将代码与文件分开，这样我们的文件就可以适当地缩小。

网络包、浏览器、包裹、汇总等。，都可以正确地处理模块，所以我们应该使用它们来创建更干净的代码。

# 结论

是时候停止在我们的代码中写生命了。它增加了额外的函数定义和嵌套。

此外，在 JavaScript 模块被引入和广泛使用之前，这是一个时代错误。2020 年，我们应该使用模块和块来分离代码。

块范围的变量用于防止从模块外部访问变量。