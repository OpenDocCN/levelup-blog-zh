# JavaScript 重构—对象和值

> 原文：<https://levelup.gitconnected.com/javascript-refactoring-objects-and-values-ea1aa7371360>

![](img/6e351cdfb023ed4e5c71806d976c14a9.png)

照片由[丹妮尔·塞鲁洛](https://unsplash.com/@dncerullo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们可以清理我们的 JavaScript 代码，这样我们可以更容易地使用它们。

在本文中，我们将研究一些与清理 JavaScript 类和对象相关的重构思想。

# 用符号常数替换幻数

如果我们有许多重复的值，它们有相同的含义，但没有明确地说明，那么我们应该把它们变成一个常数，这样每个人都知道它们的含义，如果需要改变，我们只需要改变一个地方。

例如，不写:

```
const getWeight = (mass) => mass * 9.81const potentialEnergy = (mass, height) => mass * height * 9.81
```

我们写道:

```
const GRAVITATIONAL_CONSTANT = 9.81;const getWeight = (mass) => mass * GRAVITATIONAL_CONSTANTconst potentialEnergy = (mass, height) => mass * height * GRAVITATIONAL_CONSTANT
```

现在我们知道 9.81 其实就是`GRAVITATIONAL_CONSTANT`的意思，不用赘述。

# 封装字段

我们可以向字段添加 getters 和 setters 来访问类字段，而不是直接操作它。

例如，不写:

```
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

我们写道:

```
class Person {
  constructor(name) {
    this._name = _name;
  } get name() {
    return this._name;
  } set name(name) {
    this._name = name;
  }
}
```

这样，我们可以控制如何设置值，因为我们可以在 setter 中放置代码来设置`name`。

我们还可以控制谁可以获得名称，因为它是在 getter 中返回的。

# 封装集合

我们还可以封装作为类实例一部分的数组。

例如，不写:

```
class Person {
  constructor(friends) {
    this._friends = _friends;
  } get friends() {
    return this._friends;
  } set friends(friends) {
    this._friends = friends;
  }
}const person = new Person(['joe', 'jane'])
```

在上面的代码中，我们有一个数组，而不是上面的另一种类型的对象。

但想法是一样的。

# 用数据类替换记录

我们可以用一个字段自己的数据类来替换它，这样我们在记录数据时就可以有更大的灵活性。

例如，不写:

```
class Person {
  constructor(name, bloodGroup) {
    this.name = name;
    this.bloodGroup = bloodGroup;
  }
}const person = new Person('joe', 'a')
```

我们写道:

```
class BloodGroup {
  constructor(name) {
    this.bloodGroup = name;
  }
}class Person {
  constructor(name, bloodGroup) {
    this.name = name;
    this.bloodGroup = bloodGroup;
  }
}const bloodGroup = new BloodGroup('a');
const person = new Person('joe', bloodGroup)
```

这样，我们可以在`bloodGroup`字段中存储更多种类的数据。

![](img/2daf999773c796b5c2a57869d74739d1.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 用状态/策略替换类型代码

我们根据对象的类型创建子类，而不是在类中有一个类型字段。

这样，我们可以拥有更多两个类在自己的子类中不共享的成员。

例如，不写:

```
class Animal {
  constructor(type) {
    this.type = type;
  }
}const cat = new Animal('cat');
const dog = new Animal('dog');
```

我们写道:

```
class Animal {
  //...
}class Cat extends Animal {
  //...
}class Dog extends Animal {
  //...
}const cat = new Cat();
const dog = new Dog();
```

在上面的例子中，我们没有写一个`Animal`类，而是写了`Cat`和`Dog`类，它们是`Animal`类的子类。

通过这种方式，我们可以拥有他们自己的类中没有共享的成员，而他们共享的成员仍然在`Animal`类中。

# 分解条件

我们可以把长的条件表达式分解成更小的条件表达式，命名为。

例如，不写:

```
let ieIEMac = navigator.userAgent.toLowerCase().includes("mac") && navigator.userAgent.toLowerCase().includes("ie")
```

我们写道:

```
let userAgent = navigator.userAgent.toLowerCase();
let isMac = userAgent.includes("mac");
let isIE = userAgent.toLowerCase().includes("ie");
let isMacIE = isMac && isIE;
```

我们打破了条件表达式，将更小的表达式分配到它们自己的变量中，然后将它们组合起来，使一切更容易阅读。

# 结论

如果我们有幻数，我们应该用命名的常数来代替它们。

我们应该为类字段添加 getter 和 setter 方法，这样我们就可以控制如何返回或设置它们。

如果我们有`type`字段，我们可以用它们自己的子类替换它们。

最后，我们可以把长的条件表达式分解成更小的表达式，这样更容易阅读和理解。