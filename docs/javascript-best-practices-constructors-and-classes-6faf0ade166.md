# JavaScript 最佳实践—构造函数和类

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-constructors-and-classes-6faf0ade166>

![](img/7a76d32b52646e2cb08f806823690dfe.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

在本文中，我们将研究用 JavaScript 开发时的一些最佳实践，包括对象创建和构造函数。

# 原型

当我们的构造函数中有方法时，我们应该将它们添加到构造函数的`prototype`属性中，而不是放在构造函数中。

例如，不写:

```
function Person(name) {
  this.name = name;
  this.greet = function() {
    return `hi ${this.name}`;
  }
}
```

我们应该写:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  return `hi ${this.name}`;
}
```

更好的是，我们通过编写以下代码来使用类语法:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    return `hi ${this.name}`;
  }
}
```

最后两段代码是相同的。

我们希望像最后两个例子一样编写我们的构造函数，因为我们只想在所有实例之间共享一个`greet`方法的副本。

`greet`方法不会改变，只有`this`的值会改变。

因此，我们不想像在第一个例子中那样保留同一个方法的不同副本。

相反，我们想像前两个例子一样分享它。

我们可以创建一个构造函数的实例来找出不同之处:

```
const person = new Person('joe');
console.log(person)
```

如果我们记录下`person`对象，我们可以看到该方法在`__proto__`属性中，这是`person`的原型。

它有`greet`方法。

如果我们用第一版的`Person`构造函数创建同一个对象，我们会看到`greet`方法在`person`对象本身中。

因此，为了节省内存，方法应该在构造函数的`prototype`属性中，而不是在构造函数本身内部。

# 构造函数的返回值

构造函数不需要显式地返回一些东西。

我们通常不应该显式返回任何东西，因为它会自动返回构造函数的实例。

然而，如果我们愿意，它确实让我们返回不同的东西。

但我们通常不应该这样做，除非我们有理由这样做。

如果我们想这样做，我们可以写:

```
function Person(name) {
  this.name = name;
  return {
    foo: name
  }
}
```

那么当我们如下实例化它时:

```
const person = new Person('joe');
console.log(person)
```

根据控制台日志输出，我们得到`{foo: “joe”}`而不是`Person {name: “joe”}`。

# 实施新的模式

为了创建一个对象的新实例，我们需要使用`new`。

最好的方法是使用类语法。

这样我们没有`new`就叫不出来了。

如果我们调用一个不带`new`的构造函数，那么`this`就是全局对象。

# 构造函数/类的命名约定

构造函数或类通常用 PascalCase 命名。

这将常规函数与构造函数和类区分开来。

![](img/75127e8d312f74a86f1001986439fd05.png)

照片由 [Huyen Nguyen](https://unsplash.com/@huyennguyen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 利用这个

如果我们想确保我们的构造函数总是像构造函数一样，我们也可以创建一个对象并返回它。

例如，我们可以写:

```
function Person(name) {
  const that = {};
  that.name = name;
  return that;
}
```

现在，如果我们在没有使用`new`的情况下调用我们的`Person`构造函数，我们仍然会返回一个对象。

# 自调用构造函数

我们也可以使用`instanceof`操作符来检查构造函数的`this`是否是构造函数的实例。

然后我们就知道我们是否用`new`调用了它。

如果我们这样做了，那么构造函数的`this`应该是构造函数的一个实例。

例如，我们可以进行如下检查:

```
function Person(name) {
  this.name = name;
  if (!(this instanceof Person)) {
    return new Person(name);
  }
}
```

现在，不管我们是否用`new`调用了`Person`构造函数，我们都会得到相同的结果。

```
const person = new Person('joe');
```

并且:

```
const person = Person('joe');
```

应该会得到同样的结果。

# 结论

我们应该将方法保存在构造函数的原型中，而不是构造函数本身。

这样，该方法的一个副本在所有实例之间共享。

这是可行的，因为该方法在所有实例中都是相同的。

类语法会自动为我们做这件事。

同样，我们可以检查`this`是否是构造函数的实例，以检查我们的构造函数是否被正确调用。

类语法也为我们处理了这一点，因为没有关键字`new`我们就不能调用类。