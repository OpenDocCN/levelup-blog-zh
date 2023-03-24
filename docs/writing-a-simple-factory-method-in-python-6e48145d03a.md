# 用 Python 编写简单的工厂方法

> 原文：<https://levelup.gitconnected.com/writing-a-simple-factory-method-in-python-6e48145d03a>

![](img/2cdecb303a227acd6eb1d98cd4a848b5.png)

照片由 [Jantine Doornbos](https://unsplash.com/@jantined?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你以前读过 [*设计模式*](https://en.wikipedia.org/wiki/Design_Patterns) 的话，你可能已经了解了一种叫做“工厂方法”的经典模式。该模式允许基类的子类指定要创建的对象，从而使基类更加灵活。与其将对象名硬编码到父类中，不如将它们委托给子类。这种模式使得扩展类变得更容易，并确保了更松散耦合的对象。

例如，假设您有一个父类`Bicycle`。你希望每辆`Bicycle`都有一套轮胎(我希望如此)。所以，每次一个新的`Bicycle`被初始化时，你就自动把`tires`分配给`GenericTires`。看起来不错，对吧？但是如果你想要一个不同类型的`Bicycle`和一个不同类型的`tires`呢？如果`GenericTires`接口变了或者你想完全换出来怎么办？您必须更新父`Bicycle`类。像这样梳理代码并更新硬编码的元素是乏味的、不灵活的并且容易出错。

使用工厂方法的目标是使对象更具可扩展性和抽象性，从而更易于使用。经验法则是:

> "在单独的操作中创建对象，这样子类可以覆盖它们的创建方式." *—* [设计模式(1994)](https://en.wikipedia.org/wiki/Design_Patterns)

这正是我们将在 Python 中探索如何做的。

## 最初的设计

在第一个例子中，我们将回顾一段可以应用工厂方法的代码通常是什么样子。我们将使用我们便利的`Bicycle`例子，因为谁不喜欢骑自行车兜风呢？我还发现，当类模拟我们熟悉的物理事物时，更容易理解这样的概念。Sandi Metz 也经常在示例代码中使用构建自行车，我非常喜欢这种类比。让我们开始吧。

```
class Bicycle:
    def __init__(self):
        self.tires = GenericTires() def get_tire_type(self):
        return self.tires.tire_type()class GenericTires:
    def tire_type(self):
        return 'generic'bike = Bicycle()
print(bike.get_tire_type())
```

在这个例子中，父类`Bicycle`通过直接引用类用一组`GenericTires`初始化。这使得`Bicycle`和`GenericTires`之间的耦合更加紧密和刚性。创建不同类型的自行车是可能的，但是在这个例子中改变它们的轮胎是不直观的。

假设我们想制造一种新型自行车。一辆山地自行车。它可能应该有一些特殊的轮胎来应对崎岖的地形，对不对？我们如何调整基本的`Bicycle`等级来适应不同种类的轮胎？

这里有一个稍微灵活的设计:

```
class Bicycle:
    def __init__(self):
        self.tires = self.add_tires() def add_tires(self):
        return GenericTires() def get_tire_type(self):
        return self.tires.tire_type()class MountainBike(Bicycle):
    def add_tires(self):
        return MountainTires()class GenericTires:
    def tire_type(self):
        return 'generic'class MountainTires:
    def tire_type(self):
        return 'mountain'mountain_bike = MountainBike()
print(mountain_bike.get_tire_type())
```

在这个例子中，我们添加了一个新的`add_tires()`方法来返回正确的轮胎类别。这让我们可以在`Bicycle`的任何子类中覆盖该方法，并提供不同类别的轮胎。为了添加新的自行车和轮胎，您只需要添加新的类并覆盖方法。您不必对基类进行调整。接下来，让我们来看看当你有更复杂的自行车零件时，进一步扩展这一点。

## 更灵活的设计

这里我们展示了一个更灵活和“客户端友好”的设计，类似于抽象工厂模式。该示例向父类添加了一个`factory`参数，该参数将接受一种“零件工厂”,用于指定要创建的轮胎或其他零件的类型。

```
class Bicycle:
    def __init__(self, factory):
        self.tires = factory().add_tires()
        self.frame = factory().add_frame()class GenericFactory:
    def add_tires(self):
        return GenericTires() def add_frame(self):
        return GenericFrame()class MountainFactory:
    def add_tires(self):
        return RuggedTires() def add_frame(self):
        return SturdyFrame()class RoadFactory:
    def add_tires(self):
        return RoadTires() def add_frame(self):
        return LightFrame()class GenericTires:
    def part_type(self):
        return 'generic_tires'class RuggedTires:
    def part_type(self):
        return 'rugged_tires'class RoadTires:
    def part_type(self):
        return 'road_tires'class GenericFrame:
    def part_type(self):
        return 'generic_frame'class SturdyFrame:
    def part_type(self):
        return 'sturdy_frame'class LightFrame:
    def part_type(self):
        return 'light_frame'bike = Bicycle(GenericFactory)
print(bike.tires.part_type())
print(bike.frame.part_type())mountain_bike = Bicycle(MountainFactory)
print(mountain_bike.tires.part_type())
print(mountain_bike.frame.part_type())road_bike = Bicycle(RoadFactory)
print(road_bike.tires.part_type())
print(road_bike.frame.part_type())
```

我们不再指定要安装在父类中的部件类型。添加零件由零件工厂内各自的`add_tires()`和`add_frame()`方法处理。部件工厂只是一个单独的类，处理每个具体部件的添加。父类`Bicycle`调用方法在部件工厂类中添加部件。每个实例仍然是一个`Bicycle`，但是由不同的部分组成。

您还可以采取一些额外的步骤来进一步抽象前面的一组类:

*   将`add_tires()`和`add_frame()`推广到`add_part('type')`
*   在父类`Bicycle`中创建一个方法，用于显示与自行车相关的所有部件。你可以通过简单地获取每一个`part_type()`或者将`self.tires`和`self.frame`移动到一个列表或者零件图中进行迭代来实现。
*   现在`Bicycle`期待一个零件工厂被传入。您可以将`GenericFactory`作为默认赋值包含在类中，它可以被新工厂覆盖。

这些例子只是用 Python 编写和扩展工厂模式的一些方法。还有许多其他的变化。你选择哪一个完全取决于项目的大小和课程的复杂程度。在某些情况下，也许您只需要覆盖父类中的一个方法，在其他情况下，您需要一个专用的工厂类来实例化多个对象。最终，无论您选择哪个选项，都应该感觉自然，并为客户提供简单、可扩展的界面。

*感谢阅读！希望您喜欢学习 Python 中工厂方法设计模式的工作方式。如果您不想要更多有趣的 Python 指南，请查看一下* [*5 种 Python 字符串方法，以便更好地格式化*](/5-python-string-methods-for-better-formatting-a2c3b92a2d33) *。*