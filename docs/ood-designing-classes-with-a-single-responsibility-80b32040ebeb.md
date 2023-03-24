# Python 中的 OOD 设计具有单一职责的类

> 原文：<https://levelup.gitconnected.com/ood-designing-classes-with-a-single-responsibility-80b32040ebeb>

Sandi Metz 写的《实用的面向对象设计》是一部关于永恒的和基本的 OOD 知识的杰作。为了加深理解，我总结了每一章，并用 Python 重写了样本代码。当然，如果你想得到更多的细节，请得到这本书。

这篇文章是第 2 章“设计具有单一职责的类”的总结。

![](img/35f33978b4f3559e44b7db12ccd21fe1.png)

[https://github . com/shotin 93/object-oriented-design-in-python](https://github.com/shotin93/object-oriented-design-in-python)

# 决定什么属于一个类

这一章的重点是如何决定什么属于类。

首先，我们需要将方法分类。然而，我们不能在项目的早期正确地做到这一点，因为我们的应用程序需要在未来改变。

因此，我们必须将代码组织得易于更改。要做到这一点，代码应该遵循真实的原则；

*   透明:变化的结果应该在正在变化的代码中显而易见，并且在远处的代码依赖于它
*   合理:任何改变的成本都应该与改变所获得的收益成比例
*   可用:现有代码应该在新的和意想不到的环境中可用
*   示范性的:代码本身应该鼓励那些改变它的人保持这些品质

真实原则确保每个类都有一个单一的责任。

# 创建具有单一职责的类

代码示例是关于自行车及其齿轮的。齿轮有齿圈和轮齿，我们可以用齿圈除以齿轮的轮齿来计算传动比。

```
chainring = 52
cog = 11
ratio = chainring / float(cog)
print(ratio) #4.7272727272727275chainring = 30
cog = 27
ratio = chainring / float(cog)
print(ratio) #1.1111111111111112
```

当你在代码中写东西时，名词是类的候选者，在这个例子中是自行车和齿轮。Bicycle 可以是类，但是在这个例子中它没有数据或行为。另一方面，齿轮有，如链环，齿轮和比率，所以我们应该创建齿轮类。

```
class Gear:
  def __init__(self, chainring, cog):
    self.chainring = chainring
    self.cog = cog def ratio(self):
    return self.chainring / float(self.cog)print(Gear(52, 11).ratio()) #4.7272727272727275
print(Gear(30, 27).ratio()) #1.1111111111111112
```

我们计算了齿轮比，但骑自行车的人也想知道齿轮英寸。公式如下:

*   齿轮英寸=车轮直径*齿轮比
*   车轮直径=轮辋直径+两倍轮胎直径

我们可以很容易地添加这个新行为:

```
class Gear:
  def __init__(self, chainring, cog):
    self.chainring = chainring
    self.cog = cog def ratio(self):
    return self.chainring / float(self.cog) def gear_inches(self):
    return self.ratio() * (self.rim + (self.tire * 2))print(Gear(52, 11, 26, 1.5).gear_inches()) #32.22222222222222
print(Gear(52, 11, 24, 1.25).gear_inches()) #29.444444444444446
```

但是我们还是想知道比率，代码导致 bug。

```
print(Gear(52, 11).ratio()) #TypeError: __init__() missing 2 required positional arguments: 'rim' and 'tire'
```

基于这些，这是组织代码的最佳方式吗？如果你知道这个应用程序不会永远改变，这就足够了。然而，大多数应用程序都会改变，你也不知道会发生什么变化。

# 编写拥抱变化的代码

这里有一些众所周知的技术，您可以使用它们来创建拥抱变化的代码。

## 依靠行为，而不是数据

**隐藏实例变量**

```
def cog(self):
  return self.cog
```

实现这个方法将 cog 从数据(到处引用)变为行为(定义一次)。如果 cog 被引用了 10 次，并且突然改变了它的规格，你不需要改变代码 10 个位置。

**隐藏数据结构**

```
class ObscuringReference:
  def __init__(self, data):
    self.data = data def diameters(self):
    return [cell[0] + (cell[1] * 2) for cell in self.data]data = [
  [622, 20],
  [622, 23],
  [559, 30],
  [559, 40]
]print(ObscuringReference(data).diameters()) # [662, 668, 619, 639]
```

这个例子还不错，但是要做任何有用的事情，每个数据*的发送者都必须完全知道哪段数据在数组的哪个索引上。*

在这种情况下，发送者必须知道轮辋在[0]，轮胎在[1]。如果数据结构改变了，那么这个代码也必须改变。rims 在[0]的知识不应该在你的应用中重复，因为它不是干的(不要重复自己)。

```
from collections import namedtupleclass RevealingReference:
  Wheel = namedtuple("Wheel", ("rim", "tire")) def __init__(self, data):
    self.wheels = self.wheelify(data) def diameters(self):
    return [wheel.rim + (wheel.tire * 2) for wheel in self.wheels] def wheelify(self, data):
    return [RevealingReference.Wheel(cell[0], cell[1]) for cell in data]data = [
  [622, 20],
  [622, 23],
  [559, 30],
  [559, 40]
]print(RevealingReference(data).diameters()) # [662, 668, 619, 639]
```

如上实现，diameters 方法现在不知道数组的内部结构。所有关于结构的知识都被隔离在 wheelify 方法中，所以如果结构改变，我们不需要改变代码，例如在[0]和[1]之间插入新数据。

## 处处实施单一责任

**从方法中提取额外的责任**

这个方法，RevealingReference 类的 diameters 方法，有两个职责:

*   在轮子上迭代
*   计算每个车轮的直径

```
def diameters(self):
  return [wheel.rim + (wheel.tire * 2) for wheel in self.wheels]
```

我们可以通过将代码分成两个方法来简化代码，每个方法有一个单独的职责。

```
#Iterates over the wheels
def diameters(self):
  return [diameters(wheel) for wheel in self.wheels]#Calculates the diameter of each wheel
def diameter(self, wheel):
  return wheel.rim + (wheel.tire * 2)
```

此外，我们回忆齿轮类的 gear_inches 方法。这也有两个责任:

*   在轮子上迭代
*   计算每个车轮的直径

```
def gear_inches(self):
  return ratio * (self.rim + (self.tire * 2))
```

我们可以这样简化:

```
def gear_inches(self):
  return ratio * diameterdef diameter(self):
  return self.rim + (self.tire * 2)
```

为什么我们需要承担单一责任？这有以下好处:

*   暴露以前隐藏的品质
*   避免评论的需要
*   鼓励重复使用
*   很容易转到另一个班级

**将额外的责任隔离在班级中**

一旦你简化了每个只有一个责任的方法，现在你就看到了类责任。

我们在齿轮类的齿轮英寸方法中计算直径，但直径更可能是轮的行为。因此，我们可以创建包含直径方法的 Wheel 类。

```
class Wheel:
  def __init__(self, rim, tire):
    self.rim = rim
    self.tire = tire def diameter(self):
    return self.rim + (self.tire * 2)print(Wheel(26, 1.5).diameter())
```

然后，现在我们可以通过轮类计算齿轮类的直径。

# **最后**

```
class Gear:
  def __init__(self, chainring, cog, wheel=None):
    self.chainring = chainring
    self.cog = cog
    self.wheel = wheel def ratio(self):
    return self.chainring / float(self.cog) def gear_inches(self):
    return self.ratio() * self.wheel.diameter()class Wheel:
  def __init__(self, rim, tire):
    self.rim = rim
    self.tire = tire def diameter(self):
    return self.rim + (self.tire * 2)wheel = Wheel(26, 1.5)
print(Gear(52, 11, wheel).gear_inches())
print(Gear(52, 11).ratio())
```

感谢您的阅读！！