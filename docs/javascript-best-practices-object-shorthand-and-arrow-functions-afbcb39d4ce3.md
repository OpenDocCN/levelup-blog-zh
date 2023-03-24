# JavaScript 最佳实践—对象速记和箭头函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-object-shorthand-and-arrow-functions-afbcb39d4ce3>

![](img/b83c566b76f3d56dc432fb0aa3dc7fed.png)

照片由[船桅](https://unsplash.com/@japhethmast?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究使用对象文字速记语法、回调的箭头函数，以及在大多数情况下使用`const`来防止意外的重新分配。

# 使用对象文字速记语法

我们应该使用对象文字速记语法来用属性填充我们的对象。

有两种快捷方式可用于填充对象中的对象属性。

一个是对象属性速记，另一个是方法速记。

要使用 object 属性简写，我们可以编写类似下面的代码:

```
const a = 1,
  b = 2,
  c = 3;const obj = {
  a,
  b,
  c
};
```

在上面的代码中，我们有常量`a`、`b`和`c`。

然后我们用属性`a`、`b`和`c`填充`obj`对象，这些属性的值与同名变量的值相同。

上面的代码等效于下面的代码:

```
const a = 1,
  b = 2,
  c = 3;const obj = {
  a: a,
  b: b,
  c: c
};
```

正如我们所看到的，我们用速记法删除了重复的代码。省去了我们打字，看起来更干净。

对于方法，我们可以用如下的简写来声明它们。例如，我们可以使用以下代码向我们的对象添加方法:

```
const obj = {
  foo() {},
  bar() {}
};
```

在上面的代码中，我们通过使用简写将`obj`对象中的`foo`和`bar`方法声明为传统函数，这让我们可以在不使用`:`和`function`关键字的情况下将传统函数声明为对象中的方法。

我们知道它们是传统函数，因为我们可以在其中引用`this`。例如，我们可以编写以下代码来实现这一点:

```
const obj = {
  baz: 'baz',
  foo() {
    console.log(this.baz);
  },
  bar() {}
};
```

在上面的代码中，我们记录了`this.baz`的值，其中`this`是上面代码中的`obj`。

那么当我们这样称呼它的时候:

```
obj.foo();
```

我们将`'baz'`登录到控制台。因此，我们知道`foo`方法内部的`this`是`obj`对象，所以我们知道`foo`方法是一个传统的 JavaScript 函数。

这比它的旧版本要短得多，旧版本如下:

```
const obj = {
  baz: 'baz',
  foo: function() {
    console.log(this.baz);
  },
  bar: function() {}
};
```

正如我们从上面的代码中看到的，我们必须键入更多的字符来声明上面代码中的方法，关键字`function`是 8 个额外的字符，我们在两个方法中都有它。

我们还可以缩短生成器函数的声明。以下是在 JavaScript 对象中声明生成器方法的简写:

```
const obj = {
  * foo() {
    yield 1;
  },
};
```

上面的代码有一个符号`*`,表示`foo`是一个生成器函数，而不是一个常规函数。

它是以下代码的简写:

```
const obj = {
  foo: function*() {
    yield 1;
  },
};
```

如我们所见，它要短得多。

因此，我们应该尽可能使用快捷键来填充我们的对象属性，这样我们可以输入更少的内容，使我们的代码更整洁，同时保持代码清晰。

![](img/aeebd046e0541fd54ddb281b895409b8.png)

[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 为回调使用箭头函数

使用箭头函数进行回调是一个好主意。我们不必担心箭头函数中`this`的值，因为它并不介意。

它也不那么冗长，并且它绑定到它们周围范围内的`this`，而不是绑定到它们自己的`this`值，这与常规函数不同。

如果我们不需要在回调中为`this`指定一个特定的值，这是最有可能的情况，那么我们可以在回调中使用箭头函数。

例如，不用编写下面的代码:

```
const arr = [1, 2, 3].map(function(x) {
  return x * 2;
})
```

我们可以用一个箭头函数作为回调函数编写如下代码:

```
const arr = [1, 2, 3].map(x => x * 2);
```

正如我们所看到的，每段代码中的字符数有很大的不同。他们做同样的事情，但第一个例子比第二个长得多。

我们隐式返回`x * 2`，因为我们在第一行返回了结果。此外，我们不必使用`function`和`return`关键字来定义我们的函数并返回一些内容。

由于它没有引用`this`的具体值，所以使用箭头函数是正确的选择。

# 结论

只要有可能，我们就应该使用对象快捷键来填充对象的属性。值属性和方法都有缩写。

使用 arrow 函数进行回调是一个很好的选择，因为它节省了大量的输入，并且不会绑定到自己的值`this`。