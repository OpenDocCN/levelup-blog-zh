# JavaScript 面试问题—函数和对象

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-functions-and-objects-74694bd90a8e>

![](img/51e2f4c14409b4c2f0ca1b50db10dce9.png)

保罗·布兰道在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在这篇文章中，我们将看看一些函数和宾语问题。

# 什么是箭头函数？

箭头函数是 JavaScript 中定义函数的一种新方法。从 ES2015 开始提供。

它消除了与`this`的混淆，因为它没有绑定到`this`，所以它们不能用作构造函数。

它们也不能被提升，所以它们只能在被定义后使用。

此外，它们没有绑定到`arguments`对象，所以我们不能像在`function`声明中那样将参数传递给箭头函数。

为了从 arrow 函数中获取参数，我们使用 rest 语法，如下所示:

```
const getArgs = (...rest) => rest
```

例如，我们可以如下定义箭头函数:

```
const currentDate = () => new Date();
```

在上面的代码中，我们用`=>`定义了箭头函数，并返回`new Date()`来返回当前日期。

如果我们想在多行箭头函数中返回一些东西，我们必须像其他函数一样显式地编写`return`。

例如，我们可以写:

```
const currentDate = () => {
  return new Date();
}
```

在多行箭头函数中显式返回`new Date()`。

我们可以像传递其他函数一样传递参数，如下所示:

```
const identity = (obj) => {
  return obj;
}
```

# 什么是类？

在 JavaScript 中，类是构造函数的语法糖。它用于创建一个类的实例，但是从我们的角度隐藏了原型操作。

本质上，它仍然使用它一直使用的原型继承模型。

例如，如果我们有下面的类链:

```
class Person {
  constructor(firstName, lastName) {
    this.lastName = lastName;
    this.firstName = firstName;
  } fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}class Employee extends Person {
  constructor(firstName, lastName, title) {
    super(firstName, lastName);
    this.title = title;
  } getTitle() {
    return this.title;
  }
}
```

在上面的代码中，我们有一个带有构造函数和获取全名的方法的`Person`类。

然后我们有了扩展了`Person`类的`Employee`类，正如我们用`extends`关键字指出的那样。

在构造函数中，我们调用了`Person`类的构造函数，还设置了`title`字段，该字段是`Employee`专有的。

我们还添加了一个`getTitle`方法。

如果我们忘记调用`super`函数，JavaScript 解释器会给我们一个错误。

这与下面用构造函数编写的代码相同:

```
function Person(firstName, lastName) {
  this.lastName = lastName;
  this.firstName = firstName;
}Person.prototype.fullName = function() {
  return `${this.firstName} ${this.lastName}`;
}function Employee(firstName, lastName, title) {
  Person.call(this, firstName, lastName);   
  this.title = title;
}

Employee.prototype = Object.create(Person.prototype);Employee.prototype.getTitle = function() {
  return this.title;
}
```

上面的代码与我们在类中使用的代码相同，除了如果`Person.call`或`Object.create`丢失，我们不会得到错误。

我们还必须在函数之外给每个构造函数的`prototype`添加东西。

即使我们不使用构造函数语法，我们也必须知道类语法只是构造函数的语法糖。

![](img/c5d8b75f2589b201f16d775def5d5c57.png)

安迪·奇尔顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是对象析构？

对象析构是一种将数组条目或对象属性赋给它们自己的变量的干净方式。

例如，我们可以使用它将数组条目分解为单独的变量，如下所示:

```
const [one, two] = [1, 2];
```

那么`one`是 1，`two`是 2。

上面的代码与以下代码相同:

```
const arr = [1, 2];
const one = arr[0];
const two = arr[1];
```

同样，我们可以对对象做同样的事情，如下所示:

```
const {
  one,
  two
} = {
  one: 1,
  two: 2
}
```

然后将`one`属性的值赋给`one`，将`two`的值赋给`two`。

上面的代码与以下代码相同:

```
const obj = {
  one: 1,
  two: 2
}
const one = obj.one;
const two = obj.two;
```

正如我们所看到的，对象和数组的析构比老方法要干净得多。

我们还可以通过编写以下内容将变量设置为默认值:

```
const [one, two, three = 3] = [1, 2];
```

那么由于`three`没有被分配数组条目，`three`的值为 3。

# 结论

箭头函数对于创建非构造函数的函数很有用。它们不绑定到`this`。此外，它更短，因为我们不必写`return`来返回一行代码。

JavaScript 中的类是构造函数的语法糖。它让我们更容易继承和创建实例方法，因为我们不必直接操作原型，如果我们遗漏了什么，JavaScript 解释器会给我们错误。

对象和数组的析构让我们以一种简洁的方式将对象或数组条目赋给变量。