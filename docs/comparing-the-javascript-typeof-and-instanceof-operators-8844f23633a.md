# 比较 JavaScript typeof 和 instanceof 运算符

> 原文：<https://levelup.gitconnected.com/comparing-the-javascript-typeof-and-instanceof-operators-8844f23633a>

![](img/162c0429b2e2433089a3400bd0a4909b.png)

由 [Niklas Garnholz](https://unsplash.com/@niklasgarnholz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，有`typeof`和`instanceof`运算符。

他们看起来很相似，但是他们做不同的事情。

在本文中，我们将看看它们之间的区别以及如何使用它们。

# 运算符的类型

`typeof`算子主要用于获取原始值的类型。

如果我们有数字、字符串、符号、布尔值、bigint 和未定义的值，我们可以用它们来检查。

例如，如果我们有:

```
console.log(typeof 1);
```

我们得到`'number'`日志。

如果我们有变量会更有用:

```
let foo = 1;
console.log(typeof foo);
```

然后我们可以使用`typeof`操作符来检查数据类型`foo`，这也可以得到`'number'`。

需要注意的是，它不能用于检查`null`类型。如果我们写:

```
console.log(typeof null);
```

我们得到`'object'`。相反，我们应该使用`===`操作符来检查`null`，如下所示:

```
foo === null
```

我们可以检查表达式的类型，如下所示:

```
let foo = 1;
let bar = '1';
console.log(typeof (foo + bar));
```

然后我们得到`'string'`日志。

其他示例包括检查布尔值:

```
typeof false === 'boolean';
typeof Boolean(0) === 'boolean';
```

我们还可以检查数字，如下所示:

```
typeof Number('1') === 'number';
typeof Number('foo') === 'number';
typeof NaN === 'number';
```

注意`NaN`和像`Number(‘foo’ )`一样返回`NaN`的计算也是`'number'`类型。我们应该用`Number.isNaN()`方法检查它们。

要检查 BigInt，我们可以写:

```
typeof 2n === 'bigint';
```

为了检查字符串，我们可以写:

```
typeof '' === 'string';
typeof 'foo' === 'string';
```

要检查符号类型，我们可以写:

```
typeof Symbol() === 'symbol'
typeof Symbol('bar') === 'symbol'
```

我们也可以使用`typeof`操作符来检查`undefined`，如下所示:

```
typeof undefined === 'undefined';
let x;
typeof x === 'undefined';
```

此外，我们可以使用它们来检查对象。然而，所有对象都返回类型`'object'`，所以这不是很有用:

```
typeof new Date() === 'object';
typeof /foo/ === 'object';
```

使用`new`操作符创建的任何东西都属于`'object'`类型，包括`String`、`Boolean`和`Number`:

```
typeof new String('foo') === 'object';
typeof new Number(1) === 'object';
```

当我们将`typeof`操作符应用于正则表达式文字时，一些较老的浏览器会返回`‘function'`。然而，这在今天应该不是问题。

## 例外

`typeof document.all`总是返回`‘undefined'`，即使它在所有浏览器中都有定义。

![](img/eb00742da9d43e1a4812bea4425d03ac.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 运算符的实例

`instanceof`操作符测试构造函数的`prototype`属性是否出现在对象的原型链中。

这意味着我们可以使用它来检查对象是否是来自给定类或构造函数的构造函数。

如果一个对象是一个类或构造函数的实例，它返回`true`，否则返回`false`。

例如，如果我们有:

```
class Foo {};
let foo = new Foo();
console.log(foo instanceof Foo);function Bar (){};
let bar = new Bar();
console.log(bar instanceof Bar);
```

然后它们都从`console.log`输出`true`。

## 多个框架和窗口

当我们有多个框架和窗口时，`instanceof`操作符可能不会返回我们期望的结果，因为不同的执行上下文和不同的内置全局对象和构造函数。

例如，如果我们检查一个变量是否是一个数组的实例，我们必须意识到这一点。

幸运的是，有一个`Array.isArray()`方法可以处理数组。

## 用 new 创建的任何东西都可以用 instanceof 操作符来检查

`instanceof`用于检查使用`new`操作符创建的任何东西，包括字符串、布尔值和数字。

例如，如果我们有:

```
let foo = new String('foo');
console.log(typeof foo);
console.log(foo instanceof String);
```

然后我们从第一个`console.log`得到`'object'`，第二个得到`true`。

这是因为我们创建了一个`String`对象的实例，而不是原始值字符串，即使我们可以以同样的方式使用原始字符串和字符串对象。

## 对象文字

不是用`new`操作符创建的对象文字是`Object`的实例。

所以如果我们有:

```
console.log({} instanceof Object);
```

我们得到`true`输出。

所有对象都是`Object`的实例，同时也是创建它们的构造函数的实例。例如，如果我们有:

```
let date = new Date();
console.log(date instanceof Object);
console.log(date instanceof Date);
```

两个`console.log`都输出`true`，因为`date`既是`Date`构造函数的实例，所有非原始对象都是`Object`的实例。

# 结论

`typeof`和`instanceof`操作符完全不同。`typeof`返回它所操作的实体的类型。

如果一个对象是从给定的构造函数创建的，则返回`true`，否则返回`false`。

所有非原始对象都是`Object`的实例，所以总是返回`true`。