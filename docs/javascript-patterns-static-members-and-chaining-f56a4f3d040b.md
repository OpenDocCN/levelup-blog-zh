# JavaScript 模式——静态成员和链接

> 原文：<https://levelup.gitconnected.com/javascript-patterns-static-members-and-chaining-f56a4f3d040b>

![](img/6e963c71076be4222270ece3ab9268cf.png)

照片由 [Judson Moore](https://unsplash.com/@judsonlmoore?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

在本文中，我们将研究如何创建和使用类和构造函数，包括静态成员和链接。

# 静态成员

我们可以给类和构造函数添加静态成员。

没有什么特别的方法可以做到这一点。我们只是把它们作为函数的属性加入。

例如，我们可以写:

```
class Foo {}
Foo.bar = 1;
```

然后我们将`bar`属性作为`Foo`的静态属性。

对于方法，我们可以使用 static 关键字。我们可以写:

```
class Foo {
  static hello() {
    //...
  }
}
```

或者我们可以写:

```
class Foo {}
Foo.hello = function() {
  //...
}
```

# 公共静态成员

静态成员总是公共的，因为它们只是函数的属性。

如果我们调用上面定义的`hello`方法，我们可以写出:

```
Foo.hello()
```

我们不必实例化`Foo`。

另一方面，如果我们如下定义一个实例方法:

```
class Foo {
  bar() {
    //...
  }
}
Foo.hello = function() {
  //...
}
```

并称之为:

```
Foo.bar()
```

我们得到“未捕获类型错误:`Foo.bar`不是函数”错误。

这是因为在我们实例化`Foo`之前`Foo.bar`没有被定义。

当方法实际上是实例方法时，我们应该小心被静态调用的方法。

# 私有静态成员

私有静态成员是由同一类或构造函数创建的所有对象共享的成员。

它也不能在构造函数之外访问。

例如，我们可以通过创建 IIFE 来定义私有成员，IIFE 将返回一个类或构造函数，如下所示:

```
const Foo = (() => {
  let counter = 0; return class {
    constructor() {
      console.log(counter += 1);
    }
  };
})();
```

在上面的代码中，我们有私有成员`counter`。

我们在回归生活的课上提到了它。

因此，如果我们调用`Foo`类两次:

```
new Foo();
new Foo();
```

我们得到:

```
1
2
```

记录在控制台输出中。

我们在每个类调用中增加相同的`counter`变量，所以我们看到每次调用类时`counter`的值都会增加。

我们还可以在返回的类中添加新方法。

例如，我们可以添加一个`getLastId`方法，如下所示:

```
const Foo = (() => {
  let counter = 0; return class {
    constructor() {
      console.log(counter += 1);
    } getLastId() {
      return counter;
    }
  };
})();
```

现在我们可以获得`counter`的值，并将其用作 ID。

# 对象常数

我们可以使用`const`关键字来创建常量。

但是，它们只对变量有效。

如果我们想创建一个常量作为一个对象的属性，我们必须创建一个私有成员，然后通过返回它们来访问其中定义的常量。

例如，我们可以写:

```
const Foo = (() => {
  const BAR = 1;
  return {
    get BAR() {
      return BAR;
    }
  }
})();
```

在上面的代码中，我们将私有的`BAR`常量设置为 1。

然后我们用`BAR` getter 返回一个对象，它返回私有常量`BAR`的值。

现在，即使我们尝试将其设置如下:

```
Foo.BAR = 2;
```

我们仍然得到`Foo.BAR`是 1，因为我们记录了它的值。

这是因为该属性只是一个 getter。

![](img/198781ef782a68eb3f3cfbcdb19e4565.png)

paweczerwi324ski 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 链接模式

在 JavaScript 中，我们经常看到如下模式:

```
obj
  .method1("hello")
  .method2("world")
  .method3();
```

这被称为链接模式。

要创建可链接的方法，我们只需在每个方法中返回`this`。

例如，我们写道:

```
const obj = {
  value: 0,
  increment() {
    this.value += 1;
    return this;
  },
  subtract(v) {
    this.value -= v;
    return this;
  },
  log() {
    console.log(this.value);
  }
};
```

每个方法都返回上面对象中的`this`。

然后我们可以将对象中的方法链接起来，如下所示:

```
obj
  .increment()
  .subtract(2)
  .log();
```

然后我们从`log`方法中得到`-1`。

# 结论

如果我们在每个方法中返回`this`，我们可以链接方法。

如果我们将静态成员定义为函数的属性，那么它们就是公共的。

如果我们希望它们是私有的，那么我们可以在 IIFE 中定义它们，并返回一个使用静态成员的类或构造函数。