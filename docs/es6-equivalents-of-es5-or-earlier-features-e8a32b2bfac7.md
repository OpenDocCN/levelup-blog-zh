# ES6 等同于 ES5 或更早的特性

> 原文：<https://levelup.gitconnected.com/es6-equivalents-of-es5-or-earlier-features-e8a32b2bfac7>

![](img/92468d6a5068345aa099068b5febbedd.png)

Borna Bevanda 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。自 2015 年以来，JavaScript 增加了许多伟大的功能，旨在取代早期版本 JavaScript 的旧功能。

在本文中，我们将看看哪个 ES6 特性取代了哪个旧特性。每当我们写代码的时候，我们都应该使用新的。

# 从`apply()`到展开操作符(`...`

当我们将多个参数传递给像`Math.max`这样的函数时，我们应该使用 spread 操作符，而不是调用`apply`。

这是因为我们不需要担心使用 spread 操作符的`this`的值。此外，它更干净、更容易阅读，并且可以处理任何可迭代或类似数组的对象。

例如，不写:

```
const max = Math.max.apply(Math, [1, 2, 3, 4, 5]);
```

我们可以写:

```
const max = Math.max(...[1, 2, 3, 4, 5]);
```

正如我们所看到的，写的东西少了很多，我们也不必知道`apply`是做什么的，第一个参数要传递什么。

另一个例子是用`push`将多个项目追加到同一个数组中。

而不是写:

```
const arr1 = [1, 2];
const arr2 = [3, 4];
arr1.push.apply(arr1, arr2);
```

为了将 3 和 4 推入`arr1`，我们编写:

```
const arr1 = [1, 2];
const arr2 = [3, 4];
arr1.push(...arr2);
```

同样，我们写得更少，我们不必像调用`apply`那样担心`this`的值。对于`apply`，我们必须确保通过将`arr1`作为第一个参数传入来推入`arr1`。

使用扩展操作符，我们只需将`arr2`扩展成`push`。

# 从`concat()`到展开操作符(`...`)

同样，我们可以用 spread 操作符替换`concat`。

如果我们要将另一个数组的项追加到一个数组中，而不是写入:

```
const arr1 = [1, 2];
const arr2 = [3];
const arr3 = [4, 5];
const arr = arr1.concat(arr2).concat(arr3);
```

我们写道:

```
const arr1 = [1, 2];
const arr2 = [3];
const arr3 = [4, 5];
const arr = [...arr1, ...arr2, ...arr3];
```

它们都返回一个包含来自`arr1`、`arr2`和`arr3`的所有条目的新数组。

然而，我们不必调用`arr1`上的`concat`来追加条目。我们只是将数组的所有条目放入一个新的数组中。spread 操作符将复制它传播的数组。

# 从对象文字中的函数表达式到方法定义

在 ES6 之前的 JavaScript 版本中，我们使用如下方法创建对象:

```
var obj = {
  foo: function() {},
  bar: function() {
    this.foo();
  },
}
```

我们通过引用`this`和带有`function`关键字的定义方法来调用对象的方法。

在 ES6 中，这可以缩短为:

```
const obj = {
  foo() {},
  bar() {
    this.foo();
  },
}
```

正如我们所看到的，它要短得多，但却能做同样的事情。

![](img/d76278f3322e73927369c2745d770f6f.png)

维克多·格拉巴尔奇克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 从构造函数到类

在 JavaScript 中，类只是构造函数的语法糖。例如，不写:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}Person.prototype.fullName = function() {
  return `${this.firstName} ${this.lastName}`;
};const person = new Person('Jane', 'Doe');
console.log(person.fullName());
```

我们写道:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } fullName() {
    return `${this.firstName} ${this.lastName}`;
  };
}const person = new Person('Jane', 'Doe');
console.log(person.fullName());
```

正如我们所看到的，我们不必再向构造函数的原型添加实例方法。相反，如果我们以前使用过任何其他面向对象的语言，这个类的语法看起来会很熟悉。

字段和方法都在类中，我们用`this`引用实例。

要创建从另一个构造函数继承成员的构造函数，我们使用 ES6 之前的代码执行以下操作:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
Person.prototype.fullName = function() {
  return `${this.firstName} ${this.lastName}`;
};function Student(firstName, lastName, studentNum) {
  Person.call(this, firstName, lastName);
  this.studentNum = studentNum;
}Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;
Student.prototype.describe = function() {
  return `${Person.prototype.fullName.call(this)} ${this.studentNum}`
};const student = new Student('Jane', 'Doe', '123');
console.log(student.describe());
```

上面的代码有一个从`Person`构造函数继承成员的`Student`构造函数。

为了继承`Person`的原型，我们写道:

```
Student.prototype = Object.create(Person.prototype);
```

然后`Student.prototype.constructor`必须被设置为`Student`以确保`Student`有正确的构造函数。

然后我们调用父构造函数的方法:

```
Person.prototype.fullName.call(this)
```

将`Person.prototype.fullName`的`this`设置为`Student`实例。

相反，我们使用类语法编写以下内容:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  };
}class Student extends Person {
  constructor(firstName, lastName, studentNum) {
    super(firstName, lastName);
    this.studentNum = studentNum;
  }
  describe() {
    return `${super.fullName(this.firstName, this.lastName)} ${this.studentNum}`
  }
}const student = new Student('Jane', 'Doe', '123');
console.log(student.describe());
```

正如我们所见，任何用基于类的面向对象语言编程的人都更熟悉类语法。

我们用`super`调用父类的构造函数，并通过调用上面的`super.fullName`调用父类的方法。

此外，我们必须将`extends Person`添加到`Student`中，以确保`Student`从`Person`中继承成员。

当我们拥有`extends`时，如果我们忘记调用`super`，我们将得到一个错误。这比老方法要好，如果我们错过了构建原型的任何代码行，老方法不会给我们任何错误。

# 结论

ES6 语法比早期的语法好得多，尤其是 class 语法。

虽然 spread 操作符是一种非常有用的简写方式，但是类语法是创建构造函数时必须使用的特性，因为它更加简洁，JavaScript 解释器会告诉我们是否犯了错误。