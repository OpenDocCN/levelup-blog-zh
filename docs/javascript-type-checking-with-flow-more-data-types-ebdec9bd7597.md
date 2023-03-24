# 使用流的 JavaScript 类型检查—更多数据类型

> 原文：<https://levelup.gitconnected.com/javascript-type-checking-with-flow-more-data-types-ebdec9bd7597>

![](img/803e5616b09ff78a8259c856a3c2feec.png)

[杆长](https://unsplash.com/@rodlong?utm_source=medium&utm_medium=referral)对[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)拍照

Flow 是由脸书开发的一个类型检查器，用于强制 JavaScript 中的数据类型。它有许多内置的数据类型，我们可以用它们来注释变量和函数参数的类型。

在本文中，我们将研究一些流特有的数据类型，包括可能类型、文字类型、`mixed`和`any`类型

# 也许是类型

也许类型是用来注释可选的变量或参数的。它由类型名前面的问号表示。

例如，`?string`和`?number`可能分别是字符串和数字的类型。

对于`?string`类型，我们可以用一个字符串、`null`、`undefined`或者什么都不用设置。例如，我们可以写:

```
let a: ?string = null;
let b: ?string = undefined;
let c: ?string;
let d: ?string = 'abc';
```

参数的规则是相同的:

```
maybeString(null);
maybeString(undefined);
maybeString();
maybeString('abc');
```

还有什么，比如:

```
maybeString(0);
```

会给我们带来错误。

# 可选对象属性

我们可以通过在属性名后添加一个`?`来将对象属性标记为可选的。

例如，我们可以写:

```
let x: { foo?: string } = {};
let y: { foo?: string } = { foo: 'abc' };
```

这条规则也适用于参数，因此下面的代码将会起作用:

```
function optionalFoo(val:  { foo?: string }){}optionalFoo({});
optionalFoo({ foo: 'abc'});
```

`undefined`也管用。例如，我们可以写:

```
let y: { foo?: string } = { foo: undefined };
```

我们也可以写:

```
function optionalFoo(val:  { foo?: string }){}
optionalFoo({ foo: undefined });
```

`foo`属性的任何其他值都将导致错误。

# 默认功能参数值

像从 ES2015 或更高版本开始的 JavaScript 一样，我们可以为函数参数分配默认值。例如，我们可以写:

```
function foo(value: string = "foo") {}
```

`value`参数的默认值是`'foo'`。

然后我们可以向`foo`函数传递一个字符串、`undefined`或者什么都不传递。例如，以下内容将起作用:

```
foo("bar");     
foo(undefined);foo();
```

传入`undefined`或不传入将导致`'foo'`成为`value`参数的值。

传入的其他所有内容都将导致错误。例如，以下内容不起作用:

```
foo(null);
```

# 文字类型

Flow 有文字类型，允许我们创建只能被赋予少数可能值的变量或函数参数。

例如，我们可以写:

```
let x: 1|2 = 1;
```

因为`x`具有类型`1|2`，这意味着`x`只能被赋值 1 或 2。每个可能的值由`|`符号分隔。`|`符号是联合类型运算符。它指示该类型是操作数中指示的类型之一。

如果我们写:

```
let y: 1|2 = 3;
```

我们得到一个错误，因为`x`被赋予了一个不在`y`可能值列表中的值。

函数参数也是如此。例如，以下内容将起作用:

```
function fruitFn(fruit: 'orange' | 'apple'){}fruitFn('apple');
```

而下面的代码将无法编译:

```
function fruitFn(fruit: 'orange' | 'apple'){}fruitFn('banana');
```

![](img/1d5a582b8e94a446382a00ea3f4689ee.png)

克里斯汀·门多萨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 混合型

`mixed`类型允许我们给变量或函数参数赋值。例如，我们可以写:

```
function foo(value: mixed) {

}foo('apple');
foo(1);
foo(true);
```

对于`mixed`类型，我们必须在操作参数或变量之前弄清楚实际的类型。在对它们进行操作之前，我们可以使用`typeof`或`instanceof`操作符来检查类型。

例如，我们可以检查一个值是字符串还是数字，如下所示:

```
function foo(value: mixed) {
  if (typeof value === 'number'){
    return value;
  }
  else if (typeof value === 'string'){
    return +value;
  }
}
```

我们还可以使用`instanceof`操作符来检查创建对象的构造函数，并相应地运行代码:

```
function foo(value: mixed) {
  if (value instanceof Date){
    return value.toUTCString();
  }
  else if (value instanceof Object){
    return value.toString();
  }
}
```

如果 Flow 不知道我们传入的是什么类型的对象，那么它不会接受代码。例如，我们不能写:

```
function foo(value: mixed) {
  return value.toString();
}
```

我们将得到错误:

```
[Flow] Cannot call `value.toString` because property `toString` is missing in mixed [1].
```

# 任何类型

`any`类型让我们选择不使用流进行类型检查。如果它是一个变量，我们可以把它赋给任何东西，如果它是一个函数参数，我们可以传入任何东西。

它不检查我们调用的方法是否实际存在，或者我们使用的操作符是否实际处理我们传入的数据。

`any`类型与`mixed`类型不同。`mixed`类型为选中类型。我们必须用代码检查类型，因为我们可以用类型`mixed`对变量或参数做任何事情。

类型为`any`的变量或参数没有任何类型检查，所以 Flow 允许我们对类型为`any`的变量做任何我们想做的事情。这意味着，如果我们没有检查正在操作的数据类型，我们可能会像其他 JavaScript 代码一样出现运行时错误。

当类型为`any`的变量或参数被赋给没有类型注释的其他变量时。它将被设置为`any`类型。

我们应该通过用类型注释变量或参数来停止这种情况。

例如，我们应该写:

```
function fn(obj: any) {
  let bar: number = obj.bar;
}
```

然后将在`bar`上进行类型检查。这意味着

```
function fn(obj: any) {
  let bar: number = obj.bar + 1;  
}
```

会有用，但是

```
function fn(obj: any) {
  let bar: number = obj.bar + 'abc';  
}
```

会失败。

Flow 有许多数据类型，使得来自 JavaScript 的类型注释非常方便。我们可以使用`mixed`类型传入任何东西或将变量赋给任何东西，但是我们可以在传入值或变量赋值后在代码中检查类型。

`any`类型绕过了流的类型检查，允许我们给函数赋值或传递值。

文字类型让我们将变量或参数设置为列出的可能值，用一个`|`符号分隔。

也许类型使得属性或变量可以被随意传入或停留`undefined`。