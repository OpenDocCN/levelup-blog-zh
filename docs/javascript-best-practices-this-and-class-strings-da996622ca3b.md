# JavaScript 最佳实践— This 和 Class 字符串

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-this-and-class-strings-da996622ca3b>

![](img/ae2788efab53a671dbe7fef1736fb124.png)

[李东旻](https://unsplash.com/@alexleegdp?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将看看如何返回`this`来让我们将类方法链接在一起，并编写一个`toString`方法来确保我们的类正常工作并且不会产生副作用。

# 方法可以返回`this`来帮助方法链接

在我们的 JavaScript 类中，我们可以在方法中返回`this`，这样我们就可以用修改后的`this`值链接我们的方法。

在每个返回`this`的方法中，我们可以修改`this`的值，这样我们就可以编写代码将这些方法链接在一起，并在这些方法被一起调用时获得我们想要的结果。

例如，我们可以编写如下代码:

```
class Box {
  setHeight(height) {
    this.height = height;
    return this;
  } setWidth(width) {
    this.width = width;
    return this;
  }
}
```

在上面的代码中，我们有带有`setHeight`和`setWidth`方法的`Box`类。

在每个方法中，我们用作为每个方法的参数传入的值来更改`this`的一个属性。

然后我们可以如下链接我们的`Box`实例方法调用:

```
new Box().setHeight(10).setWidth(15);
```

然后，在我们的控制台日志中，如果我们记录上面的方法链表达式的返回值，我们会得到以下输出:

```
Box {height: 10, width: 15}
```

正如我们所见，`height`和`width`属性是由上面的方法调用链设置的。

# 在我们的类中编写一个`toString()`方法，以确保它能够成功工作

我们可以在 JavaScript 类中编写自己的`toString`方法，这样我们就可以看到它成功地工作了。在方法中，我们可以返回类成员的值，这样我们就知道它们设置正确。

例如，我们可以编写以下代码向我们的类添加一个`toString`方法:

```
class Box {
  setHeight(height) {
    this.height = height;
    return this;
  } setWidth(width) {
    this.width = width;
    return this;
  } toString() {
    return `height: ${this.height}, width: ${this.width}`;
  }
}
```

在上面的代码中，我们有一个`toString`方法，它返回一个值为`this.height`和`this.width`的字符串，它们是我们的`Box`实例的成员。

然后当我们调用下面的方法链时:

```
new Box().setHeight(10).setWidth(15).toString();
```

我们看到从`toString`返回的值是:

```
height: 10, width: 15
```

我们确认成员设置正确。

# 空的构造函数或者只是委托给父类的函数是不必要的

在 JavaScript 中，我们不必为每个类提供一个构造函数。如果我们只是用与父类相同的参数调用父类构造函数，我们也不需要从子类调用父类构造函数。

例如，我们不是编写下面的代码来添加空的构造函数:

```
class Foo {
  constructor() {}
}
```

我们可以跳过`Foo`类中的`constructor`方法，如下所示:

```
class Foo {}
```

它们都做同样的事情，所以我们不需要额外的空`constructor`方法。

此外，我们不需要在子构造函数中编写只调用父构造函数的代码:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
}class Cat extends Animal {
  constructor(...args) {
    super(...args);
  }
}
```

在上面的代码中，我们在`Cat`中有一个`super`调用，它用我们传入的任何内容调用父`Animal`构造函数。

当我们如下创建一个`Cat`实例时:

```
const cat = new Cat('jane');
```

我们从控制台日志中获得值`Cat {name: “jane”}`。

在这种情况下，我们可以省略构造函数。因此，我们可以这样写:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
}class Cat extends Animal {}
```

没有了`Cat`中的构造函数，我们得到的结果和上一个例子一样，所以我们应该把它去掉。

![](img/d72dff891db799273d30404e008aadd1.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

在被我们的方法修改后，我们的类方法可以返回`this`来返回`this`的值。

如果我们在父类中有一个空的构造函数，或者在子类中有一个用与父构造函数相同的参数调用`super`的构造函数，我们可以从代码中删除它们。

此外，我们可以使用一个`toString`方法返回一个包含类成员的字符串，以确保它们设置正确。