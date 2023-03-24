# JavaScript 最佳实践—箭头函数和构造函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-arrow-functions-and-constructors-7b219149cd22>

![](img/5e42e07e5cc6328a6c41c70fa2a5d0d1.png)

照片由[缺口缺口](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在[缺口](https://unsplash.com?utm_source=medium&utm_medium=referral)处拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究箭头函数的签名、间距，并记住在子类构造函数中调用`super`。

# 箭头函数参数

当我们定义箭头函数时，如果我们的函数只有一个参数，就不需要括号。

例如，我们可以编写以下代码来返回乘以 2 的结果:

```
const double = x => x * 2;
```

在上面的代码中，我们的`double`函数有一个参数`x`并返回乘以 2 的`x`。

正如我们所看到的，我们没有在签名周围加上括号，因为如果 arrow 函数只有一个参数，就不需要括号。

只有一个参数的 arrow 函数的括号是可选的。我们可以把它放进去，如果我们认为它使阅读功能更清楚。例如，我们可以编写以下内容:

```
const double = (x) => x * 2;
```

这与我们在前面的例子中看到的是一样的。

如果我们的 arrow 函数没有一个参数，那么括号是必需的。例如，我们可以编写以下函数:

```
const foo = () => {};
const add = (a, b) => a + b;
```

在上面的代码中，我们有没有参数的`foo`函数，所以函数签名必须用括号括起来，以表明它没有参数。

另一个例子是`add`函数，它有两个参数，所以我们需要用括号将`a`和`b`参数括起来。

# 箭头函数箭头前后的空格

箭头函数有一个粗箭头作为其函数定义的一部分。通常，我们在粗箭头的前后有一个空格字符。

例如，我们通常将箭头函数定义如下:

```
const foo = () => {};
```

在上面的代码中，我们有`foo`函数，它在`=>`粗箭头的前后都有一个空格字符。

这种间距使我们的函数定义更加清晰，并且隔开的文本比没有间距的文本更容易阅读。

# `Remember to Call super()`在构造函数中

如果我们在 JavaScript 中有一个类扩展了另一个类，那么我们必须记住调用`super`，这样我们在运行代码时就不会出错。

例如，如果我们有以下代码:

```
class Animal {
  constructor(type) {
    this.type = type;
  }
}class Cat extends Animal {
  constructor() {}
}const cat = new Cat();
```

然后当我们运行上面的代码时，我们会得到一个错误，告诉我们调用超级构造函数，类似于' un catch reference error:在访问' this '或从派生构造函数返回之前，必须调用派生类中的`super` 构造函数'。

这很好，因为我们不会忘记调用`Cat`中的`super`，因为代码不会运行。

因此，我们应该通过编写以下代码来纠正这个错误:

```
class Animal {
  constructor(type) {
    this.type = type;
  }
}class Cat extends Animal {
  constructor(type) {
    super(type);
  }
}const cat = new Cat();
```

在上面的代码中，我们在`Cat`类的构造函数中调用了`super`。

尽管 JavaScript 类只是构造函数的语法糖，但它确实能防止我们犯在我们有类语法之前容易犯的错误。

我们使用`extends`关键字和`super`函数，而不是在父构造函数上调用`call`方法，而是使用`call`方法在子构造函数中调用父构造函数。

正如我们所看到的，有错误检查来确保我们调用了`super`，从而防止我们走错方向。否则，代码不会运行。

使用旧的构造函数语法，没有办法判断我们是否做错了什么。忘记调用父构造函数不会给我们带来旧语法的任何错误。我们只会得到意想不到的行为。

![](img/a404ef21017d336ec6f2354845d12d3a.png)

劳拉·德维尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

如果我们的箭头函数只有一个参数，箭头函数签名可能不需要括号。

否则，如果我们的 arrow 函数有多个参数或者没有参数，那么我们就需要括号。

如果我们忘记在子类的构造函数中调用`super`，我们会得到一个错误，这样我们就不会忘记调用它，因为代码不会运行。

这是类语法的一大优点，因为它有内置的错误检查功能。