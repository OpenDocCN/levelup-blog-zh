# Node.js 最佳实践—使用现代功能

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-using-modern-features-1f5a4a189ec8>

![](img/a91a377490f7e615ca72a6c81a522b94.png)

照片由[贝琳达·费因斯](https://unsplash.com/@bel2000a?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须为人们设定一些准则来遵循。

在本文中，我们将研究一些现代 JavaScript 特性，我们应该使用它们来创建干净且易于维护的代码。

# 更喜欢 const 而不是 let。放弃 var

`var`是一个过时的关键字，用于创建不应再使用的变量。范围不一致，不像`let`和`const`。`var`是函数作用域，所以它可以从外部块访问，并对我们的代码产生潜在的问题。

`let`和`const`的作用域是 blocked，所以它们不能在块外被访问。`const`防止常量被重新分配为另一个值。

例如，如果我们有以下代码:

```
var callbacks = [];
(function() {
  for (var i = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();console.log(callbacks.map( function(cb) { return cb(); } ));
```

那么我们就把`[ 5, 5, 5, 5, 5 ]`看成`callbacks.map( function(cb) { return cb(); } )`的值。

这是因为`i`的值直到达到 5 才被传递给回调函数。然后我们用值 5 运行它们。上面的代码实际上与以下代码相同:

```
var callbacks = [];
(function() {
  var i
  for (i = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();
console.log(callbacks.map( function(cb) { return cb(); } ));
```

因为吊装。因此，当回调运行时，`i`的值将是 5。

`let`变量不托管，所以不会有同样的问题。因此，我们不会有同样的问题:

```
var callbacks = [];
(function() {
  for (let i = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();
console.log(callbacks.map( function(cb) { return cb(); } ));
```

正如我们所看到的，`var`是一种痛苦和困惑，所以我们永远不应该使用它。

# 首先需要模块，而不是内部函数

我们应该在每个代码文件的顶部要求模块。这让我们可以很容易地判断出哪些依赖是必需的。

Require 在 Node.js 中同步运行。因此，如果在函数中调用它们，它可能会阻止其他代码在更关键的时间运行。

如果任何必需的模块或依赖项抛出错误并使服务器崩溃，最好早点发现。

# 通过文件夹要求模块，而不是直接文件

我们应该通过文件夹而不是直接通过文件来要求模块。这是因为如果我们改变模块的文件夹结构，我们不想破坏用户应用程序中的`require`表达式。

因此，以下是好的:

```
require('./foo');
```

但是下面就不好了:

```
require('./bar/foo');
```

![](img/79d90c1c38fc67c26f6f86c32dbe3103.png)

[杰克·卡塔拉诺](https://unsplash.com/@jrcatalano?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 使用===运算符

严格相等操作符`===`比`==`更好，因为它在比较变量之前不会强制变量的类型。使用`===`操作符，两个操作数必须具有相同的类型才能相等。

这很好，因为它防止了比较事物时的许多错误。使用`==`操作符是不好的，因为像下面这样的表达式都会返回`true:`

```
null == undefined
false == '0'
0 == ''    
0 == '0'
```

很多时候，并不是我们想要的。如果我们使用`==`操作符，还有许多其他奇怪的边缘情况可能会导致我们的应用程序出错。因此，我们应该使用`===`操作符。

# 使用异步 Await 并避免回调

由于节点 8 是 LTS，`async`和`await`是节点中的一个特性。因此，我们应该尽可能用它来连锁承诺。这是一个很好的连锁承诺的简写。

旧的回调 API 慢慢转化为`fs`之类的 cord 节点模块中的 promises API。因此，现在我们可以在自己代码之外的地方使用`async`和`await`。

为了处理`async`和`await`中的错误，我们可以如下处理错误:

```
(async ()=>{
  try {
    await Promise.reject('error')
  }
  catch(ex){
    console.log(ex);
  }
})();
```

在上面的代码中，我们用`catch`块捕获错误并记录`ex`的值，该值应该是来自`Promise.reject`的`'error'`。

# 结论

JavaScript 中的新构造之所以存在，是因为它们很好。它们使代码更短、更干净。它们使得代码易于阅读和修改。它只是让每个人都乐于用 JavaScript 编码。像`var`这样的旧结构应该从所有代码中删除。应该用`===`代替`==`。