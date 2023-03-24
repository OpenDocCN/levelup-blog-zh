# JavaScript 最佳实践—新的构造和函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practice-new-constructs-and-functions-f28585a11a58>

![](img/a1fc282dc7a8b21e5bc053f73e8b2e58.png)

照片由[第八页工作室](https://unsplash.com/@pageeightstudio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 也有很多棘手的地方。我们可以很容易地遵循一些最佳实践，使我们的 JavaScript 代码易于阅读。

在本文中，我们将着眼于用模块和块替换 IIFE，用类方法和箭头函数替换传统函数，从`script`标签中移除语言属性，编写纯函数，以及避免一长串参数。

# 用模块和积木代替生活

IIFE 代表立即调用的函数表达式。这是一个构造，我们定义一个函数，然后立即调用它。

这是隔离数据以防止从外部访问它们的一种流行方法。此外，它将它们隐藏在全局范围之外。

这在 ES6 之前很方便，因为没有模块标准，也没有更简单的方法来隐藏全局范围内的东西。

但是，在 ES6 中，模块是作为一个新特性引入的。这意味着我们可以替换类似这样的东西:

```
(function() {
  let x = 1;
})()
```

模块具有:

```
let x = 1;
```

如果我们不希望它在模块外可用，我们就不要导出它。

有了模块，也不需要用 IIFEs 命名空间，因为我们可以把模块放在不同的文件夹中，把它们彼此分开。

还有，ES6 的另一个新特性是 blocks。现在，我们可以使用花括号定义与外部范围隔离的代码块，如下所示:

```
{
  let x = 1;
  console.log(x);
}
```

有了像`let`或`const`这样的块范围的关键字来声明变量或常量，我们就不必担心那些我们不希望在外面被访问的东西被访问了。

现在我们可以定义任意的块，而不需要`if`语句、循环或生命。

# 用箭头函数和类方法代替传统函数

同样，在 ES6 中，我们有构造函数的类语法。这样，我们就不需要`function`关键字来创建构造函数。

这使得继承变得更加容易，并且没有关于`this`的混乱。

此外，我们还有箭头函数，它不会改变函数内部的`this`的值。

使用`function`关键字的唯一理由是生成器函数，它们是用`function*`关键字声明的。

例如，我们可以通过编写以下代码来创建一个简单的生成器函数:

```
const generator = function*() {
  yield 1;
  yield 2;
}
```

注意，生成器函数只能返回生成器，所以我们不能从它返回任何东西。

# 来自脚本标签的语言属性

`language`属性不再需要包含在`script`标签中。

例如，不要写:

```
<script src="[https://code.jquery.com/jquery-2.2.4.min.js](https://code.jquery.com/jquery-2.2.4.min.js)" integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44=" crossorigin="anonymous" language='text/javascript'></script>
```

我们写道:

```
<script src="[https://code.jquery.com/jquery-2.2.4.min.js](https://code.jquery.com/jquery-2.2.4.min.js)" integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44=" crossorigin="anonymous"></script>
```

这是因为 JavaScript 是在浏览器中运行的语言。

![](img/d81dea617d7fabbb2eb8448e47e54889.png)

雄心勃勃的创意公司 Rick Barrett 在 Unsplash 上的照片

# 编写纯函数

我们应该把函数写成纯函数。也就是说，如果我们传递相同的输入，函数总是给出相同的输出。

这很重要，因为它使测试变得容易。此外，我们知道它具体会做什么，减少出错的机会。

纯函数易于阅读和理解。流程确定且简单。因此，这是可以预见的。

纯函数的示例如下:

```
const add = (a, b) => a + b;
```

如果我们给它相同的输入，`add`函数总是给出相同的输出，因为它只是根据参数计算结果。不取决于别的。

因此，它的逻辑流程是非常可预测的。

非纯函数的一个例子是，即使给定相同的输入，也返回不同的输出。例如，如果我们有一个函数来获取今年之前`x`年的年份:

```
const getYearBefore = (x) => new Date().getFullYear() - x;
```

这不是一个纯粹的函数，因为`new Date()`会根据当前日期而变化。因此，在给定相同输入的情况下，`getYearBefore`的结果取决于当前日期的年份。

为了使它成为一个纯粹的函数，我们可以写成:

```
const getYearBefore = (date, x) => date.getFullYear() - x;
```

因为日期现在被传递到函数中，所以假设我们有相同的输入，我们会得到相同的结果，因为函数中没有不确定的东西。它只是将参数组合在一起并返回结果。

此外，我们不必担心当前日期或日期实现如何变化。

因为`new Date()`结果的不可预测性，在我们把它变成一个纯函数之前，扩展函数是很困难的。

此外，更改`new Date()`可能会在更改前破坏程序的其他部分。现在我们不用担心这个了。

由于不可预测性，跟踪和调试也更加困难。

# 避免长参数列表

使用 ES6 的析构语法，我们可以向函数传递大量的参数，而不必实际上分别传递它们。

例如，不要写:

```
const add = (a, b, c, d, e) => a + b + c + d + e;
```

它有 5 个参数。我们可以写道:

```
const add = ({
  a,
  b,
  c,
  d,
  e
}) => a + b + c + d + e;
```

这样，我们只需要将一个对象传入函数，并在`add`函数中得到 5 个变量。

我们可以这样称呼`add`:

```
const obj = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
  e: 5
}add(obj);
```

这也适用于嵌套对象。例如，我们可以写:

```
const buildString = ({
  foo: {
    bar,
    baz
  }
}) => `${bar} ${baz}`;
```

那么我们可以这样称呼`buildString`:

```
const obj = {
  foo: {
    bar: 'bar',
    baz: 'baz'
  }
};buildString(obj);
```

为了保持数据的私密性，我们可以用模块和块来代替 IIFEs。现在我们不需要额外的代码来定义函数和调用它。

此外，类语法是定义构造函数的更清晰的方法，尤其是当我们想要从其他构造函数继承时。`this`的价值也更加清晰。

编写纯函数是一个很好的实践，因为它是可预测的，因为给定相同的输入，我们总是有相同的输出。因为它也更容易阅读和测试。

有了析构语法，我们可以减少对象中变量的大量参数。给定我们作为参数传入的属性的键名和位置，所有内容都被自动赋值。