# TypeScript 函数介绍(第 1 部分)

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-functions-part-1-e1257c33a3f6>

![](img/26d3a7afbc0a5e5307320df20fc50418.png)

米切尔·奥尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

函数是一些小的代码块，接受一些输入，可能返回一些输出或者有副作用。副作用是当一个函数修改了函数外部的变量。我们需要函数将代码组织成可重用的小块。如果没有函数，如果我们想重新运行一段代码，我们必须在不同的地方复制它。函数对于任何类型脚本程序都是至关重要的。在本文中，我们将了解如何定义 TypeScript 函数，如何向函数添加类型，以及如何以不同的方式传入参数。

# 定义函数

要在 TypeScript 中定义函数，我们可以执行以下操作:

```
function add(a: number, b: number){
  return a + b;
}
```

上面的函数将两个数相加并返回总和。`a`和`b`是参数，它让我们将参数传递给函数来计算和组合它们。`add`是函数的名称，在 JavaScript 中是可选的。`return`语句让函数的结果被发送到另一个变量或作为另一个函数的参数。这不是定义函数的唯一方法。另一种方法是把它写成一个箭头函数:

```
const add = (a: number, b: number) => a + b;
```

这两者是等价的。然而，如果我们必须在函数中操作`this`，那么这两个就不一样了，因为 arrow 函数不会像用`function`关键字定义的函数那样改变函数中`this`的值。我们可以将函数赋给变量，因为函数在 TypeScript 中是对象。请注意，上面的函数只有在一行的情况下才有效，因为如果 arrow 函数是一行的话，返回是隐式完成的。如果它不止一行，那么我们用括号写出来，就像这样:

```
const add = (a: number, b: number) => {
  return a + b;
}
```

这样我们必须显式地编写`return`语句。否则，它不会返回值。

在上面的两个例子中，我们都有参数，每个参数后面都有类型。这是因为在 TypeScript 中，如果参数后面没有类型，那么该类型将被推断为具有`any`类型，如果我们在编译代码时设置了`noImplicitAny`标志，这将被拒绝。

# 功能类型

为了对传入的参数类型以及函数的返回类型添加更多的检查，我们可以显式地为函数添加一个类型标志。我们可以通过编写签名，然后添加粗右箭头，然后在其后追加函数的返回类型来做到这一点。例如，我们可以编写以下代码来指定我们的`add`函数的类型:

```
const add: (a: number, b: number) => number =
  (a: number, b: number) => a + b;
```

`add`功能的类型名称为:

```
(a: number, b: number) => number
```

在上面的代码中。如果我们为参数设置的类型不同于我们编写的类型所指定的类型，那么 TypeScript 编译器将引发错误并拒绝编译代码。例如，如果我们写:

```
const add: (a: number, b: number) => number =
  (a: number, b: string) => a + b;
```

然后我们得到错误:

```
Type '(a: number, b: string) => string' is not assignable to type '(a: number, b: number) => number'.Types of parameters 'b' and 'b' are incompatible.Type 'number' is not assignable to type 'string'.(2322)
```

从 TypeScript 编译器。如果我们不显式写出类型，TypeScript 仍然足够智能，可以从赋值操作符右侧给出的内容推断出类型，所以我们不必显式写出它。它节省了我们使用 TypeScript 键入所有内容的精力。

# 调用函数

如果我们引用并使用一个函数，那么我们就是在**调用**一个函数。

为了调用该函数，我们编写:

```
add(1, 2) // 3
```

由于我们的函数返回值，如果我们控制台记录了`add`函数的返回值:

```
console.log(add(1, 2)) // logs 3
```

我们也可以将返回值赋给一个变量:

```
const sum = add(1, 2);
console.log(sum) // logs 3
```

# 功能的一部分

所有功能都有以下部分或全部:

*   `function`关键字，可选
*   函数名，这是可选的
*   括号—这是任何函数都需要的。
*   括号内的参数，这是可选的
*   左花括号和右花括号—除了单行箭头函数之外，所有函数都需要
*   `return`语句，后者是可选的。如果一个函数没有`return`语句，它将返回`undefined`。一个后面没有任何东西的`return`语句将结束函数的执行，因此它对于控制函数的流程很方便。

