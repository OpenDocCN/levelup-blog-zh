# 为什么在使用 const JavaScript 关键字时要小心？

> 原文：<https://levelup.gitconnected.com/why-should-we-be-careful-when-using-the-const-javascript-keyword-18f3e7efa07b>

![](img/c1a9d2a63038ec9171268cddac3b681e.png)

照片由[帕梅拉·利马](https://unsplash.com/@pamelalima?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

自从 ES6 发布以来，JavaScript 增加了许多新特性。其中之一是用于声明常数的`const`关键字。它让我们创建不能重新分配给其他东西的常数。

但是，我们还是可以用其他方式改变它。在这篇文章中，我们将看看经常被忽视的`const`的特征以及如何处理这些陷阱。

# const 的特性

`const`是一个块范围关键字，用于声明在定义它的块中可用的常量。在声明之前不能使用它。这意味着没有提升。

它们不能被重新分配，也不能被声明。

例如，我们可以如下声明一个常数:

```
const foo = 1;
```

在声明之前我们不能访问它，所以:

```
console.log(foo);
const foo = 1;
```

将给出错误“未捕获的引用错误:无法在初始化前访问‘foo’”

范围可以是全局的，也可以是局部的。然而，当它是全局的时，它不是`window`对象的属性，所以不能从应用程序中的其他脚本访问它。

它是对一个值的只读引用。这意味着需要注意的是对象**仍然是可变的。当我们声明常量时，这是经常被忽略的。用`const`声明的对象的属性仍然可以改变，对于数组，我们可以添加和删除条目。**

例如，我们可以写:

```
const foo = {
  a: 1
};
foo.a = 1;
console.log(foo.a);
```

然后我们从`console.log`得到 1。

我们还可以向用`const`声明的现有对象添加属性:

```
const foo = {
  a: 1
};
foo.b = 1;
console.log(foo.b);
```

同样，我们从`console.log`得到 1。

另一个例子是数组操作。我们仍然可以操作用`const`声明的数组，就像任何其他对象一样:

```
const arr = [1, 2, 3];
arr[1] = 5;
console.log(arr)
```

这个`console.log`将会是我们`[1, 5, 3]`。

我们还可以通过编写以下内容向其添加条目:

```
const arr = [1, 2, 3];
arr[5] = 5;
console.log(arr)
```

然后我们得到:

```
[1, 2, 3, empty × 2, 5]
```

正如我们所见，用`const`声明的对象仍然是可变的。因此，如果我们想防止意外改变用`const`声明的对象，我们必须使它们不可变。

![](img/d151ddfad3082b9cfa1ad6c8309ec53b.png)

乌列尔·索伯兰斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使对象不可变

我们可以用`Object.freeze`方法使对象不可变。它有一个参数，就是我们想要冻结的对象。冻结意味着属性和值不能改变。

每个属性的属性描述符也不能更改。这意味着除了值之外，可枚举性、可配置性和可写性也不能改变。

可配置的属性意味着上面的属性描述符可以被改变并且属性可以被删除。

可枚举性意味着我们可以用`for...in`循环遍历属性。

可写性意味着属性值可以被赋值。

`Object.freeze`通过将除了`enumerable`之外的一切都设置为`false`来防止这一切的发生。

例如，我们可以如下使用它:

```
const obj = {
  prop: 1
};Object.freeze(obj);
obj.prop = 3;
```

在严格模式下，上面的代码会给我们一个错误。否则，`obj`会在最后一行后保持不变。

注意`Object.freeze`只冻结对象顶层的属性，所以如果我们运行:

```
/* 'use strict'; */
const obj = {
  prop: 1,
  foo: {
    a: 2
  }
};
Object.freeze(obj);
obj.foo.a = 3;
```

我们会得到:

```
{
  "prop": 1,
  "foo": {
    "a": 3
  }
}
```

我们可以看到，在我们对它调用了`Object.freeze`之后，`obj.foo.a`仍然发生了变化。

因此，为了使整个对象不可变，我们必须在每一层递归调用`Object.freeze`。

我们可以定义一个简单的递归函数来做到这一点:

```
const deepFreeze = (obj) => {
  for (let prop of Object.keys(obj)) {
    if (typeof obj[prop] === 'object') {
      Object.freeze(obj[prop]);
      deepFreeze(obj[prop]);
    }
  }
}
```

那么当我们调用`deepFreeze`而不是`Object.freeze`时如下:

```
const obj = {
  prop: 1,
  foo: {
    a: 2
  }
};
deepFreeze(obj);
obj.foo.a = 3;
```

我们得到`obj`保持不变:

```
{
  "prop": 1,
  "foo": {
    "a": 2
  }
}
```

`deepFreeze`函数只是遍历`obj`的所有自身属性，然后对其调用`Object.freeze`，如果属性值是一个对象，那么它调用该对象更深层次的`deepFreeze`。

包括字符串在内的原始值总是不可变的，所以我们不必担心它们。

用`Object.freeze`冻结一个对象的另一个好处是没有办法解冻它，因为属性的可配置性被设置为`false`，所以不能对对象的结构做更多的改变。

要使一个对象再次可变，我们必须复制它并把它赋给另一个对象。

既然我们知道`const`实际上并不使一切都恒定，我们在使用`const`时必须小心。我们不能给现有的常数赋值。但是，我们仍然可以更改分配给常量的现有对象的属性值。

用`const`声明的常量实际上并不是常量。为了使它实际上是常数，或者不可变的，我们必须在用`const`声明的对象的每一层上调用`Object.freeze`，以确保它实际上是常数。

这只适用于对象值属性。根据定义，原语是不可变的，所以我们不必担心它们。