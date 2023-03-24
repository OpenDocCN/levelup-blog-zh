# 条件语句是一种代码气味——这是您的解脱

> 原文：<https://levelup.gitconnected.com/conditional-statements-are-a-code-smell-here-is-your-relief-38e50c023708>

![](img/abc53fab2dbf3101805a5beb1a347cb8.png)

蒂莫西·戴克斯在 [Unsplash](https://unsplash.com/s/photos/condition?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

条件语句，如 if-else 和 switch，是任何编程语言的核心。我们日复一日地使用它们，但令人惊讶的是，它在面向对象编程语言(OOP)中被视为一种代码味道。如果你想知道为什么我会这样，那么这篇文章是为我们写的。

在我们深入之前，让我们理解 OOP 中两个非常重要的坚实原则的概念

1.  [单一责任原则](https://medium.com/@itIsMadhavan/single-responsibility-principle-a-beginners-note-cb1eaba1fecd)
2.  [开闭原理](https://stackify.com/solid-design-open-closed-principle/)

## 单一责任原则

Robert C. Martin 将单一责任原则描述为:

“一个类应该只有一个改变的理由。”

Martin 将责任定义为改变的*原因，并得出结论，一个类或模块应该有且只有一个改变(即重写)的原因。*

例如，考虑一个编译和打印报告的模块。设想这样一个模块可以因为两个原因而被改变。首先，报告的内容可能会改变。第二，报告的格式可以改变。这两件事因不同的原因而改变；一个实质性的，一个化妆品。

单一责任原则认为问题的这两个方面实际上是两个独立的责任，因此应该在不同的类或模块中。将两个在不同时间因不同原因而变化的事物联系在一起是一个糟糕的设计。

让一个班级专注于一个单独的问题是很重要的，因为这样可以使班级更加强大。阅读[这里的](https://medium.com/@itIsMadhavan/single-responsibility-principle-a-beginners-note-cb1eaba1fecd)获得更详细的解释。

## 开/关原则

Robert C. Martin 认为这个原则是“面向对象设计最重要的原则”。他将开/关原则描述为:

**“软件实体(类、模块、功能等。)应该对扩展开放，但对修改关闭。”**

这个原则的一般方法是伟大的。它告诉你写你的代码，这样你就可以在不改变现有代码的情况下添加新的功能。这防止了对一个类的更改也需要修改所有依赖类的情况。阅读[这里的](https://stackify.com/solid-design-open-closed-principle/)获得更详细的解释。

让我们回到最初的问题，为什么条件语句是一个代码气味，并从一个简单的例子开始。

# 问题:

让我们假设下面的函数是一个名为“动物”的类的行为之一。

```
private static String **getSoundIfElseWay(**String animal**)** **{** 
 if **(**animal**.**equalsIgnoreCase**(**“Dog”**))** 
      return “Bark”**;** 
 else **if** **(**animal**.**equalsIgnoreCase**(**“Cat”**))** 
      return “Mew”**;** 
 else **if** **(**animal**.**equalsIgnoreCase**(**“Lion”**))** 
      return “Roar”**;** 
 return “”**;** 
**}**
```

这里我们有一个条件块，它根据对象的类型执行各种操作。这显然违反了单一责任原则。SRP 提倡 **a 类**应该有**一个责任，**但是拥有“狗”、“狮子”和“猫”的行为或责任显然违反了这一点。

同样，这也违反了开/关原则。现在考虑我们需要实现一只“老虎”的声音，为了得到它，我们需要在上面的函数中再添加一个“else if”条件。当“动物”试图扩展一个行为时，这会迫使它进行修改，使其**为修改而打开，为扩展而关闭。**每次当我们想要添加/扩展任何功能时，我们都应该去更改它的源代码。

现在很明显，这里有条件的是一个*码闻*。那我们怎么解决呢？

有几个从代码中移除条件的重构方法。

1.  *用状态/策略恢复条件*
2.  *用多态性恢复条件*

# 用多态恢复条件句

[*多态性*](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) 在一个很幼稚的翻译中的意思是*名称相同，逻辑不同*。

在大多数情况下，当我们用多态替换条件时，我们处理的是一个*子类型多态*。OOP 中的这种多态性意味着通过在子类中提供同名方法来改变方法行为的能力。让我们考虑 OOP 教程中最著名的例子:

```
abstract class Shape 
{
    abstract public int getArea();
}

class Square extends Shape
{
    protected int length;

   **@Override
**   public int getArea() {
        return length*length;
    }
}

class Triangle extends Shape
{
    protected int height;
    protected int base; **@Override**
    public int getArea() {
        return this.height * this.base / 2;
    }
}Shape square = new Square(4);
square.getArea(); // 4

Shape triange= new Triange(2,4);
triange.getArea(); // 4
```

上面的代码非常清楚地展示了子类型多态性。我们有一个基本类型`Shape`和两个子类型(规格):`Square`和`Triangle`。这里的方法`getArea()`代表了*多态*的定义:“相同的名字，不同的逻辑”。

> *多态*:“同名，不同逻辑”

现在让我们再次回到“Animal”类，实现多态方式:

```
public abstract class Animal **{** 
    public abstract String **say();** 
**}**public class Dog extends Animal**{**       
   ** @Override **      
    public String **say()** **{**            
     return "Bark"**;**       
    **}**  
**}**public class Cat extends Animal**{**       
   ** @Override**       
    public String **say()** **{**            
     return "Meow"**;**       
    **}**  
**}**public class Lion extends Animal**{**       
   ** @Override **      
    public String **say()** **{**            
     return "Roar"**;**       
    **}**  
**}**
```

现在，Animal 类被简化了，所有的逻辑都移到了各自的类中。

上面的实现通过将行为/责任封装在相应的类中来理解单一责任原则。首先，所有动物的行为都被封装在一个类中，使得这个类违反了单一责任原则。现在我们被分成了独立的子类，我们把责任推给了它们，所以“狮子”行为的任何变化现在都是狮子的责任，而不是“动物”的责任。

上面的实现也遵循了开放/封闭的原则，让我们看看，

```
private static String **getSoundPolymorphicWay(**Animal animal**){** return animal**.**say**();** 
**}**
```

看看上面的方法，它依赖于抽象(动物类)，所以任何实现抽象的类都可以在这个方法上工作。同样，如果需要实现一个新的行为，比如说“老虎”,我们不需要在实现 **getSoundPolymorphicWay()** 和抽象**动物中做任何改变。**

我们可以为“Tiger”创建一个新类。这证明了“Animal”对进一步扩展是开放的，仍然不强制修改，无论是在“ **getSoundPolymorphicWay** ”这样的实现中，还是在它本身。

# 利益

*   这种技术遵循*告诉-不要问*原则:不是询问对象的状态，然后基于此执行操作，而是简单地告诉对象它需要做什么，让它自己决定如何做，这要容易得多。
*   移除重复的代码。你去掉了许多几乎相同的条件句。
*   如果你需要添加一个新的执行变体，你所需要做的就是添加一个新的子类，而不触及现有的代码(*开/闭原则*)。

感谢您阅读我的文章。如果你认为它有用，请分享，喜欢，鼓掌，评论✌☺

[](https://gitconnected.com/resume-builder) [## 软件工程师简历生成器和示例| gitconnected

### 免费打造不到 5 分钟的高质量软件工程简历。同步您的个人资料，我们会处理…

gitconnected.com](https://gitconnected.com/resume-builder)