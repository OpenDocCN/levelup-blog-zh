# 如何用简单工厂模式和策略模式重构 GildedRose-Refactoring-Kata

> 原文：<https://levelup.gitconnected.com/how-to-refactor-gildedrose-refactoring-kata-with-simple-factory-pattern-and-strategy-pattern-c6b4d6c04d96>

![](img/89087dac330964687115e9b09f1cddae.png)

我在 GitHub 上发现了一个有趣的项目——[**GildedRose-Refactoring-Kata**](https://github.com/emilybache/GildedRose-Refactoring-Kata)。它为一系列编程语言中的重构练习提供了一个起始代码。原则是我们应该创建足够的单元测试来覆盖所有情况(尤其是边缘情况)，然后开始重构代码。让我们试着做一些练习。

最初的项目有一个庞大的方法，其中包含许多用于更新项目属性的`if else`块。关于重构，有一个基于需求的限制——“不要改变`Item`类或项目属性”。这是一种陷阱，因为我首先想到的是使用简单工厂模式，这意味着我们需要将`Item`类修改为抽象类，然后为不同的项目分别创建派生类。无论如何，让我们看看如果允许我们修改`Item`类，我们能做些什么。

# 简单工厂模式

简单工厂模式可能是使用最广泛的模式之一。有一个用于创建其他对象的`factory`对象，所以我们不需要使用`new`关键字在代码中频繁地创建不同的对象。工厂创建的对象应该有相同的父类，但是它们根据类型执行不同的任务。它易于使用，适用于许多场景。“最简单”并不意味着它不够好。我们可以在交货和时间成本之间找到平衡点。

对于这种情况，`Item`类将是基类。我可以添加一个抽象方法来计算`SellIn`和`Quality`:

```
public abstract class Item
{
    public string Name { get; set; }
    public int SellIn { get; set; }
    public int Quality { get; set; }
​
    protected Item(string name, int sellIn, int quality)
    {
        Name = name;
        SellIn = sellIn;
        Quality = quality;
    }
    public abstract void Update();
}
```

`Item`类现在是`abstract`，所以它不能被直接实例化。现在我们可以创建派生类来更新不同类型的这些属性。为简单起见，我将创建一个 **StandardItem** 类型和一个 **AgedBrie** 类型作为示例，如下所示:

```
public class StandardItem : Item
{
    public StandardItem(string name, int sellIn, int quality) 
        : base(name, sellIn, quality) { }
    public override void Update()
    {
        SellIn = SellIn - 1;
        Quality = Quality - 2;
        if (Quality < 0)
        {
            Quality = 0;
        }
        else if(Quality > 50)
        {
            Quality = 50;
        }
    }
}
​
public class AgedBrie : Item
{
    public AgedBrie(string name, int sellIn, int quality) 
        : base(name, sellIn, quality)
    {
    }
​
    public override void Update()
    {
        SellIn = SellIn - 1;
        Quality = Quality + 1;
        if (Quality < 0)
        {
            Quality = 0;
        }
        else if (Quality > 50)
        {
            Quality = 50;
        }
        //The above validation can be refactored as well
    }
}
```

但是在这里我们仍然可以看到在检索`Quality`的值时出现了一些重复的代码。所以更好的方法是将这些验证转移到基类`Item`中`Quality`属性的`getter`方法中，或者我们可以在基类中有一个默认的实现。

通过创建这些派生类，我们可以将逻辑从大的`GildedRose`类提取到派生类中。接下来，我们需要一个工厂来生产它们。

```
public class ItemFactory
{
    public static Item CreateItem(string name, int sellIn, int quality)
    {
        switch (name)
        {
            case "Aged Brie":
                return new AgedBrie(name, sellIn, quality);
            ...
            default:
                return new StandardItem(name, sellIn, quality);
        }
    }
}
```

下一步是更新`Program`类中的`Main`方法:

```
IList<Item> Items = new List<Item>{
    //Call the factory to create the derived classes:
    ItemFactory.CreateItem("+5 Dexterity Vest", 10, 20),
...
```

我们使用工厂来生产产品。现在更新`GildedRose`类中的`UpdateQuality`方法:

```
public void UpdateQuality()
{
    foreach (var item in Items)
    {
        item.Update();
    }
}
```

因为每个派生项都有自己的方法来更新属性，所以我们可以得到正确的值。

但是，如果我们不能修改`Item`类呢？

一种变化是使用接口来扩展类的行为。我们不需要在`Item`类中添加`Update`方法。相反，我们可以创建一个提供`Update`方法的新接口。所以派生类可以实现这个接口来更新它的属性:

```
public interface IUpdate
{
    void UpdateSellIn();
    void UpdateQuality();
}
​
public class AgedBrie : Item, IUpdate
{
    ...
}
```

派生类也应该实现`IUpdate`接口。所以基本逻辑没有太大变化，只需要分别改变`UpdateQuality`方法:

```
public void UpdateQuality()
{
    foreach (var item in Items)
    {
        ((IUpdate)item).UpdateSellIn();
        ((IUpdate)item).UpdateQuality();
    }
}
```

对于一些 C#面试，一个常见的问题是“抽象类和接口的区别”。基本上，典型的答案可能是:

*   抽象类可以有函数的访问说明符，但接口不能。换句话说，您可以使用`private`、`public`等用于内部函数，但是在界面中，默认情况下所有函数都是公共的。
*   抽象类可以有默认实现，但接口不能。
*   抽象类可以有字段和常量，但接口不能有字段。
*   抽象类可以有非抽象方法，但接口不能。
*   抽象类可以有构造函数，但接口不能。
*   一个类只能从一个抽象类派生，但是它可以实现多个接口。
*   两者都不能实例化。
*   …

> *注意:请记住* ***“接口不能有实现”在今天是不正确的，因为接口支持 C# 8.0 的默认实现！*** *还有，从 C# 8.0 开始对接口有了更多的改变:C# 8.0 中的接口可以有* `*private*` *、* `*static*` *、* `*protected*` *、* `*virtual*` *成员。也许这个面试问题可以更新。仅供参考:* [*接口*](https://devblogs.microsoft.com/dotnet/default-implementations-in-interfaces/)*[*中的默认实现教程:用 C# 8.0*](https://docs.microsoft.com/zh-cn/dotnet/csharp/tutorials/default-interface-methods-versions) *中的默认接口方法更新接口。**

*那么我们什么时候应该使用抽象类或者接口呢？从我的理解来说，答案是“看情况”。抽象类用于定义具有逻辑继承关系的对象的实际身份，但接口更可能用于扩展对象的行为。在依赖注入模式中，我们主要使用接口。*

*这有点跑题了。让我们回到重构。老实说，工厂模式的变化不够好——因为它混淆了**对象**和**行为**。实际上，我们应该对用于计算属性的方法进行抽象。但是物品本身应该是一样的。让我们继续前进。*

# *战略模式*

*设计模式有多种类别:*

*   *创造模式——创造各种各样的对象*
*   *结构模式—将对象组装成更大的结构*
*   *行为模式——更加关注算法和对象之间的责任分配。*

*对于这个实践，我们可以使用行为模式之一——策略模式来分离计算逻辑和对象。这种模式允许我们在不改变原来的`Item`类的情况下拥有多种算法。*

*首先，我们需要定义一个接口来声明执行策略的方法:*

```
*public interface IStrategy
{
    (int sellIn, int quality) Update(Item item)
}*
```

*如果我们使用 C# 8.0，我们可以在这里使用默认实现，例如重置`SellIn`的值并验证`Quality`的值。*

*然后我们将定义具体的策略:*

```
*public class StandardStrategy : IStrategy
{
    public (int sellIn, int quality) Update(Item item)
    {
        var quality = item.Quality - 2;
        ...
        return (item.SellIn - 1, quality);
    }
}
​
​
public class AgedBrieStrategy : IStrategy
{
    public (int sellIn, int quality) Update(Item item)
    {
        var quality = item.Quality + 1;
        ...
        return (item.SellIn - 1, quality);
    }
}*
```

*然后我们需要创建一个`Context`类来定义客户感兴趣的接口:*

```
*public class StrategyContext
{
    private readonly IStrategy _strategy = null;
​
    public StrategyContext(string name)
    {
        switch (name)
        {
            case "Aged Brie":
                _strategy = new AgedBrieStrategy();
                break;
            ...
            default:
                _strategy = new StandardStrategy();
                break;
        }
    }
​
    public (int sellIn, int quality) Update(Item item)
    {
        return _strategy.Update(item);
    }
}*
```

*接下来我们可以在`GildedRose`类中应用`UpdateQuality`方法中的策略:*

```
*public void UpdateQuality()
{
    foreach (var item in Items)
    {
        var context = new StrategyContext(item.Name);
        var result = context.Update(item);
        item.SellIn = result.sellIn;
        item.Quality = result.quality;
    }
}*
```

*这次我们没有改变`Program`类中的`Item`类和`Main`方法。如果你觉得这里的`new`关键字不漂亮，可以继续使用工厂模式，将用于创建正确策略的逻辑解耦到另一部分。但是现在我们已经将计算行为从对象本身解耦到另一个类。*

# *摘要*

*我的回购可以在这里找到:[https://github . com/yanxiaodi/GildedRose-Refactoring-Kata-Solutions](https://github.com/yanxiaodi/GildedRose-Refactoring-Kata-Solutions)。因为我没有创建足够的单元测试，所以不能保证结果。演示是为了展示如何应用这些设计模式，而不是具体的实现。当我们开始重构代码时，最重要的事情是创建足够多的单元测试，以确保重构不会破坏当前的功能。原始项目已经有一个单元测试样本，所以我们可以添加更多的测试来覆盖那些**边缘案例* *。要做到 100%覆盖并不容易，但值得去做。*

*显然，我不认为有人会像最初的项目那样编写代码。我一直在想的一件事是，当我们有一个新的需求时，我们应该如何通过使用一些复杂的设计模式或者仅仅是`if else`来设计它？换句话说，有时客户可能只是因为预算限制而要求您尽快实现该功能，而不关心您的底层实现。假设我们有两个选择:*

*   *开发者 A 是个新手，只用`if else`就能在 2 小时内完成功能。然而，它确实像预期的那样工作。*
*   *开发人员 B 更有经验，但需要更多的时间来设计模式，所以时间成本将是 4 小时。但是将来增加更多的功能是很容易的。*

*你会怎么选择？*