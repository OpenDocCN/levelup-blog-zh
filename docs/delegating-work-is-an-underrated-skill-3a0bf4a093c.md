# 委派工作是一项被低估的技能

> 原文：<https://levelup.gitconnected.com/delegating-work-is-an-underrated-skill-3a0bf4a093c>

太多的混凝土很麻烦。

![](img/2f95621569e0804d9076ad0bb7fcf7e8.png)

亚历山大·曾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是软件设计模式系列的第四篇博客。让我们继续深入研究另一个复杂的模式。这将是一个构建，我们将在下一篇博客中介绍实际的模式😎。你可能想知道为什么一个建立本身需要另一个博客。这是因为我将尝试深入解释先决条件，以便我们能够正确理解实际的模式。

## 让我们来谈谈“新”这个关键词

“new”关键字是任何面向对象语言的基础，当我说面向对象时，我特指像 Java 和 C++这样的语言。JavaScript 也有“这”的概念，但它不是面向对象的。顺便说一句，你可以在评论里杀了我说 JavaScript 不是面向对象的。👻

在我们之前的博客中，我们强调了设计原则之一，即*对接口编程，而不是对实现编程*。

好吧，好吧，但是“新”这个关键词有什么问题。“新建”是创建对象的唯一方法，对吗？那有什么问题？

嗯，没有问题。但是如果你仔细想想，无论何时我们这样做，Object obj=new myObj()，我们确实在这里谈论具体。每当产品或管理发生变化时，开发人员都必须重新访问这些代码，并且可能需要重写或修改一些现有的部分，因此违反了“ ***代码应该对扩展开放，但对修改关闭*** ”。

让我们举一个例子来充分理解这有什么问题。我认为我们过度使用了比萨饼的例子，这是头优先模式书中的事实。另外，我正在节食，所以我们不谈论比萨饼。希望你能理解。🥺

我正在浏览我的食品配送应用程序，发现了一个出售面包卷的商店，我喜欢在我作弊的日子里订购面包卷。姑且称之为“**翻滚吧**”。我们将试图解开他们的代码逻辑。让我们为' **RollingTaste** '实现一个非常基础的类。

```
public class RollingTaste {

    Roll orderRole(string type){
        Roll roll;

        if(type.equals('chicken')){
          roll = new ChickenRoll();
        }
        else if(type.equals('egg')){
          roll = new EggRoll();
        }
       else if(type.equals('veg')){
          roll = new VegRoll();
       }

       roll.prepare();
       roll.pack();
       return roll;

    }
}
```

乍一看，这段代码似乎没有问题，这是真的，代码没有问题，但它也不是完美的。总有改进的余地。

我们可以看到，有一部分代码实现量很大，并且依赖于运行时的条件来创建不同类型的 roll 对象。现在想一想可能需要这种 roll 实例化的其他类似类。

在我们的应用程序中可以有一个类，比如 RollsMenu，我们需要获取它们的描述和价格。在那种情况下我们必须做什么？很明显，

> ***代码重复***

*代码重复不利于开发人员的健康*💀

我们将尝试应用从我们的第一个战略模式中学到的知识。我们谈到了封装那些经常变化的东西。如果我们能有一个只负责创建披萨对象的类(天哪！我想念披萨。抱歉打错了。我说的是面包卷。)😢根据类型？这似乎是一个很好的解决方案。让我们编写代码来理解这一点

```
public class RollingTaste {

    RollFactory factory;
    public RollingTaste(RollFactory factory){
        this.factory = factory;
    }

    Roll orderRoll(String type){
        Roll roll;
        roll = factory.createRoll(type);
        roll.prepare();
        roll.pack();

        return roll;
    }
}
```

现在，我们将了解什么是 RollFactory，

```
public class RollFactory {

    public Roll createRoll(String type){
        Roll roll=null;
       if(type.equals('chicken')){
          roll = new ChickenRoll();
        }
        else if(type.equals('egg')){
          roll = new EggRoll();
        }
       else if(type.equals('veg')){
          roll = new VegRoll();
       }
      return roll;
    }
}
```

所以我们 ***将创建 roll 对象的责任*** 委托给 RollFactory 类，而不是将它放在多个地方。如果我们仔细观察，我们仍然需要改变代码，如果我们想引入一个新的辊类型，但现在的变化将只在一个地方。

恭喜🤟！我们实现了简单的工厂**，它不是一个设计模式。这更像是一个编程习语。**

> 等等什么？如果这甚至不是一个设计模式，我们为什么要做这些呢？一切都很好😩

这对于理解接下来会发生什么是必要的😊。再次请通过参考更好的理解和更多的例子，请不要恨我，如果你发现任何错误或 incomplete🥺。我在努力做得更好，❤️

参考资料:

*   [https://www . code project . com/Articles/1131770/Factory-Patterns-Simple-Factory-Pattern](https://www.codeproject.com/Articles/1131770/Factory-Patterns-Simple-Factory-Pattern)
*   [https://stack overflow . com/questions/27007107/how-to-apply-simple-factory-pattern-Java](https://stackoverflow.com/questions/27007107/how-to-apply-simple-factory-pattern-java)