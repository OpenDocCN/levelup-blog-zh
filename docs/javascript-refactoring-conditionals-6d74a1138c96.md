# JavaScript 重构—条件

> 原文：<https://levelup.gitconnected.com/javascript-refactoring-conditionals-6d74a1138c96>

![](img/58a7d6634a623fe1915b26f765744414.png)

照片由 [Geert Pieters](https://unsplash.com/@shotsbywolf?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们可以清理我们的 JavaScript 代码，这样我们可以更容易地使用它们。

在本文中，我们将研究一些与清理 JavaScript 条件相关的重构思想。

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
let isIE = userAgent.includes("ie");
let isMacIE = isMac && isIE;
```

我们打破了条件表达式，将更小的表达式分配到它们自己的变量中，然后将它们组合起来，使一切更容易阅读。

# 巩固条件表达式

如果我们有多个短条件表达式分配给它们自己的变量，那么我们可以把它们合并成一个。

例如，不要写:

```
const x = 5;
const bigEnough = x > 5;
const smallEnough = x < 6;
const inRange = bigEnough && smallEnough;
```

我们写道:

```
const x = 5;
const inRange = x > 5 && x < 6;
```

因为表达式很短，即使把它们组合在一起也不会让它变得更长，所以我们可以这样做。

# 合并重复的条件片段

如果我们在一个条件块中有重复的表达式或语句，那么我们可以将它们移出。

例如，不写:

```
if (price > 100) {
  //...
  complete();
} else {
  //...
  complete();
}
```

我们写道:

```
if (price > 100) {
  //...  
} else {
  //...  
}
complete();
```

这样，我们就不必重复调用`complete`函数了。

# 移除控制标志

如果我们对一个循环使用了控制标志，那么我们经常会看到这样的循环:

```
let done = false;
while (!done) {
  if (condition) {
    done = true;
  }
  //...
}
```

在上面的代码中，`done`是控制标志，我们将`done`设置为`true`，以便当`condition`为`true`时，我们停止`while`循环。

相反，我们可以使用`break`来停止循环，如下所示:

```
let done = false;
while (!done) {
  if (condition) {
    break;
  }
  //...
}
```

# 用保护子句替换嵌套条件

嵌套的条件语句非常难读，所以我们可以使用 guard 子句来代替它们。

例如，代替下面的嵌套条件语句:

```
const fn = () => {
  if (foo) {
    if (bar) {
      if (baz) {
        //...
      }
    }
  }
}
```

我们写道:

```
const fn = () => {
  if (!foo) {
    return;
  } if (!bar) {
    return;
  } if (baz) {
    //...
  }
}
```

在上面的代码中，保护子句是:

```
if (!foo) {
  return;
}
```

并且:

```
if (!bar) {
  return;
}
```

如果这些条件为假，它们会提前返回函数。

有了它，我们就不需要筑巢了。

![](img/6f89e4f68259a13f1826390a9f6d2891.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 用多态替换条件

我们可以为每种数据创建子类，然后拥有不同的方法来适应它所调用的对象类型，而不是用一个`switch`语句对不同类型的数据做同样的事情。

例如，不要写以下内容:

```
class Animal {
  constructor(type) {
    this.type = type;
  } getBaseSpeed() {
    return 100;
  } getSpeed() {
    switch (this.type) {
      case ('cat'): {
        return getBaseSpeed() * 1.5
      }
      case ('dog'): {
        return getBaseSpeed() * 2
      }
      default: {
        return getBaseSpeed()
      }
    }
  }
}
```

我们写下如下内容:

```
class Animal {
  constructor(type) {
    this.type = type;
  } getBaseSpeed() {
    return 100;
  }
}class Cat extends Animal {
  getSpeed() {
    return super.getBaseSpeed() * 1.5;
  }
}class Dog extends Animal {
  getSpeed() {
    return super.getBaseSpeed() * 2;
  }
}
```

我们的`switch`语句很长，我们必须为不同种类的对象定制`case`块。

因此，我们最好将 switch 语句变成它们自己的子类，这样它们就可以有自己的方法来获得速度。

# 引入空对象

如果我们重复检查了`null`或`undefined`，那么我们可以定义一个子类来表示这个类的`null`或`undefined`版本，然后使用它。

例如，不写:

```
class Person {
  //...
}
```

我们写道:

```
class Person {
  //...
}class NullPerson extends Person {
  //...
}
```

然后，不是设置对象的属性，其中`Person`是`null`或`undefined`，而是将其设置为`NullPerson`实例。

这消除了用条件检查这些值的需要。

# 结论

我们可以创建一个`null`或`undefined`版本的类来设置它，而不是`null`或`undefined`。

此外，我们可以将长条件表达式分解成更小的表达式，并将小表达式组合成大表达式。