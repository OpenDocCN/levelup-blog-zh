# 用荒谬的星巴克订单解释 Python 类

> 原文：<https://levelup.gitconnected.com/using-ridiculous-starbucks-orders-to-explain-python-classes-81d4c8b3d5>

![](img/b87b443fb91a0ca9ce056dcc6a2af30c.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Naufal Giffari](https://unsplash.com/@naufalcreed?utm_source=medium&utm_medium=referral) 拍摄

好吧，你可能想知道——为什么要星巴克的订单？为什么不用正常的东西来解释类呢？闹钟？汽车？银行账户？还有别的吗？

嗯，他们说写下你所知道的——而我是一个有咖啡瘾的千禧一代。那就去星巴克吧，朋友们。

在本文中，我们将使用类创建 Python 对象(*咳咳*荒谬的星巴克订单)。我们将在面向对象编程的环境中探索类——但主要集中在类上。(当然，还有咖啡。)

让我们回顾一下面向对象编程的一些基础知识。

# 好吧，什么是面向对象编程？

如果你是一个新的程序员，很可能你已经大部分时间都在使用[程序编程](https://www.programiz.com/python-programming/object-oriented-programming)。过程化编程的工作方式有点像一个菜谱——使用函数按顺序执行一系列任务。

顾名思义，面向对象编程(OOP)使用对象执行任务。一个**编程** **对象**是自包含的数据类型，有自己的特征和动作。OOP 允许编码人员轻松地创建和更新数据对象。

这显然是对 OOP 过于简化的介绍。要获得更完整的介绍，请查看 [Real Python](https://realpython.com/python3-object-oriented-programming/) 、[w3schools 网站](https://www.w3schools.com/python/python_classes.asp)，或者 Youtube 上 Tim 的这个[面向对象编程的精彩介绍](https://www.youtube.com/watch?v=JeznW_7DlB0)。

# 酷毙了。那么什么是类呢？

类是对象蓝图。它们描述了一个对象有什么特征(它的“属性”)和对象能对它做什么/已经做了什么(它的“方法”)。类允许用户根据需要创建该对象的任意多个副本(或“实例”)。它们对于创建多个相似类型的对象特别有用，例如，不同的星巴克饮料。

太棒了。现在，让我们创建一个用来制作对象的类。

# 让我们创建一个“饮料”类

既然我超级有创造力，我就把这个类叫做“饮料”。我们将使用下面的语句来创建“饮料”类:

```
class Drink:
```

接下来，我们将创建一个**初始方法**(“_ _ init _ _”)，它采用三个参数:“self”、“size”、“name”和“type”。

```
class Drink:
        def __init__(self, size, name, type):
```

把“__init__”方法想象成“main”函数。就像大多数程序都包含一个“main”函数一样，几乎每个类都有一个“__init__”方法。通过使用这个类创建一个对象，我们自动地将该对象作为参数“传递”给我们的“__init__”方法。这个“self”参数让我们创建(并在以后定制)我们的对象。

# 好吧，但是等等——我们不是在做星巴克饮料吗？

是啊！抱歉。让我们开始有趣的部分。

类有特征(或“属性”)。每当使用特定的类创建一个对象时，它会自动拥有该类中定义的属性。

酷，那么——星巴克的饮料有什么属性？

[严格出于研究目的](https://media.giphy.com/media/vg1ewVKZkCwFy/giphy.gif)，我点了星巴克，用他们的菜单看你可以选择的不同选项。这是我从这些选项中找到的一个简短的属性列表:

*   订购者的姓名
*   饮料类型
*   大小
*   风味
*   浇头
*   浓缩咖啡
*   甜味剂
*   牛奶的

## 下面是在类中创建属性的基本格式:

```
self.__[attribute] = value
```

使用 OOP 的好处之一是它允许我们隐藏属性——这意味着外部程序不能改变属性值。我们通过在 self 之间添加两个下划线来实现这一点。和属性。这有效地“隐藏”了该属性，并防止它被外部程序破坏。

现在，让我们将这些属性作为饮料类的一部分。(注意，这些属性可以有各种各样的值，从参数到整数、字符串、列表等等。)

```
class Drink:
        def __init__(self, name, size, drinkType):
               self.__type = drinkType
               self.__size = size
               self.__name = name
               self.__flavors = []
               self.__numFlavors = 0               
               self.__toppings = []
               self.__numEspresso = 0
               self.__typeSweetener = "None"
               self.__numSweetener = 0
               self.__typeDairy = "None"
```

非常好。现在，让我们继续我们的方法。

方法基本上是类的“动词”——它们定义了对象可以/已经对其执行的动作。

## 下面是创建方法的基本格式:

```
def methodName(self, parameter1, parameter2):
        statement
        statement
        etc.
```

在我们的例子中，我们希望创建以下方法:

*   **加 espresso shot** →一个参数:一个数字。如果这个数大于 0，就把它加到自我的价值上。__numEspresso 属性。
*   **添加甜味剂** →两个参数:一个数字和一个偏好(一个字符串)。如果这个数字大于 0，就把它加到自我的价值上。__numSweetener 属性。自我的价值。__typeSweetener 属性现在是首选项。
*   **加味** →两个参数:一个味和一个号。将味道附加到属性 self。_ _ 口味(一个列表)。将数字加到自身上。__numFlavors 属性。
*   **添加套色** →一个参数:套色。将顶层追加到属性 self。_ _ 浇头(列表)。
*   **添加乳品** →一个参数:偏好。将“首选项”的值指定给属性 self。__typeDairy。

```
**#Define addEspresso method** def addEspresso(self, num):
        if num > 0:
                self.__numEspresso += 1**#Define addSweetener method** def addSweetener(self, num, preference):
        if num > 0:
                self.__typeSweetener = preference
                self.__numSweetener += num**#Define addFlavor method** def addFlavor(self, flavor, num):
        self.__flavors.append(flavor)
        self.__numFlavors += num**#Define addTopping method** def addTopping(self, topping):
        self.__toppings.append(topping)**#Define addDairy method:** def addDairy(self, preference):
        self.__typeDairy = preference
```

## 最后一步:添加一个“__str__”方法

这个方法总是返回一个字符串。我们通过将对象作为参数传递给 print 语句来调用这个方法。在我们的例子中，我们将在“下订单”之前使用这种方法来检查我们的订单。下面是我们的代码将产生的结果:

```
**[Name]** ordered a **[size]** **[drink type]** with the following additions:**[number espresso shots]** Espresso shots
**[number flavor shots]** **[flavors]** Flavor shots
**[dairy type]**
```

下面是我们的 __str__ 方法:

```
def __str__(self):
        return (f"{self.__name} ordered a {self.__size 
                {self.__drinkType}
                with the following additions: \n"
                f"{self.__numEspresso} Espresso shots \n"
                f"{self.__numFlavors} {self.__flavors} flavor shots \n"
                f"{self.__typeDairy}"). 
```

现在让我们做一些饮料。

# 喝 1。凡妮莎想要一杯大杯双份浓缩香草拿铁，加燕麦牛奶和三份无糖榛子。

(三个给你凡妮莎。你去吧瓦内萨。)

我们的第一步:1)导入我们的类，2)创建我们的对象(Vanessa 的饮料)。

```
import drinkdrink1 = drink.Drink("Vanessa", "Venti", "Latte")
```

我们的第一个语句引入了“饮料”类。

我们的第二个语句向 __init__ 方法传递四个参数:名称(Vanessa)、大小(Venti)、饮料类型(Latte)和对象本身(drink1)。

现在,“drink1”正式成为一个对象——可以使用内置在饮料类中的任何方法对其进行修改。

凡妮莎想要两杯浓缩咖啡。让我们使用“addEspresso”方法将这些添加到 drink1 中。

```
drink1.addEspresso(2)
```

我们将调用 addFlavor 方法来添加三份无糖榛子风味:

```
drink1.addFlavor(“sugar-free hazelnut”, 3)
```

让我们通过调用 addDairy 方法添加我们的 oatmilk:

```
drink1.addDairy("oatmilk")
```

最后，让我们通过调用 print 方法来查看我们的订单:

```
print(drink1)**Output:** Vanessa ordered a Venti Latte with the following additions: 
2 Espresso shots 
3 ['hazelnut'] flavor shots 
oatmilk
```

# 喝 2。伊维特想要一杯特兰塔蜂蜜杏仁牛奶冰镇咖啡，加三份浓缩咖啡。—)，四份香草味，多加泡沫。

我们将这样做:

```
**#1) Create our drink object** drink2 = drink.Drink("Yvette", "Trenta", "Honey Almond Milk Cold Brew")**#2) Add 3 espresso shots** drink2.addEspresso(3)**#3) Add 4 vanilla flavor shots** drink2.addFlavor(“vanilla”, 4)**#4) Add extra foam using the "addDairy" method** drink2.addDairy("extra foam")**#5) Review the order** print(drink2)**Output:** Yvette ordered a Trenta Honey Almond Milk Cold Brew with the following additions: 
3 Espresso shots 
4 ['vanilla'] flavor shots 
extra foam
```

# 喝 3。马特奥想要一大杯冰星巴克金发香草拿铁，多加奶油，多加泡沫，三份菠萝姜汁糖浆，多加一杯浓缩咖啡。

```
**#1) Create our drink object** drink3 = drink.Drink("Matteo", "Tall", "Iced Starbucks Blonde Vanilla Latte")**#2) Add 3 espresso shots** drink3.addEspresso(1)**#3) Add 4 vanilla flavor shots** drink3.addFlavor(“Pineapple Ginger”, 3)**#4) Add extra foam using the "addDairy" method** drink3.addDairy("extra foam, extra cream")**#5) Review the order** print(drink3)**Output:**
Matteo ordered a Tall Iced Starbucks Blonde Vanilla Latte with the following additions: 
1 Espresso shots 
3 ['Pineapple Ginger'] flavor shots 
extra foam, extra cream
```

这三个对象现在可以彼此独立编辑。它们都有相同的方法和原始属性，因为它们是使用相同的类创建的。和往常一样，我强烈建议尝试一下本文中讨论的想法。学习代码的最好方法:写代码。所以开始吧，玩得开心点，别忘了给自己弄杯[咖啡](https://www.starbucks.com/)(不管它来自哪里)！