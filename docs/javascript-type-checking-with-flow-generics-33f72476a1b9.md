# 使用流的 JavaScript 类型检查—泛型

> 原文：<https://levelup.gitconnected.com/javascript-type-checking-with-flow-generics-33f72476a1b9>

![](img/2f5047728e57cd012b92fbdff726c9a8.png)

鲍里斯·斯莫克罗维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Flow 是一个由脸书开发的类型检查器，用于检查 JavaScript 数据类型。它有许多内置的数据类型，我们可以用它们来注释变量和函数参数的类型。

在本文中，我们将了解如何使用泛型类型使数据类型变得抽象，并允许代码重用。

# 定义泛型类型

为了使类型在不同的实体中抽象，我们可以使用泛型类型从实体中抽象出类型。

例如，我们可以用泛型类型编写一个函数，如下所示:

```
function foo<T>(obj: T): T {
  return obj;
}
```

在上面的代码中，我们用`<T>`来表示`foo`是一个通用函数。`T`是被引用时的通用类型标记。

为了利用泛型类型，我们必须对它进行注释。否则，Flow 不会知道它可以接受泛型类型。

当我们想要在类型别名中泛型类型时，我们必须在类型别名中注释类型。

例如，我们可以写:

```
type Foo = {
  func<T>(T): T
}
```

如果我们写道:

```
type Foo = {
  func<T>(T): T
}function foo(value) {
  return value;
}const f: Foo = { func: foo };
```

因为我们没有给我们的`foo`函数添加泛型类型标记。

一旦我们用泛型类型注释了我们的`foo`函数，它应该可以工作了:

```
type Foo = {
  func<T>(T): T
}function foo<T>(value: T): T {
  return value;
}const f: Foo = { func: foo };
```

# 句法

## 通用函数

我们可以定义一个通用函数如下:

```
function foo<T>(param: T): T {

}

function<T>(param: T): T {

}
```

此外，我们可以用泛型定义函数类型，如下所示:

```
<T>(param: T) => T
```

我们可以使用带有变量和参数的通用函数类型，如:

```
let foo: <T>(param: T) => T = function(param: T): T {}function bar(callback: <T>(param: T) => T) {

}
```

## 通用类

为了创建泛型类，我们可以在字段、方法参数和方法的返回类型中插入类型占位符。

例如，我们可以如下定义一个泛型类:

```
class Foo<T> {
  prop: T; constructor(param: T) {
    this.prop = param;
  } bar(): T {
    return this.prop;
  }
}
```

## 键入别名

泛型类型也可以添加到类型别名中。例如，我们可以写:

```
type Foo<T> = {
  a: T,
  v: T,
};
```

## 接口

同样，我们可以用泛型定义接口，如下所示:

```
interface Foo<T> {
  a: T,
  b: T,
}
```

# 传入类型参数

对于函数，我们可以按如下方式传入类型参数:

```
function foo<T>(param: T): T {  
  return param;
}foo<number>(1);
```

我们也可以将类型参数传递给类。当我们实例化一个类时，我们可以如下传入一个类型参数:

```
class Foo<T> {}
const c = new Foo<number>();
```

流泛型的一个方便的特性是我们不必自己添加所有的类型。我们可以用一个`_`代替一个类型，让 Flow 为我们推断类型。

例如，我们可以编写以下内容:

```
class Foo<T, U, V>{}
const c = new Foo<_, number, _>()
```

让 Flow 推断出`T`和`V`的类型。

![](img/b40c040ed10d827217a94c45d98ed6d0.png)

西蒙·雷伊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 行为

泛型是类型的变量。我们可以在代码中用它们代替任何数据类型注释。

此外，我们可以给它们起任何我们喜欢的名字，所以我们可以这样写:

```
function foo<Type1, Type2, Type3>(one: Type1, two: Type2, three: Type3) {

}
```

Flow 跟踪用泛型类型注释的变量和参数的值，所以我们可以给它赋值一些意想不到的东西。

例如，如果我们编写如下代码，我们会得到一个错误:

```
function foo<T>(value: T): T {  
  return "foo";
}
```

由于我们不知道如果`T`是一个字符串，我们总是可以在调用`foo`时返回一个字符串。

此外，Flow 跟踪我们通过泛型传递的值的类型，以便我们以后可以使用它:

```
function foo<T>(value: T): T {
  return value;
}let one: 1 = foo(1);
```

注意，我们没有为它传递任何泛型类型参数来标识类型为`1`。将`1`改为`number`也可以:

```
let one: number = foo(1);
```

如果我们省略了泛型函数中的泛型类型参数，Flow 将允许我们传入任何内容:

```
function logBar<T>(obj: T): T {
  if (obj && obj.bar) {
    console.log(obj.bar);
  }
  return obj;
}logBar({ foo: 'foo', bar: 'bar' });  
logBar({ bar: 'bar' });
```

我们可以通过为类型参数指定限制来限制可以传入的内容:

```
function logBar<T: { bar: string }>(obj: T): T {
  if (obj && obj.bar) {
    console.log(obj.bar);
  }
  return obj;
}logBar({ foo: 'foo', bar: 'bar' });  
logBar({ bar: 'bar' });
```

加上`{ bar: string }`之后，那么我们知道传入的任何东西都必须有 string `bar`属性。

我们可以对原始值做同样的事情:

```
function foo<T: number>(obj: T): T {
  return obj;
}foo(1);
```

如果我们尝试传入任何其他类型的数据，都会失败，所以

```
foo('2');
```

会给我们一个错误。

泛型允许我们返回比我们在类型参数中指定的更具体的类型。例如，如果我们有一个字符串函数，它返回传入的值:

```
function foo<T: string>(val: T): T {
  return val;
}let f: 'foo' = foo('foo');
```

我们可以把函数返回值作为类型，而不是把它赋给一个字符串变量。

# 参数化泛型

我们可以向泛型传递类型，就像向函数传递参数一样。我们可以用类型别名、函数、接口和类来做到这一点。这称为参数化泛型。

例如，我们可以创建一个参数化泛型类型别名，如下所示:

```
type Foo<T> = {
  prop: T,
}let item: Foo<string> = {
  prop: "value"
};
```

同样，我们可以对类做同样的事情:

```
class Foo<T> {
  value: T;
  constructor(value: T) {
    this.value = value;
  }
}let foo: Foo<string> = new Foo('foo');
```

对于接口，我们可以写:

```
interface Foo<T> {
  prop: T,
}class Item {
  prop: string;
}(Item.prototype: Foo<string>);
```

# 参数化泛型的默认类型

我们可以向参数化泛型添加默认值。例如，我们可以写:

```
type Item<T: string = 'foo'> = {
  prop: T,
};let foo: Item<> = { prop: 'foo' };
```

如果没有指定类型，那么假定`prop`具有`'foo'`类型。

这意味着如果我们将类型参数留空，那么`prop`的任何其他值都将不起作用。大概是这样的:

```
let foo: Item<> = { prop: 'bar' };
```

不会被接受。

# 方差符号

当转换泛型类型时，我们可以使用`+`符号来允许比赋值类型更广泛的类型。例如，我们可以写:

```
type Foo<+T> = T;let x: Foo<string> = 'foo';
(x: Foo<number| string>);
```

正如我们所见，我们可以将`x`从类型`Foo<string>`转换为类型`Foo<number| string>`，并在通用类型`Foo`上添加`+`符号。

使用泛型，我们可以通过用泛型类型标记替换它们来从代码中抽象出类型。我们可以在任何有类型注释的地方使用它们。此外，它可以处理任何流结构，如接口、类型别名、类、变量和参数。