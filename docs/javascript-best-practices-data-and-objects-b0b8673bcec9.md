# JavaScript 最佳实践—数据和对象

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-data-and-objects-b0b8673bcec9>

![](img/25f06fbccadf006db3b7ff2b5d57004b.png)

Claudio Guglieri 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究处理不同类型的数据和对象时的最佳实践。

# 原始类型

JavaScript 中有不同种类的原语类型。它们是字符串，数字。布尔人。空，未定义。符号和 bigint。

符号数据类型对 ES6 来说是新的，所以我们应该确保将它们转换成 ES5 代码。它不能被聚合填充，所以它必须被转换成与我们在最终构建工件中的目标平台兼容的代码。

Bigint 也是新的，不能多填充。如果我们使用它，我们也应该在最终的构建工件中将它转换成与我们的目标平台兼容的东西。

# 使用 const 而不是 var

让我们在 JavaScript 代码中定义常量。从 ES6 开始就有了。一旦它被定义，它就不能被赋予新的值。然而，赋值仍然是可变的。

它也是块范围的，所以我们只能访问块内的常量。与用`var`声明的变量不同，它没有被提升，所以我们可以在定义它之前引用它。

`var`也是函数作用域，所以可以在块外访问。

所以，`const`比`var`好。

如果我们不需要重新分配不同的值，那么使用`const`。

否则，使用`let`。

我们可以如下使用它们:

```
const a = 1;
let b = 1;
b = 2;
```

我们不应该在代码中编写类似下面这样的内容:

```
var c = 1;
```

# 目标

当我们创建新对象时，我们应该使用对象字面语法，而不是`Object`构造函数。它要短得多，做同样的事情。

它们都创建从`Object`构造函数继承的对象。

例如，不写:

```
const obj = new Object();
```

在上面的代码中，我们使用了带有`Object`构造函数的`new`操作符来创建一个对象，这是不必要的。

我们改为编写以下内容:

```
const obj = {};
```

使用构造函数让我们在代码中输入更多我们不需要的字符。

# 使用计算的属性名创建动态属性名

从 ES6 开始，我们可以在定义的对象中使用动态属性名。我们用括号将计算出的属性键括起来。

例如，我们可以编写以下代码来实现这一点:

```
const getKey = (k) => {
  return `foo ${k}`;
}const obj = {
  [getKey('bar')]: 1
}
```

在上面的代码中，有一个`getKey`函数，用于返回一个计算出的键，我们将该键放入`obj`对象中，用作属性键。

这样，我们可以用最短和最清晰的方式定义一个带有计算属性键的对象。

这比在定义对象后使用括号符号要好。例如，我们不想写:

```
const getKey = (k) => {
  return `foo ${k}`;
}const obj = {};
obj[getKey('bar')] = 1;
```

因为它比较长，而且我们要多次写`obj`。

![](img/c8c284776079b23a2bcab49c869b8da7.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用对象方法速记

ES6 的另一个伟大特性是对象方法简写。它允许我们在不使用`function`关键字的情况下创建一个方法。

在旧的方法中，我们在对象中创建一个方法，如下所示:

```
const cat = {
  name: 'james',
  greet: function() {
    return `hi ${this.name}`;
  }
}
```

在上面的代码中，我们使用了`function`关键字来定义`cat`对象中的`greet`方法。

更好的方法是用 object 方法简写，如下所示:

```
const cat = {
  name: 'james',
  greet() {
    return `hi ${this.name}`;
  }
}
```

上面的代码和前面的例子做了同样的事情，但是我们省略了`function`关键字。

我们也可以对生成器函数做同样的事情。而不是写:

```
const foo = {
  gen: function*() {
    yield 2;
  }
}
```

我们写道:

```
const foo = {
  * gen() {
    yield 2;
  }
}
```

它们都有`gen`生成器方法，但是我们在第二个例子中省略了`function`关键字。

# 结论

我们应该尽可能使用 ES6 特性。我们应该使用的好特性包括对象方法简写、计算属性键(如果我们需要动态生成的对象键名)和关键字`const`。

如果我们使用像符号和 bigints 这样的新数据类型，我们应该确保它们能在我们的目标平台上工作。