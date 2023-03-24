# 我们现在应该停止使用的 JavaScript 结构

> 原文：<https://levelup.gitconnected.com/javascript-constructs-that-we-should-stop-using-now-293b2e84b4df>

![](img/3a53f3c19b2d687ffc3dc3395d15bbca.png)

约翰·马特丘克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 通过保留旧的结构来保持与旧应用程序的向后兼容性。

在大多数情况下，它们已经过时，并被产生更干净、更具表达力的代码的新构造所取代。

在本文中，我们将看看那些我们现在应该停止或尽量减少使用的旧构造。

# 传统功能

传统函数是以关键字`function`开头的函数。

我们不应该再经常使用它们，因为我们有了类语法作为构造函数和箭头函数的语法糖，它们不关心`this`的值。

## 提升

传统函数也有两种不同的变体——函数声明和函数表达式。

函数声明是这样编写的函数:

```
function foo() {
  console.log('foo');
}
```

它们被提升，这意味着它们被拉到代码的顶部，或者说被提升，是由 JavaScript 解释器自动完成的。

因此，我们可以在它被定义之前调用它们:

```
foo();function foo() {
  console.log('foo');
}
```

函数表达式是分配给变量的传统函数:

```
const foo = function() {
  console.log('foo');
}
```

如果我们在定义函数表达式之前调用它们，我们会得到一个错误。

这很混乱，很难记住。因此，编写传统函数很容易出错。

## 遗产

由于需要操作原型，构造函数也令人困惑。

例如，我们必须写:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}Person.prototype.fullName = function() {
  return `${this.firstName} ${this.lastName}`;
}
```

创建一个构造函数，然后向其中添加一个实例方法。

`Person.prototype.fullName`是`Person`的实例方法，`Person`是构造函数。

这是我们要做的又一步，实例方法在函数之外，这和大多数面向对象语言不一样。

同样，构造函数可以像其他面向对象语言一样用`new`关键字实例化，但是我们使用函数而不是类来实例化它们，这使得这更令人困惑。

如果我们必须继承，那么事情会变得更加混乱和容易出错。

例如，如果我们想要创建一个`Employee`构造函数来扩展上面的`Person`构造函数，我们必须编写:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}Person.prototype.fullName = function() {
  return `${this.firstName} ${this.lastName}`;
}function Employee(firstName, lastName, employeeCode) {
  Person.call(this, firstName, lastName)
  this.employeeCode = employeeCode;
}Employee.prototype = Object.create(Person.prototype);Employee.prototype.getEmployeeCode = function() {
  return this.employeeCode;
}
```

它有许多部分。我们要写:

```
Person.call(this, firstName, lastName)
```

在`Employee`构造函数中用 call 方法调用`Person`构造函数。

然后我们必须通过使用`Object.create`来扩展`Person`的原型来设置`Employee`的`prototype`。

要添加一个`Employee`独有的实例，我们必须像处理`getEmployeeCode`一样将方法添加到`Employee`的原型中。

如果我们错过了这些步骤中的任何一步，我们都不会收到警告或错误。因此，很容易犯错误。

![](img/fbb705dcd6e02fc3d31d62c947be9d21.png)

[凯·皮尔格](https://unsplash.com/@kaip?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 类别语法

类语法解决了上述所有问题。它们不能被提升，方法留在类中，我们用`extends`关键字继承现有的类，用`super`调用超类的构造函数。

如果我们的类扩展了另一个类，如果我们忘记调用`super`，我们会得到错误。

例如，我们可以使用如下的类语法重写`Person`构造函数:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}
```

如果我们在它被定义之前引用它，我们会得到一个错误，

另外，`fullName`方法在类内部，而不是`Person`的`prototype`的属性，

下面是一样的，但是干净多了。

为了创建一个扩展了`Person`类的`Employee`类，我们编写:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}class Employee extends Person {
  constructor(firstName, lastName) {
    super(firstName, lastName)
  } getEmployeeCode() {
    return this.employeeCode;
  }
}
```

正如我们所见，我们刚刚添加了`extends`关键字，调用父类的构造函数的`super`调用，然后将我们自己的`getEmployeeCode`方法添加到`Employee`类中。

如果我们在类声明中有`extends`，那么如果我们遗漏了`super`，我们就会得到错误。

# 参数对象

在使用 rest 操作符之前，我们必须使用`arguments`对象将所有参数传递给一个函数。

`arguments`对象是一个类似数组的对象，这意味着它有索引和`length`属性。但是它没有任何数组方法。

还是那句话，这很有欺骗性。

使用 rest 操作符，我们得到一个返回的数组，所以我们可以使用数组方法。

例如，我们可以写:

```
const foo = (...args) => console.log(args.join(','))
foo(1, 2, 3, 4, 5);
```

`...args`是休息操作。这 3 个点是 rest 操作符，`args`有传入的参数数组。

我们可以在`args`上调用`join`方法，所以我们知道它是一个数组。

它也适用于传统的箭头函数，然而，`arguments`不适用于箭头函数。

# 结论

旧的结构保存在 JavaScript 中，所以旧的应用程序不会崩溃。然而，这并不意味着我们应该使用它们。

我们应该开始使用新的构造，比如类语法和 rest 操作符，因为它们更干净，欺骗性和迷惑性更小。