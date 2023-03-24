# 学习 JavaScript:使用对象— this、getters 和 setters

> 原文：<https://levelup.gitconnected.com/learning-javascript-working-with-objects-this-getters-and-setters-16f944397986>

![](img/dca911a501de1b667d4c4fa1f7c00d88.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 对象为读者提供了一些特殊的特性，使得基于对象的程序员更加有效和高效。在本文中，我将描述其中的三个特性——`this`关键字、getters 和 setters。

# this 关键字

如果在计算机编程中还有比`this`关键字更令人困惑的关键字，我不知道它是什么。然而，`this`背后的概念并没有那么难理解。关键字`this`指的是引用中的当前对象，使用它的代码在引用中执行。

例子往往比解释更好，所以这里举个例子。首先，让我们编写一个对象构造函数和一个简短的程序来测试它:

```
function Person(n, a) {
  this.name = n;
  this.age = a;
}let me = new Person("Mike", 63);
print(me.name + ", " + me.age);
```

这个程序的输出是:

```
Mike, 63
```

`this`关键字用于在代码运行时引用内存中的当前对象属性。可以翻译为“将 n 中的值赋给这个特定对象的 name 属性。”

`this`的另一个很好的用途是消除属性名与参数名相同的代码的歧义，就像在这个重写的构造函数中一样:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}
```

你不能把`this` 关键字从构造函数定义中去掉，但是它仍然有助于使代码对读者来说更加清晰。

`this`关键字在处理 JavaScript 中的对象时起着重要的作用，我希望我已经让你对它的用法更清楚了。

# 用 Setters 分配对象属性值

setter 是一个特殊的函数，它允许您将数据分配给一个对象，就像您使用赋值一样，但是有一个函数的支持，该函数可用于执行数据验证之类的事情。

让我们来看看用于设置`Person`对象年龄的 setter 的第一个版本，以及一个测试程序:

```
let Person = {
  name: "",
  setAge(age) {
    this.age = age;
  }
}let me = Person;
me.name = "Mike";
me.setAge = 63;
print(me.name + ", " + me.age);
```

这个程序的输出是:

```
Mike, 63
```

setter 的一个更好的用途是用它来执行一些数据验证。在这个版本中，setter 检查以确保分配给对象的年龄至少为 0。下面是代码和一个测试程序:

```
let Person = {
  name: "",
  set setAge(a) {
    if (a >= 0) {
      this.age = a;
    }
    else {
      this.age = 0;
    }
  }
}let me = Person;
me.name = "Mike";
me.setAge = 63;
print(me.name + ", " + me.age);
me.setAge = -10;
print(me.name + ", " + me.age);
```

这个程序的输出是:

```
Mike, 63
Mike, 0
```

当第二次调用`me.setAge`时，`setAge`函数查看参数的值，并选择将`age`属性设置为 0，而不是无效的年龄-10。

现在让我们看看如何使用 getters 来检索对象的属性。

# 用 Getters 检索对象属性

在 JavaScript 中，getters 不如 setters 有用。要理解其中的原因，您需要理解其他面向对象编程语言(如 Java 和 C++)中 getters 和 setters 的用法。在这些语言中，当变量的访问级别是私有的，但您希望类的用户能够访问该变量的值时，getter 方法(或成员函数)是必需的。做到这一点并遵循良好的 OOP 设计的唯一方法是提供一个 getter 来检索值。

JavaScript 对象没有私有访问变量，所以所有属性都可以使用点符号直接从对象中访问。但是，如果我们想返回一些东西，而不仅仅是存储在属性中的原始数据，我们可能还想提供一个 getter。

这里有一个例子。我定义了一个`Timer`对象，它以秒为单位存储时间，但通过一个 getter 以分钟为单位返回时间。下面是对象定义和测试它的程序:

```
let Timer = {
  start: 0,
  stop: 0,
  get time() {
    return (this.stop - this.start) / 60;
  }
}let someTime = Timer;
someTime.start = 0;
someTime.stop = 230;
print(someTime.time.toPrecision(3));
```

这个程序的输出是:

```
3.83
```

在这种情况下，我没有使用 setter，所以我可以突出显示 getter，所以我将 setter 留给读者作为练习。

# 可以在对象创建后添加 Getters 和 Setters

创建对象后，可以为对象定义 getters 和 setters。下面是你如何从上面用`Timer`对象做到这一点。首先，让我们重新定义没有 getter 或 setter 的 Timer:

```
Object.defineProperty(someTime, 'time', {
  get: function() {return (this.stop - this.start) / 60; }
});
```

如果您不熟悉`Object.defineProperty`，我将在以后的文章中详细介绍。

下面是测试这个新 getter 的完整程序:

```
function Timer(start, stop) {
  this.start = start,
  this.stop = stop
}let someTime = new Timer(0, 230);
print("Starting time: " + someTime.start);
print("Stopping time: " + someTime.stop);Object.defineProperty(someTime, 'time', {
  get: function() {return (this.stop - this.start) / 60; }
});print("Time elapsed in minutes: " + someTime.time.toPrecision(3));
```

这个程序的输出是:

```
Starting time: 0
Stopping time: 230
Time elapsed in minutes: 3.83
```

# 使用 Getters 和 Setters

在基于对象的 JavaScript 程序中，Getters 和 setters 并不是必需的，但它们确实使一些编程变得更容易，并使一些事情成为可能，比如数据验证，没有它们是不可能的。如果您打算在程序中创建和使用对象，您应该认真考虑对您的对象使用 getters 和 setters。

感谢您的阅读，请将您的意见和建议发邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。