# 在 C#中，类构造函数会告诉你一些关于代码设计的令人讨厌的事情

> 原文：<https://levelup.gitconnected.com/nasty-things-a-class-constructor-can-tell-you-about-code-design-in-c-87e8f76d9013>

## 知道如何快速发现代码库中的设计味道。

![](img/dfdc940f84e3ba057410fc9e17049796.png)

照片由[内特·格兰特](https://unsplash.com/@nateggrant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

即使在具有许多实现的特性和业务规则的复杂代码库中，也有精确的位置可以帮助您快速识别设计问题。一个这样的地方是构造函数，或者更确切地说是它的依赖项，对它的分析可以引导您创建技术债单来重新设计或重构您的应用程序的部分。

关于代码设计，构造函数依赖分析至少能告诉你以下 4 点:

# 你遗漏了一些抽象概念

对构造函数依赖关系的分析可以帮助识别设计中的*缺失抽象*问题。

```
public class EmailService
{
    private IPriceCalculator _priceCalculator;
    private IDiscountCalculator _discountCalculator;

    public EmailService(
         IPriceCalculator priceCalculator,
         IDiscountCalculator discountCalculator)
    {
        _priceCalculator = priceCalculator;
        _discountCalculator = discountCalculator;
    }

    public void Send()
    {
        double price = _priceCalculator.Calculate();
        double finalPrice = _discountCalculator.Apply(price);

        SendEmail($"You should pay {finalPrice}$");
    }
}
```

在通过电子邮件发送给用户之前，`EmailService`类注入几个依赖项来计算价格。

这里的主要问题是，价格是通过按要求的顺序调用几个方法来获得最终值的。如果其他应用程序服务也需要计算相同的最终价格，以便在数据库中存储一个值或生成一个 PDF 报告，该怎么办？他们还必须注入`IPriceCalculator`和`IDiscountCalculator`依赖项。

为什么这是一个问题？

*   每个需要计算最终价格的应用程序服务都需要复制并粘贴第 16 行和第 17 行。如果计算最终价格的逻辑改变了呢？开发人员将不得不在几个地方改变逻辑。
*   当不同的服务以自己的方式实现相同的逻辑时，在最终价格的计算中出现错误和不一致的风险很高。

这个问题的产生是因为缺乏抽象性。在上面的例子中，代码缺少像`IFinalPriceCalculator`这样的抽象，它必须处理`IDiscountCalculator`和`IPriceCalculator`的依赖关系。

```
public class FinalPriceCalculator
{
    private IPriceCalculator _priceCalculator;
    private IDiscountCalculator _discountCalculator;

    public FinalPriceCalculator(
         IPriceCalculator priceCalculator,
         IDiscountCalculator discountCalculator)
    {
        _priceCalculator = priceCalculator;
        _discountCalculator = discountCalculator;
    }

    public decimal GetFinalPrice()
    {
        double price = _priceCalculator.Calculate();
        double finalPrice = _discountCalculator.Apply(price);
        return finalPrice;
    }
}
```

每个应用服务现在只需要注入`IFinalPriceCalculator`服务，并通过调用单个`GetFinalPrice`方法简单地获得最终价格。

# 你违反了单一责任原则

分析构造函数的依赖列表可以帮助您发现对单一责任原则(SRP)的违反。

在一个类有大量(10，20，…)依赖项的情况下，SRP 违规是立即可见的。许多依赖关系通常意味着一个类只做许多事情。按照单一责任原则，将如此大的班级分成更小的单一目的班级是一种选择。

然而，在依赖关系的数量很少的情况下，SRP 的违反乍看起来可能并不明显。但是对依赖性的性质进行稍微更详细的分析可以帮助识别 SRP 的违反。让我们看一个例子:

```
public class TaxCalculator
{
    private IPdfGenerator _pdfGenerator;

    public TaxCalculator(IPdfGenerator pdfGenerator)
    {
        _pdfGenerator = pdfGenerator;
    }

    public void CalculateAndSave()
    {
        double tax = CalculatedTax();
        _pdfGenerator.Save(tax);
    }
}
```

顾名思义，`TaxCalculator`类负责计算一些税收，它只有一个依赖项`IPdfGenerator`。它说除了计算税收，`TaxCalculator`类还知道生成 PDF 文件的逻辑。即使`TaxCalculator`类本身没有实现 PDF 生成逻辑，SRP 仍然被违反。

混合使用`TaxCalculator`和`IPdfGeneratory`会使在完全不需要 PDF 生成逻辑的不同场景/功能/环境中重用税务计算逻辑变得非常困难。因此，对于任何复杂的应用程序来说，将`IPdfGenerator`依赖从`TaxCalculator`转移到另一个抽象层次是必要的。

# 你有隐含的依赖

在类构造函数中声明的依赖被称为*显式依赖*。他们有优势:

*   如果不提供构造函数所需的所有依赖项，并且拥有一个部分初始化的对象，就不可能实例化一个类。
*   从维护的角度来看，显式依赖很方便:开发人员可以简单地查看类的构造函数，以了解正在使用哪些依赖。
*   显式依赖关系(当它们是具有虚方法的接口或类时)可以很容易地被模仿用于单元测试目的。

> 当一个构造函数很少或者没有依赖项时，这可能意味着该类使用隐式依赖项。

```
public class OrderService
{
    private IOrderRepository _orderRepository;

    public OrderService(IOrderRepository orderRepository)//Explicit dependency
        _orderRepository = orderRepository;
    }

    public void SaveOrder(Order order)
    {
        var validator = new OrderValidator(); //Implicit dependency
        ...
    }
}
```

与显式依赖不同，隐式依赖通常用于类中的任何地方，任何代码行。

隐式依赖通常是对静态方法或非静态类的调用，它们是用`new`关键字创建的。在这两种情况下，它在类之间创建了一个紧密的耦合，使得代码重用、维护和单元测试更加困难，或者在没有重构的情况下是不可能的。

因此，如果在您的类中发现了隐式依赖，请考虑将它们重构为显式依赖。

# 您可以使用其他依赖注入技术

构造函数注入并不是将依赖项注入到类中的唯一技术。

依赖项也可以通过类的属性或方法注入。

```
public class OrderService
{
    //property
    public IEmailService EmailService { get; set; }

    //constructor    
    public OrderService() 
    {
    }

    //method
    public void GenerateOrder(PdfGenerator pdfGenerator)
    {
        ...
    }
}

//---
var orderService = new OrderService();
orderService.EmailService = new EmailService(); //property injection
orderService.GenerateOrder(new PdfGenerator()); //method injection
```

属性或方法注入模式实际上是我们前面讨论过的隐式依赖。因此，它们应该只在真正必要的时候使用，例如，在类`OrderService`被实例化时依赖项不存在的情况下。

不必要地使用属性或方法注入，忽略构造函数依赖技术会导致不必要的设计复杂性。因此，尽可能考虑使用构造函数注入技术。

# 结论

定期查看各种类构造函数的依赖项列表可以帮助您发现与可接受的代码设计的偏差。您可以在调查一些错误、实现特性或审查代码时快速完成这项工作。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](https://betterprogramming.pub/5-ways-to-implement-factory-design-pattern-in-c-382c0992a3ff) [## 在 C#中实现工厂设计模式的 5 种方法

### 静态、异步、参数化、内部和抽象工厂

better 编程. pub](https://betterprogramming.pub/5-ways-to-implement-factory-design-pattern-in-c-382c0992a3ff) 

👉订阅我的 [**软件开发日报**](https://t.me/sd_daily) 电报频道，从我这里获取实用的编程技巧，供您日常工作使用。

还有，考虑成为[中员](https://esashamathews.medium.com/membership)。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)