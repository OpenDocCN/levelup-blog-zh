# 基本 JavaScript 设计模式——对象创建

> 原文：<https://levelup.gitconnected.com/basic-javascript-design-patterns-object-creation-677f21df771d>

![](img/7c0798663da7e04b8ea4fdfff4d060b4.png)

照片由[费迪南·斯托尔](https://unsplash.com/@fellowferdi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

为了组织我们的代码，我们应该使用一些基本的设计模式。在本文中，我们将研究一些可能用到的基本对象创建模式。

# 一个

单例模式是我们创建一个类的单个实例的地方。

由于我们可以在 JavaScript 中创建不带构造函数的对象文字，因此我们可以轻松地创建对象，如下所示:

```
const obj = {
  foo: 'bar'
};
```

`obj`也将是一个独立的对象，所以定义一个对象字面量遵循 singleton 模式。

如果我们创建一个具有相同结构的新对象，我们将得到一个不同的对象。

例如，如果我们创建一个新对象:

```
const obj2 = {
  foo: 'bar'
};
```

然后当我们写下:

```
obj2 === obj
```

比较两个对象，然后返回`false`,因为它们是不同的引用。

# 使用新的

我们也可以创建单例对象，即使我们从构造函数或类中创建它们。

例如，我们可以写:

```
let instance;
class Foo {
  constructor() {
    if (!instance) {
      instance = {
        foo: 'bar'
      };
    }
    return instance;
  }
}
```

然后，如果我们创建 2 个`Foo`实例:

```
const foo1 = new Foo();
const foo2 = new Foo();
```

当我们比较它们时:

```
console.log(foo1 === foo2);
```

我们看到`true`被记录。

我们检查`instance`是否被创建，如果被创建，就返回它。否则，我们将其设置为一个对象。

由于`instance`设置后不会改变，所以所有实例都是一样的。

我们可以通过将变量`instance`放入一个生命中来使其私有。

例如，我们可以写:

```
const Foo = (() => {
  let instance;
  return class {
    constructor() {
      if (!instance) {
        instance = {
          foo: 'bar'
        };
      }
      return instance;
    }
  }
})()
```

现在我们只能访问返回的实例，而不是查看构造函数。

# 静态属性中的实例

或者，我们可以将 singleton 的实例放在构造函数或类的静态属性中。

例如，我们可以写:

```
class Foo {
  constructor() {
    if (!Foo.instance) {
      Foo.instance = {
        foo: 'bar'
      };
    }
    return Foo.instance;
  }
}
```

这样，我们设置类`Foo`的公共`instance`属性来返回实例，如果它存在的话。否则，它会先创建它。

现在如果我们写:

```
const foo1 = new Foo();
const foo2 = new Foo();console.log(foo1 === foo2);
```

我们得到和以前一样的结果。

# 闭包中的实例

我们也可以把单例实例放在闭包里。

例如，我们可以写:

```
function Foo() {
  const instance = this;
  this.foo = 'bar';
  Foo = function() {
    return instance;
  }
}
```

我们可以写一个构造函数，在函数结束时被重新分配给自己。

这将让我们在使用`this`分配即时变量时返回`instance`。

这不适用于类语法，因为它是作为常量创建的，所以我们不能给它重新赋值。

![](img/d864433abe5187277b9033bea917b00b.png)

照片由[彭军欧阳](https://unsplash.com/@joshoy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 工厂

我们可以创建一个工厂函数来创建对象。

工厂函数只是一个常规函数，我们可以用它来创建对象。

当设置相似的对象时，这对于执行重复的操作很有用。

它还为工厂用户提供了一种在编译时无需特定类即可创建对象的方法。

例如，我们可以创建自己的工厂函数，如下所示:

```
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}
class Bird extends Animal {}const AnimalMaker = type => {
  if (type === 'dog') {
    return new Dog()
  } else if (type === 'cat') {
    return new Dog()
  } else if (type === 'bird') {
    return new Dog()
  }
}
```

上面的代码有`AnimalMaker`工厂函数。

它采用了`type`参数，允许我们在给定`type`的情况下创建不同的子类。

所以我们可以这样称呼它:

```
const animal = AnimalMaker('dog');
```

创建一个`Dog`实例。

我们也可以随意在类中添加方法:

```
class Animal {
  walk() {}
}
class Dog extends Animal {
  bark() {}
}
class Cat extends Animal {}
class Bird extends Animal {}const AnimalMaker = type => {
  if (type === 'dog') {
    return new Dog()
  } else if (type === 'cat') {
    return new Dog()
  } else if (type === 'bird') {
    return new Dog()
  }
}
```

这个也行。

# 结论

我们可以用一个实例 y 创建对象，使用对象字面量或者检查类的实例是否存在，如果不存在就创建它。

工厂函数对于创建相似的对象很有用。