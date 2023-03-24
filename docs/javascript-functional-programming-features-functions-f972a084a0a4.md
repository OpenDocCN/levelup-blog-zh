# Javascript 函数式编程特性—函数

> 原文：<https://levelup.gitconnected.com/javascript-functional-programming-features-functions-f972a084a0a4>

![](img/64f7bb497e275e625c0a42479d6ef688.png)

照片由 [Chandan Chaurasia](https://unsplash.com/@chaurasia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。它还使用了许多函数式编程特性，使我们的生活更加轻松。

在本文中，我们将研究 JavaScript 函数的函数式编程特征。

# 高阶函数

高阶函数是接受其他函数参数或在其结果中返回函数的函数。

例如，这意味着 JavaScript 代码可以接受回调或返回函数。

高阶函数的例子包括数组实例的`map`方法，我们可以通过编写如下代码来调用它:

```
const arr = [1, 2, 3].map(a => a * 2);
```

在上面的代码中，我们用回调函数`a => a * 2`调用了`map`方法，

然后`map`方法将调用回调，通过将每个条目加倍，将每个条目从其原始值映射到新值，将每个映射的条目添加到新数组中，并返回新数组。

还有许多其他的数组实例方法接受回调，包括`some`、`every`、`find`、`findIndex`、`filter`等。

我们可以通过编写如下代码来创建一个返回函数的函数:

```
const returnAdd = () => (a, b) => a + b;
const result = returnAdd()(1, 2);
```

在上面的代码中，我们有`returnAdd`函数，它返回一个将两个数相加的函数。

然后我们可以通过先调用`returnAdd`函数返回加数的函数，再调用返回的函数加数并返回和，来调用返回的函数加 2 数。

因此，上例中的`result`应该是 3。

# 一级功能

一级函数是像对待任何其他对象一样对待的函数。它们可以存储在变量中，传递，从其他函数返回，并拥有自己的属性。

从上一节我们可以看到，函数可以传递给另一个函数，函数也可以作为函数中的参数返回。

此外，它们还可以具有属性。例如，我们可以通过编写如下代码来设置函数的属性:

```
const foo = () => {};
foo.bar = 1;
```

在上面的例子中，我们在第一行定义了`foo`函数。然后我们在`foo`函数上创建`bar`属性，然后将其值设置为 1。

当我们记录`foo.bar`的值时，我们应该看到`foo.bar`是 1。

正如我们所见，函数就像任何对象一样，它们可以有自己的属性。

当我们需要创建方法的静态属性时，这对于构造函数更有用。

例如，如果我们有下面的构造函数:

```
function Person(name) {
  this.name = name;
}
```

然后我们可以为它定义一个静态方法，如下所示:

```
Person.greet = () => console.log('hello');
```

现在我们有了一个静态的`greet`方法，我们可以在`Person`上调用它。那么我们可以这样称呼它:

```
Person.greet();
```

我们看到`'hello'`已经登录到控制台。

正如我们在前面的例子中看到的，可以做同样的事情来为函数添加属性。

因为 JavaScript 类只是构造函数上的语法糖。我们可以像定义构造函数一样定义 JavaScript 类的静态成员。

例如，如果我们有下面的类:

```
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

然后我们可以在它上面定义一个静态方法如下:

```
Person.greet = () => console.log('hello');
```

![](img/4c145bd36a1b4a1d07c7dd769fdba774.png)

Borna Bevanda 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 递归

递归是函数调用自身的地方。像大多数语言一样，我们可以定义一个递归函数。许多循环可以用递归方式重写。在某些函数式语言中，使用递归是循环遍历项的默认方式。

然而，它们更难理解，所以为了使阅读代码更容易，我们坚持使用循环和数组方法来迭代和操作数据。

例如，我们下面的循环:

```
for (let i = 0; i <= 4; i++) {
  console.log(i);
}
```

产生与以下递归函数相同的结果:

```
const loop = (i = 0) => {
  console.log(i);
  if (i === 4) {
    return;
  }
  loop(i + 1);
}loop();
```

正如我们所看到的，循环要短得多，也更容易理解。

使用递归`loop`函数，我们必须像使用`for`循环一样运行`console.log`，但是还必须检查`i`为 4 时的基本情况，然后如果`i`达到 4，我们必须结束循环。

否则，我们让函数调用它自己。

这对于遍历不确定深度级别的树结构更有用。

# 结论

在 JavaScript 中，函数和其他对象一样。我们可以给它设置属性，把它作为参数传入，函数可以返回函数。

递归也是函数式编程的一个重要部分。我们可以用它来创建一个调用自身的函数。