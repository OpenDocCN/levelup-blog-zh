# JavaScript 最佳实践—类和函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-classes-and-functions-5c7f47a4b307>

![](img/d42c39582c623ccbfef451a17c9c79ce.png)

照片由[捕捉人心。](https://unsplash.com/@dead____artist?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

使用默认参数和属性缩写，清理我们的 JavaScript 代码很容易。

在本文中，我们将研究创建类和函数时的最佳实践。

# 避免创建神类

神类是全知的类。如果一个类主要用于使用 get 和 set 方法从其他类中检索数据，那么我们不需要这个类。

所以如果我们有这样的东西:

```
class Foo {
  getBar() {}
  setBar() {}
  getBaz() {}
  setBaz() {}
  getQux() {}
  setQux() {}
}
```

我们可以去掉类，直接访问我们需要的东西。

# 消除不相关的类

如果我们需要一个类，那么我们应该删除它。

这适用于只有数据的类。如果我们有这个类中的成员，那么我们应该考虑他们是否应该是另一个类的成员。

# 避免以动词命名的类

我们不应该用动词来命名类，因为只有行为而没有数据的类不应该是类。

例如，如果我们有:

```
class GetFoo {
  //...
}
```

那么应该是函数而不是类。

# 我们应该什么时候创建函数？

我们应该创建函数来使我们的代码更好。以下是创建函数的原因。

## 降低复杂性

降低复杂性是创建函数的最重要的原因之一。

我们必须创建它们，这样我们就不会重复编码，并最小化代码大小。

如果我们编写的所有过程都没有函数，那么我们会有很多相似的代码。

例如，如果我们有:

```
const greet1 = `hi joe`;
const greet2 = `hi jane`;
```

然后我们可以用一个函数来概括:

```
const greet = (name) => `hi ${name}`;
const greet1 = greet('joe');
const greet2 = greet('jane');
```

现在我们可以在任何地方调用`greet`来创建类似的字符串。

# 引入一个中间的、可理解的抽象

上面的代码也是一个抽象。我们概括了返回问候字符串的代码。

我们可以传入不同的值`name`并返回一个新的字符串。

# 避免重复代码

避免重复代码也是创建函数的一个很好的理由。

同样，从上面的例子中我们可以看到，我们可以调用`greet`来创建更多相同的字符串。

那我们就不用到处重复`hi`部分了。

# 支持子类化

我们可以在子类中创建一个方法来覆盖超类中的方法。

例如，我们可以用 JavaScript 编写以下代码来实现这一点:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} speaks`;
  }
}class Dog extends Animal {
  constructor(name) {
    super(name);
  }
  speak() {
    return `${super.speak(this.name)} in dog language`;
  }
}
```

我们通过调用`super.speak`覆盖了`Dog`中的`speak`方法。

因此，我们可以保持`Dog`中的`speak`方法更简单。

![](img/5e6ef05b3897e41d7375354e3c304955.png)

丹尼尔·塞鲁洛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 隐藏序列

函数有利于隐藏过程的实现。

例如，我们可以有一个函数调用其他函数并对它们做一些事情。

我们可以写:

```
const calcWeight = () => {
  //...
}const calcHeight = () => {
  //...
}const calcWidth = () => {
  //...
}const getAllDimensions = () => {
  const weight = calcWeight();
  //...
  const height = calcHeight();
  //...
  const width = calcWidth();
  //...
}
```

我们在上面的代码中调用了多个函数。但是我们程序的其他部分不知道它们被调用的顺序。

这减少了公开的实现并减少了耦合。

## 提高便携性

功能可以很容易地移动到任何地方。我们可以很容易地移动它和其他依赖它的东西。

## 简化复杂的布尔测试

如果我们有很长的布尔表达式，那么我们应该把它们放入自己的函数中。

例如，如果我们有:

```
const isWindowsIE = navigator.userAgent.toLowerCase().includes('windows') &&
  navigator.userAgent.toLowerCase().includes('ie');
```

然后我们可以把整个事情写成一个函数:

```
const isWindowsIE = () => {
  const userAgent = navigator.userAgent.toLowerCase()
  return userAgent.includes('windows') &&
    userAgent.includes('ie');
}
```

这比使用长布尔表达式要干净得多。

# 结论

神类，只有数据或者行为的类都是不好的。我们应该确保在创建两者之前有一个混合。

我们创建函数来降低复杂性，删除重复的代码，并进行抽象。

他们擅长创造任何东西。