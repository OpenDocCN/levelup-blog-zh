# 基本 JavaScript 设计模式——工厂、迭代器和装饰器

> 原文：<https://levelup.gitconnected.com/basic-javascript-design-patterns-factories-iterators-and-decorators-eab33ba31d5e>

![](img/f09eb65213d5ba4d02dce33c896df515.png)

照片由[马腾·戴克斯](https://unsplash.com/@maartendeckers?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

为了组织我们的代码，我们应该使用一些基本的设计模式。在本文中，我们将研究工厂、迭代器和装饰器。

# 内置对象工厂

JavaScript 的标准库附带了几个工厂方法。

它们包括`Object`、`Boolean`、`Number`、`String`、`Array`。

例如，我们可以如下使用`Boolean`工厂函数:

```
const bool = Boolean(1);
```

那么`bool`就是`true`，因为 1 是真的。

它们所做的只是根据我们传入的内容返回不同的对象或值。

很多都是有用的。然而，`Object`构造函数并不那么有用，因为我们可以用对象文字创建对象。

# 迭代程序

JavaScript 从 ES6 开始就有迭代器了。

因此，我们不必从头开始编写任何东西来实现这个模式

我们只需要使用一个生成器函数来依次返回我们要寻找的值。

然后我们可以调用内置的`next`函数来获取生成器函数返回生成器后返回的值。

例如，我们可以写:

```
const numGen = function*() {
  while (true) {
    yield (Math.random() * 100).toFixed(1);
  }
}
```

创建一个生成器函数。

生成器函数由关键字`function*`表示。在关键字`function`后面有一个星号。

`yield`关键字将依次返回下一个值。

然后我们可以用它来返回一个随机数，如下所示:

```
const nums = numGen();
console.log(nums.next().value);
console.log(nums.next().value);
console.log(nums.next().value);
```

我们首先调用`numGen`来返回一个迭代器。

然后我们调用`nums.next().value`来获得我们想要的值。

我们也可以有一个返回有限数量的值的生成器函数。

例如，我们可以写:

```
const numGen = function*() {
  const arr = [1, 2, 3];
  for (const a of arr) {
    yield a;
  }
}const nums = numGen();
let value = nums.next().value;
while (value) {
  console.log(value);
  value = nums.next().value;
}
```

在上面的代码中，我们修改了`numGen`函数来按顺序返回数组的条目。

这样，我们可以在一个循环中调用`next`方法，用`next`方法按顺序获取条目。

除了`value`属性，`next`方法返回的对象也有`done`属性。

所以我们可以写:

```
const nums = numGen();
let {
  done,
  value
} = nums.next();while (!done) {
  console.log(value);
  const next = nums.next();
  done = next.done;
  value = next.value;
}
```

一旦返回所有值，`done`将被设置为`true`。

# 附加功能

如果我们想要更多的功能，那么我们必须实现我们自己的迭代器。

例如，如果我们需要回滚功能，我们可以创建自己的迭代器，如下所示:

```
const iterator = (() => {
  const arr = [1, 2, 3];
  let index = 0;
  return {
    next() {
      index++;
      return arr[index];
    },
    rewind() {
      index--;
      return arr[index];
    }
  }
})();
```

我们只是添加了一个数组，用`next`方法移动到下一个`arr`条目，用`rewind`方法移动到前一个`arr`条目。

那么我们可以如下使用`iterator`:

```
console.log(iterator.next());
console.log(iterator.rewind());
```

我们看到:

```
2
1
```

从控制台日志输出。

![](img/f6db68fa104e90f2e3b1564265c231d5.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 装饰者

装饰者让我们可以动态地向对象添加功能。

JavaScript 现在有了装饰器，所以我们可以用它们来创建我们自己的装饰器，并动态地改变对象。

例如，我们可以创建一个，如下所示:

```
const foo = () => {
  return (target, name, descriptor) => {
    descriptor.value = "foo";
    return descriptor;
  };
};class Foo {
  @foo()
  bar() {
    return "baz";
  }
}const f = new Foo();
console.log(f.bar);
```

JavaScript 中的 decorator，它只是一个函数，返回一个带参数`target`、`name`和`descriptor`的函数。

`descriptor`是属性描述符，所以我们可以为它设置`value`、`writable`和`enumerable`属性。

我们的`foo`装饰器将它的`value`属性转换为字符串`'foo'`。

所以我们得到的是`Foo`实例的`bar`属性变成了字符串`'foo'`而不是方法。

JavaScript 装饰器仍然是实验性的语法，所以我们需要 Babel 来使用它。

# 结论

JavaScript 有各种工厂函数，让我们创建对象。

还有`Object`、`Boolean`、`Number`、`String`等功能。

JavaScript 内置了对迭代器的支持。然而，如果我们想要它提供更多的功能，那么我们必须实现我们自己的功能。

装饰器存在于 JavaScript 中，所以我们可以用它来创建装饰器来修改属性。