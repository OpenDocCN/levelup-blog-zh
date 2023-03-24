# 对于重构来说，简单的事情可以快速取胜

> 原文：<https://levelup.gitconnected.com/easy-things-that-can-be-quick-wins-for-refactoring-a3c1cd488ef1>

![](img/8054be2f29dbc728134ed6e05281d77e.png)

照片由[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在这篇文章中，我们将会看到一些简单的事情，即使是最丑陋的代码也可以轻松重构。

# 没有不变函数

不变函数是给定任何类型的输入，总是返回相同内容的函数。

显然，这是没有用的，因为这个函数总是返回相同的东西。

对于不变函数，我们要么将返回值指定为常量，要么改变函数，返回不同类型的输出。

例如，如果我们有以下函数:

```
const foo = name => 'name';
```

那么这应该被重构，因为函数总是返回`'name'`。我们不想要那样的函数，因为参数是无用的，返回值是常量。

一个产生副作用但总是返回相同结果的函数也是一个不变函数。

例如，如果我们有:

```
let name;
const setName = (firstName, lastName) => {
  name = `${firstName}, ${lastName}`;
  return 'name';
}
```

那么这也是一个不变函数，因为它返回相同的东西。在那之前它做什么并不重要。

在这个例子中，我们应该重构我们的函数，使它成为一个纯函数。

例如，我们应该将`setName`函数重写为一个纯函数，并如下调用它:

```
const setName = (firstName, lastName) => `${firstName}, ${lastName}`
```

在上面的代码中，我们将`setName`函数改为一个纯函数，返回组合的`firstName`和`lastName`参数。

现在我们不再有不变函数了，因为它并不总是返回相同的东西。

同样，现在我们可以这样称呼它:

```
let name = setName('jane', 'smith');
```

这比产生不必要的副作用要好。

纯函数易于测试和理解，因为给定输入，输出是可预测的。

# 将 switch 语句的 Default 子句放在最后

`switch`语句的`default`子句应遵循公认的惯例。这使得我们的代码更容易阅读，因为它的一致性和模式是可预测的。从而减轻读者的认知负担。

因此，与其写:

```
const foo = (bar) => {
  switch (bar) {
    default: {
      return 1
    }
    case 'foo': {
      return 2
    }
    case 'bar': {
      return 3;
    }
  }
}
```

我们应该改为写:

```
const foo = (bar) => {
  switch (bar) {
    case 'foo': {
      return 2;
    }
    case 'bar': {
      return 3;
    }
    default: {
      return 1;
    }
  }
}
```

这对任何人来说都更容易阅读，因为我们坚持被大多数人接受的惯例。

![](img/2b4118ad3d844d69dffb27b76bd841b7.png)

图片由[诚信公司](https://unsplash.com/@honest?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 清除冗余变量

冗余变量对我们的大脑来说很难。此外，由于浏览器或 Node.js 必须为每个变量分配更多的资源，因此它们会占用用户计算机上更多的内存。

代码的混乱使得阅读和调试变得困难，因为它们容易引起误解。它们看起来不同，但实际上是用于同一件事。

例如，不写:

```
const foo = (bar) => {
  switch (bar) {
    case 'foo': {
      const two = 2;
      return two * 2;
    }
    case 'bar': {
      const three = 3;
      return three * 2;
    }
    default: {
      const one = 3;
      return one * 2;
    }
  }
}
```

其中有三个我们不需要的常数。我们可以改为写:

```
const foo = (bar) => {
  let num;
  switch (bar) {
    case 'foo': {
      num = 2;
      return num * 2;
    }
    case 'bar': {
      num = 3;
      return num * 2;
    }
    default: {
      num = 3;
      return num * 2;
    }
  }
}
```

在上面的代码中，我们有`num`变量，它被分配到每个`case`块和`default`块中。

然后我们可以在设置好变量后按照我们希望的方式使用`num`。现在我们只有 1 个变量，而不是 3 个，节省了计算机内存，也减少了读者的认知负荷。

# 结论

说到重构，有一些立竿见影的方法。一是去除冗余变量。

我们也不应该将`switch`语句的`default`块从底部移到它通常被接受的位置之外。

最后，我们不应该写不变函数，即使它们会带来副作用。