# 使用参数

正如我们所见，许多函数都有参数。参数是传递给函数进行计算的数据，所以如果我们有一个函数调用`add(1, 2)`，那么 1 和 2 就是参数。

另一方面，**参数**是我们在定义函数时写在括号中的内容，用来说明我们可以传入什么作为参数。

我们不必定义函数参数来传递参数，因为我们在每个函数中都有`arguments`对象。但是，不建议这样做，因为不清楚您想传入什么。然而，我们可以使用`arguments`来传递我们想要传递的可选内容。

例如，如果我们回到上面定义的`add`函数:

```
function add(a: number, b: number){
  return a + b;
}
```

`a`和`b`是参数。当我们把它写成`add(1, 2)`的时候。1 和 2 是自变量。

定义函数时，我们可以指定多达 255 个参数。然而，我们通常不应该定义超过 5 个，因为它变得难以阅读。

# 通过值传入参数

在 TypeScript 中有两种传入参数的方法。一种方法是通过值传递参数。按值传递意味着传入的参数与传入的变量完全分离。

如果我们将一个变量传递给一个函数，变量的内容将被复制到参数中，并且与原始变量完全分离。

即使我们改变传入的参数变量，原始变量也不会改变。

像字符串、数字、布尔、未定义和`null`对象这样的原始数据类型是通过 TypeScript 中的值传入的。

例如，如果我们有以下代码:

```
const a = 1;
const addOne = (num: number) => num + 1
const b = addOne(a);
console.log(b);
```

即使我们在`a`上调用`addOne`之后,`a`仍然是 1，因为我们复制了`a`并在将`a`作为调用`addOne`的参数传入时将其设置为`num`。

# 通过引用传入参数

非原语通过引用传递到函数中，这意味着对作为参数传递到函数中的对象的引用。没有为参数复制内容，而是直接修改传入的对象。

例如，如果我们有以下代码:

```
let changeObj = (obj: { foo: string }) => obj.foo = 'bar'
const obj = {
  foo: 'baz'
}
changeObj(obj);
console.log(obj); // logs {foo: "bar"}
```

原来的`obj`对象被定义为`{ foo: 'baz' }`。然而，当我们将`obj`传递给`changeObj`函数时，传递的`obj`参数被就地改变。我们传入的原始`obj`对象被更改。`obj.foo`变成了`'bar'`而不是最初定义的`'baz'`。

![](img/5cb3bab97386b0d7a3cdab54427763b5.png)

由[马克斯·巴斯卡科夫](https://unsplash.com/@snowboardinec?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 缺少参数

在 TypeScript 中，您不需要将所有参数传递给函数。任何没有传入的参数都将在它的位置设置`undefined`。所以如果你没有传入所有的参数，那么你必须检查参数中的`undefined`，这样你就不会得到意想不到的结果。例如，我们有一个`add`函数:

```
function add(a, b){
  return a + b;
}
```

如果我们调用`add`而没有第二个参数，写为`add(1)`，那么我们得到`NaN`，因为 1 + `undefined`不是一个数字。

# 默认功能参数值

我们可以为可选参数设置默认函数参数，以避免意外结果。使用`add`函数，如果我们想让`b`可选，那么我们可以为`b`设置一个默认值。最好的方法是:

```
function add(a: number, b: number = 1){
  return a + b;
}
```

在上面的代码中，我们将`b`设置为 1，这样如果我们没有为`b`传入任何东西，默认情况下我们会自动为`b`参数获取 1。所以如果我们运行`add(1)`，我们得到的是 2 而不是`NaN`。

一种更老的替代方法是检查参数的类型是否是`undefined`，如下所示:

```
function add(a: number, b: number){
  if (typeof b === 'undefined'){
    b = 1;
  }
  return a + b;
}
```

这与第一个函数达到了相同的目的，但是语法更笨拙。

TypeScript 函数允许我们将代码组织成可以重用的小部分。有许多方法来定义一个函数，但是坚持通常推荐的方法，比如使用箭头函数，并且不要过多地使用`arguments`。我们将在本系列的下一部分继续研究 TypeScript 函数。