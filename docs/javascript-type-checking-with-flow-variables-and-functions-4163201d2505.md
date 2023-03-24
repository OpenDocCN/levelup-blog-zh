# 使用流检查 JavaScript 类型——变量和函数

> 原文：<https://levelup.gitconnected.com/javascript-type-checking-with-flow-variables-and-functions-4163201d2505>

![](img/6846fa438e48117679ea1c7f80d845fd.png)

史蒂夫·奥迪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Flow 是一个由脸书开发的类型检查器，用于检查 JavaScript 数据类型。它有许多内置的数据类型，我们可以用它们来注释变量和函数参数的类型。

在本文中，我们将研究流支持的用于类型检查的数据类型，包括变量和函数的基本属性。

# 变量类型

就像在 JavaScript 中一样，我们可以用 Flow 分别用`let`、`var`和`const`关键字声明变量和常量。

`let`和`var`用于声明变量。这意味着它们的值可以在初始赋值后重新赋值。

## 常数

`const`关键字用于声明常量。一旦它被赋予一个值，它可以被重新赋予另一个值。

流可以在不添加类型注释的情况下推断数据类型常量:

```
const a = 1;
```

它会知道`a`是一个数字。

当然，我们可以用如下类型对其进行注释:

```
const a: number = 1;
```

## var 和 let

`var`和`let`都是用来给变量赋值的。区别在于`var`不是块范围的，而`let`是块范围的。

例如，我们可以写:

```
if (true){
  var x = 1;
}
console.log(x);
```

然而，如果我们用`let`替换`var`，我们会得到一个错误:

```
if (true){
  let x = 1;
}
console.log(x);
```

我们可以用另一个值重新分配用`var`或`let`声明的变量。例如，我们可以写:

```
var x = 1;
x = 2;let y = 1;
y = 2;
```

当然，如果一个数据类型注释被添加到一个变量中，那么我们必须为它分配一个兼容类型的值。例如，如果我们有一个数字变量:

```
let y: number = 1;
```

然后写道:

```
y = '2';
```

之后就会产生一个错误。

如果没有将数据类型注释添加到变量或常数中，流程将假设变量或常数的类型是已经分配给它的所有数据类型的联合。

例如，如果我们有:

```
let a = 1;
a = 'foo';
a = true
```

然后我们可以写:

```
let b: number|string|boolean = a;
```

没有任何错误。这是因为在将`a`赋值给`b`之前，我们已经将一个数字、字符串和布尔赋值给了`a`，所以`a`被认为具有所有这些类型的联合。

Flow 还可以计算出它被赋予的类型。例如，我们可以写:

```
let foo = 1;
let num: number = foo;foo = false;
let boo: boolean = foo;foo = "foo";
let str: string = foo;
```

然而，在像函数和条件这样的块中运行的代码将抛出流的类型推断，并且当我们试图将它分配给我们期望工作的东西时将抛出一个错误。

例如，如果我们写:

```
let foo = 1;if(true) {
  foo = true;
  foo = "foo";
}let str: string = foo;
```

我们将得到错误:

```
[Flow] Cannot assign `foo` to `str` because number [1] is incompatible with string [2].
```

因为 Flow 不知道`foo`变量的类型。

同样，在函数中被重新赋值后，Flow 也无法判断出`foo`的类型，如下所示:

```
let foo = 1;const fn = ()=> {
  foo = true;
  foo = "foo";
}fn();let str: string = foo;
```

![](img/54e443a27a82ab905381b42c93627bb1.png)

照片由[卡拉·富勒](https://unsplash.com/@caraventurera?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 功能类型

函数可以对其参数和函数的返回类型进行类型注释。

例如，如果我们有函数:

```
function add(a: number, b: number): number {
  return a + b;
}
```

那么`a: number`和`b: number`中的`number`就是参数的数据类型，右括号后的`number`就是函数`add`的返回类型。

一旦我们用类型注释了参数，当有任何东西传入时，Flow 将验证类型。

例如，如果我们有:

```
add(1, 2);
```

那么 Flow 将接受代码是有效的，因为我们按照函数定义中的指示传入了数字。

另一方面，如果我们传入任何其他类型的数据，如字符串，如下所示:

```
add('a', 'b');
```

然后我们得到错误:

```
[Flow] Cannot call `add` with `'b'` bound to `b` because string [1] is incompatible with number [2].
```

因为我们为参数传入了字符串，这与数字不兼容。

# 函数语法

Flow 的函数语法类似于 JavaScript，但是向参数和返回类型添加了额外的类型注释。

我们可以用 rest 操作符拥有无限数量的参数，它用`...`操作符表示，并将超出所列参数的参数保存为一个数组。

例如，我们可以写:

```
function foo(str: string, bool?: boolean, ...nums: Array<number>): void {
  console.log(nums);
}foo('a', false, 1, 2, 3);
```

然后我们得到`[1,2,3]`对应`nums`。

但是，如果我们传入如下无效的内容:

```
foo('a', false, true, 2, 3);
```

然后，由于我们指定`...nums`是一个数字数组，所以我们在将`true`传入第三个参数时会得到一个错误。

我们可以像 JavaScript 一样用 Flow 声明变量和常量。除了 JavaScript 的语法，我们还可以给变量和常量添加数据类型注释。

函数的语法和定义也是从 JavaScript 的语法扩展而来的。像变量一样，我们可以向变量添加数据类型注释，包括由 rest 操作符操作的变量，还可以向函数添加返回类型注释。