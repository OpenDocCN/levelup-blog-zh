# JavaScript 最佳实践——定义函数并调用它们

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-defining-functions-and-calling-them-a10b7e6142c1>

![](img/363dae7a45e67a540173adfe7c2d86fe.png)

照片由[马特布里内](https://unsplash.com/@mbriney?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将了解如何在 JavaScript 代码中定义函数并调用参数数量可变的函数。

# 永远不要使用函数构造函数来创建新函数

在 JavaScript 中，`Function`构造函数让我们定义一个带有参数字符串和函数体的函数。

除最后一个参数外，所有参数都是参数，以字符串形式传入。最后一个参数是函数体，它也作为字符串传入。

`Function`构造函数返回一个只在全局范围内运行的函数。

例如，我们可以定义一个函数如下:

```
const add = new Function('a', 'b', 'return a + b');
```

在上面的代码中，我们用被定义为字符串的参数`a`和`b`以及函数体`return a + b`调用了`Function`构造函数。

这很糟糕，因为一切都被定义为字符串，这意味着它们很难跟踪和调试。此外，有人可能会将恶意字符串传递到`Function`构造函数中，从而运行恶意代码。

所有返回的函数也在全局范围内运行，这可能不是我们想要的。

因此，我们不应该运行任何使用`Function`构造函数的代码。

# 函数签名中的间距

函数签名中的间距应该是标准化的和一致的。`function`关键字或函数名后面不应该有空格。应该有一个右括号和左花括号。

例如，我们应该编写类似下面的代码:

```
function() {}
function add() {}
```

这样，我们可以很容易地阅读名字，括号也很容易阅读。

# 永远不要改变参数

改变参数总是不好的。因为对象是通过引用传递的，所以它们也会变异外部变异的参数。

例如，如果我们编写以下代码:

```
const foo = (a) => a.foo = 2;
let a = {
  foo: 1
};
foo(a);
console.log(a);
```

然后在上面的代码中，我们在我们的`foo`函数中有了`a`参数。在函数内部，我们将`a.foo`设置为 2。

那么当我们用下面的等式定义变量`a`时:

```
{
  foo: 1
}
```

接下来当我们调用`foo(a)`时，我们得到`a`是`{foo: 2}`，因为我们改变了参数，这被传播到`a`变量本身，因为`a`是通过引用`foo`函数传入的。

相反，我们应该做的是，在函数内部分配属性值变量，然后对其进行变异。

例如，我们可以这样做:

```
const foo = (a) => {
  let bar = a.foo;
  bar = 2;
};let a = {
  foo: 1
};
foo(a);
console.log(a);
```

这样，我们得到`a`是:

```
{
  foo: 1
}
```

这就是我们最初传递的内容。在上面的代码中，我们将`a.foo`赋值给`bar`，并将`bar`的值设置为 2。

![](img/0f516c6f0e7e029e5091b0846171653c.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 更喜欢使用扩展运算符`... to Call Functions with Variable Number of Parameters`

随着 spread 操作符的引入，我们可以使用它来调用带有可变参数数量的函数，方法是通过 spread 操作符将数组扩展到方法的参数中。

这比老办法好多了，老办法是在函数上调用`apply`来调用参数个数可变的函数。

例如，我们可以调用`Math.max`方法从一个数字数组中获取最大数字，如下所示:

```
const max = Math.max(...[1, 2, 3]);
```

在上面的代码中，我们通过使用 spread 操作符用一个数组调用了`Math.max`。扩展操作符将数组`[1, 2, 3]`扩展到`Math.max`方法的参数中，

然后我们得到 3 作为`max`的结果。

这比老方法好多了，老方法是按如下方式使用`apply`:

```
const max = Math.max.apply(undefined, [1, 2, 3]);
```

在上面的代码中，我们用两个参数调用了`apply`，第一个参数是`this`的值，它可以是任何值，因为它是一个静态函数，但是我们选择了`undefined`。第二个参数是我们想要传递给`Math.max`的参数数组。

然后我们得到同样的结果。正如我们所看到的，这是一个更复杂的方法来获得相同的结果。

# 结论

我们不应该使用`Function`构造函数来创建函数。此外，我们应该使用 spread 操作符来调用参数数量可变的函数，而不是使用`apply`。