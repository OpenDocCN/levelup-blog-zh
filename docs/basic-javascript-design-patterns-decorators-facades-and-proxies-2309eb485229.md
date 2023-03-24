# 基本的 JavaScript 设计模式——装饰者、外观和代理

> 原文：<https://levelup.gitconnected.com/basic-javascript-design-patterns-decorators-facades-and-proxies-2309eb485229>

![](img/c4e724959df852525ab39855182d4c94.png)

由 [Vu Viet Anh](https://unsplash.com/@bin1080a?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

为了组织我们的代码，我们应该使用一些基本的设计模式。在本文中，我们将研究装饰者、外观和代理。

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

JavaScript 中的装饰器，它只是一个函数，返回一个带有参数`target`、`name`和`descriptor`的函数。

`descriptor`是属性描述符，所以我们可以为它设置`value`、`writable`和`enumerable`属性。

我们的`foo`装饰器将它的`value`属性转换为字符串`'foo'`。

所以我们得到的是`Foo`实例的`bar`属性变成了字符串`'foo'`而不是方法。

JavaScript 装饰器仍然是实验性的语法，所以我们需要 Babel 来使用它。

`@babel/plugin-proposal-decorators`将允许我们在 JavaScript 项目中使用装饰器。

装饰函数返回的函数有另外两个参数。

`target`是装饰器所在的构造函数或类。

`name`参数是它正在修改的实体的字符串。

我们可以用它来修改类中的其他实体。

# 外表

facade 是一个简单的模式，它让我们为对象提供一个可选的接口。

我们可以用它来隐藏复杂的实现。

这很好，因为我们不希望我们代码的用户知道所有的事情。

门面很简单，我们不用做太多。

我们还可以使用它将多个被调用的操作合并成一个操作。

例如，如果我们有多个被一起调用的方法，那么它们可以放在一起成为一个方法。

我们可以如下实现外观模式:

```
const adder = {
  add() {
    //...
  },
}const subtractor = {
  subtract() {
    //...
  },
}
const facade = {
  calc() {
    adder.add();
    subtractor.subtract();
  }
}
```

我们创建了一个结合了`add`和`subtract`方法的`facade`对象。

facade 模式用于在多个对象前面创建一个界面。

此外，我们可以用它来处理浏览器的复杂性。

有些浏览器可能没有`preventDefault`或`stopPropgation`方法，所以我们可能想在调用它们之前检查它们是否存在:

```
const facade = {
  stop(e) {
    if (typeof e.preventDefault === "function") {
      e.preventDefault();
    }
    if (typeof e.stopPropagation === "function") {
      e.stopPropagation();
    }
    //...
  }
}
```

现在我们总是调用`stop`，不必担心它们的存在，因为我们在调用它们之前会检查它们是否存在。

# 代理人

代理模式是一个对象充当另一个对象的接口。

代理模式和外观模式的区别在于，我们可以代理为一个对象创建接口。

但是外观可以是任何东西的接口。

我们可以按如下方式创建代理:

```
const obj = {
  foo() {
    //...
  }, bar() {
    //...
  },
}const proxy = {
  call() {
    obj.foo();
    obj.bar();
  }
}
```

我们用`proxy`来调用`obj`中的`foo`和`bar`。

这种模式适合惰性初始化。

这是我们只在使用的时候初始化的地方。

我们只在需要的时候运行初始化代码，而不是总是运行初始化代码。

![](img/6c6bbce2739b10228764af96afc6e999.png)

[Nelson estevo](https://unsplash.com/@nelsonmestevao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 代理作为缓存

代理可以充当缓存。我们可以在返回结果之前检查缓存中的结果。

如果有，我们就从缓存中返回结果。

否则，我们生成结果，放入缓存，然后返回。

例如，我们可以写:

```
const obj = {
  add() {
    //...
  }
}const proxy = {
  cache: {},
  add(result) {
    if (!cache[result]) {
      cache[result] = obj.add();
      //...      
    }
    return cache[result];
  }
}
```

现在我们可以只在需要的时候做昂贵的手术，而不是总是做。

# 结论

装饰模式可以用来动态改变实体。

JavaScript 有实验性的装饰器让我们做到这一点。

外观和代理都可以用来隐藏对象的实现。

代理用于隐藏一个对象，而外观可以用来隐藏更复杂的实现。