# JavaScript 最佳实践—现代语法

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-modern-syntax-493ae277f67c>

![](img/d7c16383f3bfbac726183f5f1cd390ed.png)

[杆长](https://unsplash.com/@rodlong?utm_source=medium&utm_medium=referral)在[未飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)上的照片

有比其他方法更好的方法来编写 JavaScript 代码。

在本文中，我们将研究使用最新 JavaScript 语法特性的最佳实践。

# 句法糖

在 JavaScript 中，包含的语法糖通常是好的。

它们让我们以更短、更清晰的方式编写代码，并使代码在编写过程中更具可读性。

这些语法糖是现有技术的替代物，所以我们应该使用它们。

# 常数不一致

`const`是声明块范围常量的关键字。

他们会把赋给它的原始值变成不可变的，因为我们不能在一个常量赋值后再给它赋值。

当我们声明一个值时，它必须被赋值。

然而，`const`有点欺骗性，因为有些人可能不知道我们仍然可以通过改变属性来改变分配给`const`的对象。

此外，可以用像`push`和`unshift`这样的方法改变数组。

因此，我们不应该假设分配给`const`的对象是不可变的。

# 限制函数的范围

用`functionm`关键字定义的传统函数可以被调用来运行块中定义的语句，并可能返回值。

如果写成函数声明，它们可以在任何地方运行。

例如，如果我们有:

```
function foo() {
  //...
}
```

那么`foo`可以在它被定义之前或之后运行。

它还定义了自己的`this`，可以作为构造函数与`new`操作符一起使用。

所以，为了限制我们函数的能力，我们应该使用箭头函数。

如果我们需要构造函数，那么我们应该使用类语法来定义它们，让每个人都清楚。

# 类语法

类语法对于定义构造函数非常有用。它与旧的构造函数语法做同样的事情。

它看起来像 Java 等面向对象语言中的一个类，但它做的事情与 JavaScript 构造函数一样。

因此，原型继承模型仍然用于 JavaScript 类。

所以如下:

```
function Person(name) {
  this.name = name;
};Person.prototype.greet = function() {
  console.log(`hi ${this.name}`);
};
```

与以下内容相同:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    console.log(`hi ${this.name}`);
  }
}
```

它们持有和做同样的事情，但是字段和方法的位置是不同的。

# 箭头功能

箭头功能很棒。他们更矮。但在试镜中会更整洁。

如果我们在第一行返回，它可以在不添加`return`关键字的情况下返回。

它们封装了使它们更方便的品质。但是它们不是用`function`关键字定义的传统函数的替代物。

我们可以通过调用`bind`来改变函数内部的`this`。

还有，我们不能用它们调用`call`和`apply`来改变`this`，也不能用它们调用带参数的函数。

![](img/0d66623e29c15fa1a8236a55d9e121db.png)

托马斯·凯利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 走向异步

由于 JavaScript 的单线程特性，用 JavaScript 实现异步有其自身的困难。

我们必须编写代码来解除线程阻塞，这样在我们准备好运行程序之前，它们不会阻碍我们的程序。

这就是带有回调的异步代码的用武之地，它使得调用异步代码更加容易。

我们可以通过调用`setTimeout`使我们的代码异步。此外，几乎所有 HTTP 客户端都以异步方式运行。

如果我们有很多带有回调的异步代码，那么我们必须嵌套回调。

事情变得很糟糕。

如果我们使用回调来链接异步代码，我们可能会得到如下代码:

```
async1((err, res) => {
  if (!err) {
    async2(res, (err, res) => {
      if (!err) {
        async3(res, (err, res) => {
          //...
        });
      }
    });
  }
});
```

这很难看，而且不可维护。相反，我们用承诺。那么我们可以这样写:

```
promise1
  .then((res) => {
    //...
    return promise2
  })
  .then((res) => {
    //...
    return promise3
  })
  .then((res) => {
    //...
  })
```

这比前一个例子中嵌套异步回调要干净得多。

我们可以把事情写得更短:

```
(async () => {
  const val1 = await promise1;
  //...
  const val2 = await promise2;
  //...
  const val3 = await promise3;
  //...
})();
```

如我们所见，代码要短得多，并且与我们之前的承诺链完全相同。

唯一的区别是`va11`、`val2`和`val3`保存承诺的解析值，而不是`res`。

# 结论

我们了解了一些关于 JavaScript 的事情，比如`const`并不总是不可变的，并用`async` 和`await`清理异步代码。

此外，箭头函数和类应该分别用于常规函数和构造函数。