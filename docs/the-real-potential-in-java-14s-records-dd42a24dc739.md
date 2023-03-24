# Java 14 记录的真正潜力

> 原文：<https://levelup.gitconnected.com/the-real-potential-in-java-14s-records-dd42a24dc739>

## 输入更少的代码很好，但这只是冰山一角。

![](img/4a715cf95b7217300bd294305a1979a5.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 Java 14 引入的所有特性[中，可能最令人兴奋的是](https://www.techgeeknext.com/java/java14-features)[记录](https://www.techgeeknext.com/java/java14-features#rp)。

开发人员长期以来一直抱怨在创建简单的 Java 类时需要编写和维护繁琐、不必要的代码。记录——目前是预览功能——承诺改变这一切。Java 开发人员很兴奋，这是理所当然的。但是他们是因为所有正确的理由而兴奋吗？

## 这不仅仅是保存击键

Java 以冗长、臃肿的语言而闻名。也许最突出的原因是语言迫使我们实现编译器应该能够自己发现的方法。这些方法包括:

*   每个字段的 getters 和 setters
*   初始化字段的显式构造函数
*   `equals()`、`hashCode()`、`toString()`等方法

所以当 [JEP 359:记录(预览)](https://openjdk.java.net/jeps/359)展开时，许多开发者站起来鼓掌。然而，这种掌声大多只是庆祝我们不再需要打字的按键。我们找到的关于这个主题的大多数文章采取以下形式:

*这里是我们到目前为止在创建一个类时必须做的事情:*

```
final class Point {
    public final int x;
    public final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    } public boolean equals(Point that) {
        return this.x == that.x and this.y = that.y
    } public int hashCode() {
        return 31 * x + y;
    } public String toString() {
         return "Point: x:" + x + " y:" + y;
    }}
```

*下面是我们需要对记录做的所有事情:*

```
record Point(int x, int y) { }
```

*这样我们就不需要打字了！结束了。*

这的确值得兴奋。但是当谈话到此为止时，它错过了许多更令人兴奋的事情。

在本文中，我将介绍我所看到的记录的一些主要好处。首先，我们将更多地讨论理论上的，记录促进好的软件设计的一些方法。然后，我们将讨论 Records 即将推出的一个实用、强大的功能——深度模式匹配。

# 设计的东西

当我第一次开始用 Java 编程时，我学到了正确设计类的规则:

*   我应该显式实现`equals()`
*   如果我计划覆盖`equals()`，那么我也必须覆盖`hashCode()`
*   我应该覆盖我的类的`toString()`方法
*   如果对我的类有一个自然的排序，我应该实现`Comparable`并提供一个`compareTo()`实现
*   按照 JavaBean 规范，我应该保持我的类的成员是私有的，并实现公共的 getter/setter。

但是有些事我不太喜欢。特别是，那些规则对我写的许多类来说没有意义。

## 名词对动词

渐渐地，我开始意识到有两种类别:*名词*和*动词*。

*名词*代表事物。它们储存数据。它们是我们在第一个 OO 教程中学习的那些类:*形状*、*圆形*、*正方形*和*三角形*；*停车场*、*车轮*、*发动机*、*车门*。它们保持自己的状态，并需要适当的封装。我们通常会实例化它们中的许多，我们需要跟踪它们并对它们进行比较。

*动词*则执行动作。这些是 Spring 应用程序中的*控制器*，Hibernate 层中的 *DAOs* ，以及 web 应用程序中的*web 控制器*。它们不维护任何状态，除了对依赖关系的引用(依赖关系本身通常是动词)。我们通常会创建类的单个实例，因此不需要定制`equals()`、`hashCode()`等的实现。

Java 中的具体类在历史上都是同一类型的。虽然框架可能提供了一些机制，但是直到现在还没有语言级别的特性可以将原型应用到类中。

## 建模数据

有了记录，我们可以更清楚地表达我们的意图。我们现在可以说，“这个类是一个*的东西*。它被设计用来存储数据，并将数据从一个地方转移到另一个地方。”除了减少击键次数，记录还允许我们简明地对特定的*名词*类(也称为*数据类*)包含的数据建模。

但是不要简单地相信我的话。在[官方的 JEP](https://openjdk.java.net/jeps/359) 中，作者自己声明:

> 虽然表面上很容易将记录视为主要是关于样板文件的减少，但我们选择了一个更具语义的目标:*将数据建模为数据*。

## 不变

关于我的名词/数据类，一些别的东西开始困扰我。如果`*fooA*.equals(*fooB*)` *现在是*，那么`fooA` *不应该总是*等于`fooB`吗？或者可能更重要的是(当我们考虑像 HashMaps 这样的结构时)，应该允许任何class' `hashCode()`值改变吗？

问题是，`equals()`和`hashCode()`的输出是由对象的内部状态决定的。如果该状态被允许改变，那么由对象的`equals()`和`hashCode()`返回的值可能会因此而改变。

这当然是一个*可变性*的问题。Java 作为一种语言——以及它根深蒂固的约定——鼓励可变的数据结构。然而，在许多情况下，出于许多原因，我们实际上应该支持不可变的结构。像`equals()`和`hashCode()`这样的方法的返回值的变化是一个原因。共享的、可变的对象导致的意外错误是另一个原因。

当然，如果我们愿意，我们可以通过以下方式将我们的 Java 数据类设计成不可变的:

*   宣布所有的班级成员成为最终的*成员*
*   实现一个构造函数，它在实例化过程中设置这些成员的值
*   省略任何 setters(编译器无论如何都不允许这样做)
*   理想情况下，声明类本身 *final* ，不允许用额外的可变成员扩展类

问题是它是非标准的，并且需要显式的工作。这是规则的例外。

使用记录，字段是隐式最终的。我们的构造函数是为我们创建的，用于在实例化时设置那些最终字段。换句话说，不变性成为记录的规则。

# 有趣的事情:模式匹配

记录为函数式编程语言中常见的一个极其强大的特性打开了大门，这个特性被称为*深度模式匹配*。如果看到“模式匹配”时，您的第一个想法是“正则表达式”，那么，如果不是在正确的位置上，您就在正确的范围内。

为了描述模式匹配，让我们从 Java 迄今为止提供的最接近模式匹配的东西开始。然后我们将继续讨论提供真正的深度模式匹配的 JVM 语言的例子:Scala。

## *的实例*

*虽然 Java 是静态类型的，但它也是多态的。所以我们在运行时可能并不总是知道类的确切类型。为此，Java 提供了操作符的*实例。**

```
*public void attendTo(Animal a) {
  if (a instanceof Dog) {
    walk((Dog)a);
  } else if (a instanceof Cat) {
    cleanLitterBoxOf((Cat)a);
  } else if (a instanceof Bird) {
    cleanCageOf((Bird)a);
  } else {
    returnToStore(a);
  }
}*
```

*无独有偶，操作符的*实例在 Java 14 中也得到了增强，称为* 实例的 [*模式匹配。最明显的好处是，我们不再需要执行冗长且容易出错的强制转换，这使得我们可以像这样重写上面的示例:*](https://openjdk.java.net/jeps/305)*

```
*public void attendTo(Animal a) {
  if (a instanceof Dog d) {
    walkDog(d);
  } else if (a instanceof Cat a) {
    cleanLitterBoxOf(a);
  } else if (a instanceof Bird b) {
    clearCageOf(b);
  } else {
    returnToStore(a);
  }
}*
```

*我们现在可以声明我们要检查的类型的变量；该变量将隐式地“转换”给我们。例如，在上面的第二行中， *d* 已经是类型`Dog`；我们没有必要为了调用像`public void walkDog(Dog d)`这样的方法而显式地转换它。*

*我们得到的第二个好处是，我们可以在我们的 *if* 语句中使用这个变量。假设我们不想遛狗，如果它是一只拉布拉多犬；相反，我们想让它在湖边跑来跑去(有人告诉我，因为实验室喜欢水):*

```
*public void attendTo(Animal a) {
  if (a instanceof Dog d and d.breed == Breed.Labrador) {
    letDogRunByLake(d);
  } else if (a instanceof Dog d) {
    walkDog(d);
  }
  // ...
}*
```

*很强大的东西，是吧？但权力也就到此为止了。我们可以捕获隐式转换的变量(例如 *Dog d* )以备后用，但这是我们真正能捕获的全部。该特性名义上被称为“模式匹配”，但它是一种非常有限的形式，我们可以称之为“浅层”*

## ****开关****

*现在让我们切换…到 Java 的 *switch* 语句。switch 语句还提供了模式匹配的阴影。我们可以使用 switch 语句来匹配数值变量的值:*

```
*int i = ...
String s = null;
switch (i) {
  case 0: 
    s = "none";
    break;
  case 1: 
    s = "one";
    break;
  default: 
    s = "many;
}*
```

*从 Java 7 开始，字符串也可以打开:*

```
*int i = -1;
String s = ...;
switch (s) {
  case "none":
    i = 0;
    break;
  case "one":
    i = 1;
    break;
  default:
    i = 2;
}*
```

*直到最近，*开关*还只是一个*语句*，只能控制流量。有了 Java 12，*开关*现在可以作为*表达式*；即发出一个新值:*

```
*int i = ...
String s = switch (i) {
  case 0 -> "none";
  case 1 -> "one";
  default -> "many;
}*
```

*现在，让我们想象我们可以将 switch 表达式与的最新*实例结合起来:**

```
*Animal a = ... // get a concrete instance of an Animal subclass
String s = switch (a) {
  case (Dog d && d.breed == Breed.Labrador) -> 
      "A dog that likes to swim";
  case (Dog d) -> "dog of breed " + d.breed;
  case (Cat c) -> "cat of breed " + c.breed;
  default -> "some other animal";
}*
```

*当然，我们不能在 Java 中做到这一点；该代码将无法编译。但是如果我们可以，那么我们将开始看到模式匹配的真正力量。*

## *包含案例类的案例研究*

*Scala 是一种运行在 JVM 上的混合 OO/函数式语言。Scala 提供了许多强大的特性。其中之一，*深度模式匹配*。随着记录的引入，似乎更有可能被添加到 Java 开发人员的工具箱中。*

*在我们深入细节之前，让我们看看如何使用 Scala 的模式匹配来实际实现最后一个(一厢情愿的)例子([如果您不熟悉 Scala 的语法](https://medium.com/@dt_23597/scala-syntax-for-java-developers-69734ce17cdf)，请查看本文):*

```
***val** a: Animal = ... // get a concrete instance of an Animal subclass
**val** s = a **match** {
  **case** *Dog*(_, *Labrador*()) => "a dog that likes to swim"
  **case** *Dog*(_, b) => s"a dog of breed $b"
  **case** *Cat*(_, b) => s"a cat of breed $b"
  **case** _ => "some other animal"
}*
```

*如果你不熟悉 Scala，上面例子中最令人困惑的部分可能是 case 块本身。您可能会猜测，如果第一行匹配，`*s*`将被赋予字符串值*“一只喜欢游泳的狗”…* ，但是`*Dog*(_, Labrador())`到底会匹配什么呢？*

*为此，我们需要探索 Scala 中的*用例类*。Case 类从一开始就是 Scala 的一等公民。Java 记录非常类似于 case 类；乍一看，case 类与记录非常相似:*

```
***case** **class** Dog(name: String, breed: Breed) **extends** Animal*
```

*它还提供与唱片相同的内容:*

*   *一个构造函数(以名为`apply()`的函数的形式)，它接受并设置所有的字段(这里， *name* 和 *breed* )。除此之外，这个`apply`方法允许我们在实例化一个 case 类时省略 *new* 关键字(例如`val d = Dog(“Fido”, Labrador())`而不是`val d = new Dog(“Fido”, Labrador()`*
*   *隐式不可变字段*
*   *字段的 getters*
*   *`equals()`、`hashCode()`和`toString()`实施*

*Case 类还隐含地提供了两个更重要的东西。第一个是`copy()`方法，它允许我们制作一个 case 类实例的副本，在这个过程中修改特定的字段。比如`val d2 = d.copy(name="Buddy")`。*

*第二个是*提取器*功能。就像构造函数获取*单个字段*并从中创建*对象实例*一样，提取器获取*对象实例*并返回*单个字段*作为元组。在 Scala 中，提取器采用名为`unapply()`的函数形式。*

*所以在上面的例子中，`Dog`的提取器将返回一个`(String, Breed)`的元组。如果我们手工编写的话，下面是`Dog`的构造函数和提取函数的样子:*

```
***def** apply(name: String, breed: Breed) = {
    new Dog(name, breed)
}**def** unapply(d: Dog): Option[(String, Breed)] = {
    Some(d.name, d.breed)
}*
```

*(注意`Option`和 Java 的`Optional`差不多，`Some`是`Option`的子类。在这个练习中，你可以忽略所有这些。)*

*仔细看，你可能会注意到一些东西。*提取器*(忽略`Option`包装器)的返回值看起来与传递给我们的 case 类的*构造器/apply* 方法的参数相同。*

*当我们利用 Scala 的深度模式匹配时，我们是在匹配提取器的返回值。但是我们可以像使用对象的构造函数一样编写模式。*

*让我们再看看上面的例子:*

```
***val** s = a **match** {
  **case** *Dog*(_, *Labrador*()) => "a dog that likes to swim"
  **case** *Dog*(_, b) => s"a dog of breed $b"
  **case** *Cat*(_, b) => s"a cat of breed $b"
  **case** _ => "some other animal"
}*
```

*现在来看看以下 val:*

```
***val** d1 = *Dog*("Buddy", *Labrador*()) 
  *// d1 matches the first case; it is a Labrador Dog with a name*
**val** d2 = *Dog*("Fido", *Labrador*()) 
  *// d2 also matches the first case; it too is a Labrador with a name*
**val** d3 = *Dog*("Pongo", *Dalmatian*()) 
  *// d3 matches the second case; it's a Dog, but not a Labrador*
**val** c1 = *Cat*("Morris", *Tabby*()) 
  *// c1 matches the third case; it's a Cat of some breed**
```

*我们的`Breed`和`Labrador`类会是什么样子？`Breed`可能是一个特征(类似于 Java 接口),而`Labrador`可能是一个案例类:*

```
***trait** Breed { }
**case** **class** Labrador() **extends** Breed*
```

*让我们把事情扩展一下。拉布拉多可以有多种颜色；比如*黄色*(又名*金色*)*黑色、*和*巧克力*。让我们添加一个可以传递给我们的`Labrador`案例类的`Color`案例类:*

```
***case** **class** Color(value: String)
**case** **class** Labrador(color: Color) **extends** Breed*
```

*现在我们可以像这样创建一个实例:
`**val** d = *Dog*("Fido", *Labrador*(*Color*("yellow")))`
并使用下面的模式匹配找到它:*

```
***val** s = a match {
  **case** *Dog*(n, *Labrador*(*Color*("yellow"))) => s"a Golden Lab named $n"
  **...**
}*
```

*注意，当我们这样做时，我们已经捕获并使用了`Dog`的名称(通过 *n* 变量)。*

*您已经可以看到这将变得多么强大。但是我们再举一个例子。Scala 提供了一个名为`Either[A,B]`的密封抽象类。任何一个都是有效的联合类型。被密封后，`Either`的所有子类都是在编译时定义的；其实`Either`只有两个子类:`Left[A]`和`Right[B]`。这允许 Scala 函数返回两种完全不同的类型之一。*

*因此，让我们创建一个非`Animal`案例类:*

```
***case class** Alien(planet: String, numberOfEyes: Int)*
```

*除了这个功能之外:*

```
***def** whoDugUpMyFlowers(): Either[Alien, Animal] =
  if (Random.nextBoolean()) {
    *Right*(*Dog*("Buddy", *Labrador*(*Color*("yellow"))))
  } else {
    *Left*(*Alien*("Neptune", 7))
  }*
```

*现在，我们可以对该函数的结果进行模式匹配，如下所示:*

```
***val** v = *whoDugUpMyFlowers()*
v match {
  ***case*** *Left(Alien(p, e))* => println(s"A $e-eyed alien from $p")
  ***case*** *Right(Dog(n, b))* => println(s"A $b named $n")
}*
```

*`Option[A]`类类似，提供两个子类:`Some[A]`和`None`。我们可以用同样的方式对`Option`的实例进行模式匹配:*

```
***val** maybeDog: Option[Dog] = whatIsInMyYardRightNow()
maybeDog match {
  ***case*** *Some(Dog(_, _))* => println(s"A dog is in my yard")
  ***case*** *None* => println("Nothing is in my yard")
}*
```

*当然，Java 没有`Either`或`Option`类。但是没有什么能阻止我们写自己的，特别是一旦 [JEP 360:密封类(预览版)](https://openjdk.java.net/jeps/360)在 Java 15 中出现。*

# *Records 和 Java 目前缺什么？*

*记录有望为 Java 语言带来大大改进的数据建模、不可变的数据对象和强大的特性，比如深度模式匹配。*

*但是我们还没到那一步。当然，记录 JEP 仍然是一个预览语言功能。但是记录，从设计上来说，有一些苛刻的限制。此外，记录——以及 Java 语言本身——缺少一些真正有用的重要特性。*

*这并不意味着没有希望的理由。例如， [*JEP 359 记录*](https://openjdk.java.net/jeps/359) 文档本身多次提到模式匹配作为可能的未来添加。*

*那么，为了让记录在 Java 中真正发挥其潜力，我们还希望有什么其他的语言特性呢？*

## *复制()*

*不变性很重要。它保护我们免受运行时可能发生的许多潜在错误的影响，并且它通常使我们更容易对代码进行推理。但是，不变性存在一个问题。那就是，我们不能修改我们的数据结构。*

*当然，这才是重点。但是当我们有一个数据类和名词的实例时，我们只需要修改一个字段，这就很困难了。就目前的记录而言，我们需要构建一个全新的实例，复制除了我们想要修改的字段之外的所有字段:*

```
***Dog** *dog1* = 
  new **Dog**("Fido", new Labrador("chocolate"), "2019-07-01")
// let's rename our dog
**Dog** *dog2* =
  new **Dog**("Buddy", dog1.breed, dog1.dateOfBirth)*
```

*为了使像记录这样的不可变数据类真正可行，它们应该提供一些机制来轻松地产生修改后的副本。正如我们之前简单提到的，Scala 的 case 类通过隐式生成的`copy()`方法提供了一个简单的解决方案:*

```
*dog1.copy(name = "Buddy")*
```

*当然，Java 不允许我们按名称传递方法参数，所以向记录中添加一个`copy()`方法不会那么简单。我们可能会认为这个特性——按名称传递参数——是 Java 中的一个语言特性。但不知何故，这似乎不像是我们在语言中所能期待的。但是，我们可以想象一个隐式创建的“复印机”被添加到记录中，允许我们做如下事情:*

```
*Dog dog2 = dog1.copier.name("Buddy").makeCopy()*
```

## *元组*

*正如我们在 Scala 中看到的，针对类的深度模式匹配依赖于提取器方法。反过来，提取器方法依赖于元组。我很难想到提取器可以产生的另一种数据结构。*

*元组是一个简单的概念，但是 Java 语言从来没有直接支持它们。当然，各种库提供了它们自己的实现，但是为了支持像提取器这样的语言级特性，它们本身需要在语言级实现。*

## *提取器/解构器*

*当然，除非元组用于实现提取器/解构器，否则它对我们没有帮助。 [*JEP 359*](https://openjdk.java.net/jeps/359) 文档暗示解构器模式可能会作为未来的语言特性被添加进来。*

## *展开性*

*我们在这里讨论的记录的两大优势是:*

*   **名词*或*数据类*的正确语义建模。*
*   *深度模式匹配*

*这些优点都可能受到记录不能扩展的限制，它们也不能扩展任何其他类。例如，像这样的类层次结构是不可能的:*

```
 *Animal                              Shape
__________|___________             __________|___________     
 ↓        ↓          ↓              ↓        ↓          ↓
Dog      Cat      Bird             Circle  Square   Triangle*
```

*基本原理是有道理的:记录必须是不可变的。不应该允许它扩展任何可能包含可变字段的类，也不应该允许任何其他类扩展记录并添加自己的可变字段。此外，我们希望开发人员能够快速推理出给定记录的结构。*

*幸运的是，记录能够实现接口(包括默认方法)。此外，将来将记录与[密封类](https://openjdk.java.net/jeps/360)(另一个预览特性)配对可能会使记录扩展其他记录变得安全可行。*

# *摘要*

*减少我们需要输入的样板文件的数量足以让我们对记录感到兴奋。但正如《JEP》的作者所指出的，事情远不止如此。记录将允许我们清楚地模拟数据。它们使得创建不可变的数据结构变得容易。它们为一些非常强大的特性进入 Java 语言打开了大门。*

*觉得这个故事有用？想多读点？只需[在这里订阅](https://dt-23597.medium.com/subscribe)就可以将我的最新故事直接发送到你的收件箱。*

*今天[成为媒体会员](https://dt-23597.medium.com/membership)，你也可以支持我和我的写作，并获得无限数量的故事。*