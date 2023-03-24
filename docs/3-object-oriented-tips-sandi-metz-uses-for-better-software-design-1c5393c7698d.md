# 高级开发人员对弹性软件使用什么原则？

> 原文：<https://levelup.gitconnected.com/3-object-oriented-tips-sandi-metz-uses-for-better-software-design-1c5393c7698d>

## Sandi Metz 关于面向对象设计的三大开发经验

![](img/fc1eda389bebffb6deadd3bf9e770ce0.png)

照片由[克里斯蒂娜·莫里洛](https://www.pexels.com/@divinetechygirl?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从 [Pexels](https://www.pexels.com/photo/group-of-women-sitting-on-chairs-1181625/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

你需要添加另一个条件。有很多条件的复杂代码。你再加一个假设，然后继续前进。但是问题依然存在。

那是怎么发生的？最后是怎么得到这么复杂的代码的？

我知道简单的解决方法。我选择容易的道路。过了一会儿，我看到了一个更简单的解决方案，但是代码已经投入生产。没有人，但是没有人接触生产代码。

你如何防止这种情况？桑迪梅茨有我们应该遵循的伟大原则。

# 避免简单—变得简单

争取松散耦合的代码。`Product`不应该知道`Price`的存在。尽可能减少类依赖。

```
class Product {
...
 Product() { ... new Price(20.0d).getPrice() ... }
...
}
```

避免在对象内部实例化对象。增加依赖性、不必要的复杂性，并导致灵活性降低。

当`Price`发生变化的时候，`Product`也会发生变化。违反了面向对象编程的开放/封闭原则。违规导致代码发臭。

在这个实现中，你不能使用不同的`Price`类型。我们再加上`PromotionPrice`，或者`SeasonalPrice`。你知道必须改变`Product`类来适应新的价格类型。

```
class Product {
...
 Product(Price price) { ... price.getPrice() ...}
...
}
```

相反，将`Price`作为参数传递，然后使用它。这个类将为价格的子集工作，不需要任何改变。

添加一个新的`Price`子类将与该设计无缝配合。

看看代码中的条件。

```
if(condition1)
{ 
 behaviorMethod1();
}
else { 
 behaviorMethod2();
}
```

将方法移入类中。适当的时候注射。注入减少了执行，使代码可读，并且更容易测试。易于测试的代码也很容易被你理解。

> 简单是可靠的先决条件

容易意味着近。避免轻易接近。喜欢简单的。

容易是熟悉的。做熟悉的事情导致没有进步。

容易也意味着近。接近我们的能力。作为程序员，我们不喜欢有些东西遥不可及。我们是聪明人。

轻松是相对的。对我来说容易，对你来说不容易。比起简单的方法，更喜欢简单的方法。不要拿现有的代码，因为很容易。重构，让它更简单。

# 什么都不是

让我们看看下面的例子。你想得到一个数字账户名。如果数字帐户为空，则返回丢失的消息。

```
DigitalAccount digitalAccount = fetchDigitalAccount(id);if(digitalAccount == null)
{ 
 return "Missing digital account"; 
}
else { 
 return digitalAccount.getName();
}
```

糟糕的解决方案。您应该探索多态解决方案。子类比条件更能揭示意图。

> 条件滋生。—桑迪·梅茨

时间流逝，条件倍增。在前面的代码中添加一个额外的条件会增加难度。很难改变，因为它在你的代码库中成倍增加。

Null 是某种东西。有时候。有时候就是这样，没什么。

以之前的代码为例。Null 是某物，它是一个丢失的帐户。

丢失的帐户没有名称，因此行为可能会改变。做好准备。使用空对象模式。

```
class MissingAccount extends Account {
 @Override
 protected String getName() {
  return "Missing account!";
 }
}
```

从帐户创建一个缺失的帐户子类。当获取名称时，返回丢失的帐户。

我有意延长了`Account`。DigitalAccount 是一个子类，继承它会导致错误的继承。下一节将详细介绍错误的继承。

# 不要滥用继承

错误的继承无处不在。我做错了，你也做错了。让我们纠正这一点。

调查现有的继承。不要盲目地扩展类。看看超类，看看你继承了什么。

```
// wrong inheritance
class Product { // reasonable for all products
 getPrice() {...} // reasonable for tangible products
 getCostOfShipping() {...}
 getWeight() {...}}class DigitalProduct extends Product {
 // reasonable for all products
 getPrice() {...} // not reasonable for digital products
 getCostOfShipping() {...}
 getWeight() {...} 
}class PhysicalProduct extends Product  { 
 // all the inherited methods are reasonable
}
```

子类在某些情况下，从超类中提取特定的行为。将通用逻辑提取到更通用的类中。

超类应该是抽象的。不应该存在它的任何实例。多个子类应该实现超类。

只有一个子类扩展超类是错的。根据定义，一个抽象类应该至少有两个子类。如果不是，就把所有东西放在一个类中。

```
// correct approach; right inheritanceabstract class Product { // reasonable for all products
 getPrice() {...}}class ElectronicBook extends Product { 
}class PrintedBook extends Product  { 
 getCostOfShipping() {...}
 getWeight() {...}
}
```

# 结论

你应该从这篇文章中得到什么？

学会简单。不要选择简单的解决方案。它们会适得其反。从长远来看，它们会适得其反。

请注意 null。Null 可以是某些东西。有时候就是这样。空。

不要滥用继承。了解关于超类的更多信息。如果需要，实现您自己的。

# 资源

[空是说话的东西](https://www.youtube.com/watch?v=OMPfEXIlTVE&ab_channel=Confreaks)

Ruby — Sandi Metz 中实用的面向对象设计

# 继续阅读相关文章

[](/13-quotes-every-great-developer-should-know-70a972596a05) [## 每个伟大的开发人员都应该知道的 13 条名言

### 你应该知道和思考的 13 条开发者名言

levelup.gitconnected.com](/13-quotes-every-great-developer-should-know-70a972596a05) [](/i-found-the-best-gilded-rose-kata-solution-a896ffd8f7ac) [## 我找到了最好的镀金玫瑰形解决方案

### 通过桑迪梅斯解决镀金玫瑰形

levelup.gitconnected.com](/i-found-the-best-gilded-rose-kata-solution-a896ffd8f7ac)