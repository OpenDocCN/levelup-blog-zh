# JavaScript 最佳实践—类和构造函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-classes-and-constructors-d19696a8d943>

![](img/831c2166896476d48109fd5b0c1b1587.png)

[金伯利农民](https://unsplash.com/@kimberlyfarmer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究处理类和构造函数的最佳实践。

# 始终使用类语法。避免直接操作 P `rototype`

JavaScript 中的类语法只是构造函数语法之上的语法糖。

要用实例方法创建构造函数，我们必须编写类似下面的代码:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  return `hi ${this.name}`;
};
```

在上面的代码中，我们有`Person`构造函数，它有`name`参数。我们将`name`参数设置为`this.name`。

然后我们定义了`greet`实例方法，我们返回了包含单词“hi”和`this.name`的字符串。

`this.name`是`Person`实例中的`name`成员。

如果我们记录以下表达式的值:

```
new Person('jane').greet()
```

然后返回字符串`'hi jane’`。

旧的构造函数使得创建构造函数更加困难。我们必须向它的`prototype`属性添加实例，这意味着我们不能将所有成员放在一个地方。

相反，我们应该使用类语法，它让我们将所有成员都放在一个类中。

例如，使用类语法，我们可以将代码重写如下:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    return `hi ${this.name}`;
  };
}
```

在上面的代码中，我们有`Person`类，它封装了构造函数的所有成员，包括实例变量和方法，都在一个实体中。

这比在原型中添加属性要好得多。此外，我们确保我们的初始化代码在`constructor`方法中，而不是在函数本身中。

这就更清楚了，因为我们知道`this`的值就是`Person`实例。通过阅读代码，不太清楚构造函数示例中的`this`的值是什么。

# 使用`extends`进行继承

我们可以添加到类中的`extends`关键字使得用构造函数进行继承变得容易。

有了它，我们可以创建一个继承自父类的子类，而不用创建必须调用和修改`prototype`的构造函数。

如果我们有了`extends`关键字，如果我们忘记通过调用`constructor`中的`super`来调用父构造函数，我们也会得到一个错误。

例如，不用编写如下带有构造函数的代码:

```
function Animal(name) {
  this.name = name;
}Animal.prototype.greet = function() {
  return `hi ${this.name}`;
}function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Animal;
```

在上面的代码中，我们必须做很多事情来创建一个`Dog`构造函数作为`Animal`构造函数的子代。

我们必须通过编写`Animal.call(this, name);`来调用`Animal`构造函数。然后我们必须通过用`Animal`的`prototype`调用`Object.create`来创建`Dog`的`prototype`，就像我们用:

```
Dog.prototype = Object.create(Animal.prototype);
```

此外，我们必须通过编写以下内容将`Dog`的父构造函数设置为`Animal`:

```
Dog.prototype.constructor = Animal;
```

正如我们所见，我们需要许多行来创建一个继承自`Animal`的`Dog`构造函数。

我们只能通过从`Dog`实例中调用`greet`方法来确保我们做得正确，如下所示:

```
new Dog('jane').greet()
```

由于我们正确地用构造函数做了继承，我们可以轻松地调用`Animal`构造函数。

相反，我们使用类语法编写以下内容:

```
class Animal {
  constructor(name) {
    this.name = name;
  } greet() {
    return `hi ${this.name}`;
  };
}class Dog extends Animal {
  constructor(name, breed) {
   super(name);
    this.breed = breed;
  }
}
```

在上面的代码中，我们所要做的就是用`super`从`Dog`的构造函数中调用父构造函数，并添加`extends`关键字。如果我们忘记调用`super`，我们将得到一个错误。

此外，类成员代码都在花括号内，所以我们可以知道

因此，使用带有`extends`的类语法要清晰得多。出错的机会也更少。

![](img/5e09ed64ecfaca60fb15a586f0a7b64d.png)

[伊万·班杜拉](https://unsplash.com/@unstable_affliction?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

应该始终使用类语法，而不是构造函数语法。它更适合定义独立的构造函数，也适合从其他构造函数继承。

类语法相当于构造函数。