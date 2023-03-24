# JavaScript 最佳实践—改进类

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-improving-classes-b343d5c1102a>

![](img/885903a91de0deb568420861fc33bf9b.png)

[NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

使用默认参数和属性缩写，清理我们的 JavaScript 代码很容易。

在本文中，我们将研究创建类的最佳实践，以及何时应该创建它们。

# 构造器

为了让我们的构造函数变得更好，我们应该做一些事情。它们如下。

# 如果可能，初始化所有构造函数中的所有成员数据

我们应该把它们都放在构造函数中，这样当我们实例化对象时它们都被初始化了。

所以我们可以写:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
```

现在我们确保所有的东西都用一个值初始化了。

# 在构造函数中创建一个单例

如果我们只需要构造函数的一个实例，那么我们可以创建它的一个实例。

例如，我们可以编写以下内容:

```
class Person {
  constructor(name) {
    if (this.instance) {
      this.instance = {
        name
      }
    }
    return this.instance;
  }
}
```

在上面的代码中，如果`this.instance`还没有定义，我们返回我们创建的对象。

否则，我们返回设置为`this.instance`的值。

# 除非证明并非如此，否则更喜欢深层副本而不是浅层副本

深层拷贝拷贝所有东西，所以这比浅层拷贝好多了。浅层拷贝会留下一些引用原始对象的内容。

如果我们想要一个真实的副本，那就不好了。

因此，我们必须让我们的代码生成深层副本，如下所示:

```
const copy = obj => {
  const copied = {
    ...obj
  };
  for (const k of Object.keys(obj)) {
    if (typeof obj[k] === 'object') {
      copied[k] = {
        ...copied[k]
      };
      copy(copied[k]);
    }
  }
  return copied;
}
```

我们只是使用 spread 操作符来复制嵌套的对象，如果找到一个的话。递归地做同样的事情。

然后我们返回复制的对象。

# 我们应该什么时候创建一个类？

我们不应该总是创造阶级。在一些情况下，创建一个类是有意义的。

## 模拟真实世界的物体

类对于建模现实世界的对象非常有用，因为它们建模对象的行为

它们让我们将实例变量和方法封装到一个包中，分别存储状态和对对象执行操作。

## 模型抽象对象

同样，我们可以使用类来建模抽象对象。

它们可以用来进行抽象，抽象是不同种类对象的概括。

类非常适合保存子类的共享成员。子类可以继承它们。

然而，我们应该保持继承树的简单，这样人们就不会对代码感到困惑。

## 降低复杂性

我们可以使用类来降低程序的复杂性。

类非常适合隐藏信息。在 JavaScript 中，类中还没有私有变量，所以我们必须在方法中隐藏数据。

这样我们就可以最小化程序不同部分之间的耦合。

## 隐藏实施细节

方法也有利于隐藏实现细节。

我们可以将细节隐藏在方法中，只运行需要的东西。

为此，我们可以在方法中嵌套函数和变量。

## 限制变化的影响

变化的影响可以被减少，因为我们可以隐藏事情。

与隐藏实现一样，可以通过限制方法中更改的影响来隔离更改的影响。

## 隐藏全局数据

通过将全局数据放入类的方法中，它们可以变成私有数据。

那他们就不用暴露在公众面前了。我们所要做的就是使用`let`和`const`在方法中声明它们。

![](img/47de048c62946fdb7205ff931fa041ab.png)

照片由[亨德里克·科内里森](https://unsplash.com/@the_bracketeer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 流线参数传递

如果我们将相同的参数传递给不同的函数，那么我们可以将参数更改为实例变量，将函数更改为方法。

例如，如果我们有:

```
const speak = (name) => `${name} spoke`;
const greet = (name) => `hi, ${name}`;
```

然后我们可以将这些方法放入它们自己的类中，如下所示:

```
class Person {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} spoke`;
  }
  greet() {
    return `hi, ${this.name}`;
  }
}
```

现在我们不必到处都经过`name`。

我们只是创建了一个`Person`的实例，并在不传入任何参数的情况下调用这些方法。

# 结论

我们可以创建类来封装数据和打包东西。然而，我们不应该为每件事创建类。

此外，我们应该尽可能制作深层拷贝而不是浅层拷贝。