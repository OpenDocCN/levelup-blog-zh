# JavaScript 的优点——递归和作用域

> 原文：<https://levelup.gitconnected.com/good-parts-of-javascript-recursion-and-scope-c7a462a6f94>

![](img/3a0da3138d7170e6d57fe81edf9ae442.png)

照片由 [Frank Albrecht](https://unsplash.com/@shotaspot?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 是世界上最流行的编程语言之一。它可以做很多事情，并且有一些领先于许多其他语言的功能。

在本文中，我们将研究函数调用本身的方法和函数的作用域。

# 递归

递归函数是调用自身的函数。

我们可以用它把一个问题分成一组子问题，每个子问题都有一个简单的解决方案。

例如，我们可以创建如下递归函数:

```
const count = (i) => {
  if (i === 10) {
    return;
  }
  console.log(i);
  count(i + 1);
}count(0);
```

在上面的代码中，我们有一个简单的递归函数，它在`i`达到 10 时停止。

对于`i`的每个值，我们将`i`的值记录到控制台。

我们应该确保我们有一个基本情况来停止函数。

如果函数返回递归运行自身的结果，JavaScript 不会通过用循环替换它来优化它。

例如，如果我们有:

```
const factorial = (i, a = 1) => {
  if (i < 2) {
    return a;
  }
  return factorial(i - 1, a * i)
}const result = factorial(10);
```

然后 JavaScript 会把它变成一个循环，所以如果我们调用它太多次，调用堆栈就会满了。

# 范围

编程语言中的作用域控制变量和参数的可见性。

在 JavaScript 中，我们有一个函数和块范围的变量。

`let`和`const`是块范围的，`var`是函数范围的。

所以，为了减少混乱，我们应该使用`let`和`const`来声明数据。

例如，如果我们有:

```
if (condition) {
  let x = 1;
  const y = 2;
  //...
}
```

`x`和`y`是块范围的，所以它们只在`if`块中可用。

我们应该忘记`var`，只使用`let`或`const`。

变量应该尽可能晚地声明，这样它们的生命周期就很短。

这减少了我们在阅读代码时在许多行代码中使用变量的需要。

# 关闭

内部函数可以访问函数中定义的参数和变量。

我们可以利用这一点使值私有，并在内部函数中使用它们。

例如，我们可以写:

```
const obj = (() => {
  let value = 0;
  return {
    increment(val) {
      value += val;
    },
    getValue() {
      return value;
    }
  };
})();
```

`value`是私有的，所以我们可以通过增加`val`来更新`value`。

所以我们安全地更新了`value`而没有暴露给外界。

我们在上面使用了一个生命，我们可以返回一个使用私有变量的对象。

同样，我们可以通过去掉括号把它变成一个工厂函数。

例如，我们可以写:

```
const createName = (name) => {
  return {
    getName() {
      return name;
    }
  };
};const name = createName('foo').getName();
```

我们返回一个返回参数`name`值的对象。

它不是一个构造函数，所以我们不能将它与`new`操作符一起使用。

内部函数可以访问变量本身，而不是变量产生时的值。

例如，如果我们有:

```
const addHandlers = (nodes) => {
  for (var i = 0; i < nodes.length; i += 1) {
    nodes[i].onclick = () => {
      alert(i);
    }
  }
}addHandlers(document.querySelectorAll('button'))
```

如果我们运行上面的代码，无论我们点击哪个按钮，警告框总是显示 3。

这是因为在创建函数时，循环体是针对变量`i`而不是变量`i`的。

为了解决这个问题，我们可以用`let`代替`var`:

```
const addHandlers = (nodes) => {
  for (let i = 0; i < nodes.length; i += 1) {
    nodes[i].onclick = () => {
      alert(i);
    }
  }
}addHandlers(document.querySelectorAll('button'))
```

`let`是块范围的，因此当循环结束时，`onclick`处理程序将获得`i`的当前值，而不是`i`的值。

![](img/12498cc7c72e69fcbeecf236cf4dfab5.png)

由 [Emre Gencer](https://unsplash.com/@reo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

递归函数是直接或间接调用自身的函数。

这有助于将问题分成琐碎的子问题。

变量的范围取决于我们如何声明它们。如果我们用`let`或`const`来声明它们，那么它们就是块范围的。

如果我们用`var`声明它们，那么它们就是函数范围的。

闭包允许我们访问私有变量。