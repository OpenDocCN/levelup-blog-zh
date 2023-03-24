# 在 C#中实现单例设计反模式的 5 种方法

> 原文：<https://levelup.gitconnected.com/5-ways-to-implement-the-singleton-design-anti-pattern-in-c-68bb664c31f2>

![](img/05d7b3403ef1b7e74a835821406ab269.png)

照片由 [Csaba Balazs](https://unsplash.com/@balazscsaba2006?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*单例设计模式有助于将一个类的实例化限制为一个对象。*

关于单例设计模式的一些事实:

*   最讨厌的设计模式之一。
*   Erich Gamma 会从“四人帮”的书中去掉单例模式。
*   这种模式很容易被误用。

由于最后一个事实，对我来说，singleton 的经典实现更像是反模式的，而不是模式。

让我们看看这个模式有什么问题，我们如何减少它的缺陷，以及**我们能从不同的单例实现**中学到什么。

# 基于静态属性的实现

我们的第一个单例实现非常普通，只有几行代码，但是它只能在一定程度上工作。该实现确实允许创建单个实例，但并不像它应该的那样懒惰。

让我们看看代码:

这里有两个可能的触发器可以实例化一个 singleton 对象。第一个是显而易见的，也是我们所期望的——对实例属性的访问。但是第二个触发器不太明显——它是对 singleton 类的任何其他静态属性的访问。NET framework 在第一次访问类的任何静态成员之前调用类的静态构造函数。在我们的例子中，单例实例化发生在静态构造函数中，因为这一行:

```
private static readonly Singleton _instance = new Singleton();
```

在幕后编译成这段代码:

```
static Singleton()
{
    _instance = new Singleton();
}private static readonly Singleton _instance;
```

所以当前的实现在某种程度上是可行的。然而，如果单例的初始化尽可能的懒惰，你应该考虑使用懒惰类。

## 优点:

*   线程安全。
*   易于实施。

## 缺点:

*   不懒。
*   单一对象不能在运行时以多态的方式被另一种类型的对象替换。
*   使用 singleton 的代码是不可单元测试的。
*   该实现违反了单一责任原则，因为该类除了完成其主要工作之外，还负责创建自身。
*   使用单例将会导致代码的其余部分存在隐式依赖。我已经在我的另一篇文章中描述了隐式依赖的问题:

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) 

# 基于懒惰<t>类的实现</t>

前面的代码示例可以通过一个微小的改变得到显著的改进— Lazy <t>类。只有在访问 Instance 属性时，singleton 对象才会被实例化。</t>

## 优点:

*   线程安全。
*   百分百懒。
*   易于实施。

## 缺点:

*   同样，在运行时没有可扩展性。
*   同样，代码是不可测试的。
*   再次违反 SRP。
*   又是隐式依赖问题。

# 双重检查锁定实现

我喜欢这种单例实现，只是因为与其他实现相比，它在技术上很复杂。以下代码示例完全等同于上一个示例。然而，从学习的角度来看，这要有趣得多，因为它展示了如何使用锁同步、volatile 关键字和双重检查锁定技术。

锁定结构使得单例实现是线程安全的，同时需要双重检查锁定来提高性能。获取第 17 行的锁是一个开销很大的操作。如果已经创建了 singleton，就没有必要获取一个锁，然后尝试创建它。在第 15 行添加额外的 null 检查有助于避免不必要的锁尝试，这对性能更好。

## 优点:

*   线程安全。
*   百分百懒。

## 骗局

*   复杂的实现。
*   同样，在运行时没有可扩展性。
*   同样，代码是不可测试的。
*   再次违反 SRP。
*   又是隐式依赖问题。

# 周围环境

singleton 的一个主要缺点是在运行时缺乏灵活性。我们不能用 ExtendedSingleton 或 SingletonMock 替换 singleton 类。如果我们需要在运行时实现可扩展性，同时保持一个全局访问点，我们可以使用环境上下文模式。

## 优点:

*   运行时的可扩展性。
*   单一责任原则现在受到尊重。

## 缺点:

*   这不再是单一模式了。可以创建单例类的许多实例。
*   隐式依赖问题仍然存在。

# IoC 容器中的单例

像 Autofac 这样的控制容器的反转控制一个注册对象的生存期。所有的 IoC 容器都有称为 singleton 或类似的生存期范围。任何对象都可以注册为单个实例，这样就可以实现单例模式的主要目标。

## 优点:

*   易于实施。
*   使用“singleton”的代码现在是单元可测试的了。
*   没有违反 SRP。现在，IoC 容器负责创建单例，而不是单例类本身。
*   singleton 类将被注入到其他类的构造函数中，即显式使用。

## 缺点:

*   可以用 new 关键字创建许多实例。

# 结论

我看不出使用经典的 singleton 反模式背后有什么真正的动机。在以下情况下，单例仍然可以是一种模式:

*   它没有状态(改变应用程序一部分的状态会影响所有相关部分的行为)。
*   它是显式使用的(*private Singleton _ Singleton = Singleton)。类构造函数中的实例*。singleton 的使用并没有深藏在代码中)。
*   在需要被单元测试覆盖的代码中没有使用它。
*   它的用途仅限于最小数量的模块。

对我来说有很多如果。

# 更多设计模式

萨沙·马修斯·https://link.medium.com/VaQVf8Anvdb 的《企业应用中外观设计模式的 3 个主要用例》

[](/the-state-design-pattern-to-implement-likes-and-dislikes-958389b379ff) [## 如何使用状态设计模式实现好恶

### 从需求引出开始

levelup.gitconnected.com](/the-state-design-pattern-to-implement-likes-and-dislikes-958389b379ff) [](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd) [## 适配器设计模式的最简单解释

### C#中的真实世界示例

levelup.gitconnected.com](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd)