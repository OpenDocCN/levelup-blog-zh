# 为软件开发人员创建出色功能的技巧

> 原文：<https://levelup.gitconnected.com/techniques-to-create-brilliant-functions-for-software-developers-44fcb4a78e3e>

## 注意你的功能。

![](img/6f522ac69a34460aca5488616431f0c5.png)

照片由 [Alexandru Acea](https://unsplash.com/@alexacea?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

函数是分组到类中的最小的自定义构建块。然后将类分组到模块中，依此类推直到工作产品。

函数是低级的构建块，应该根据最佳实践来编写，这样它们就可以很容易地组合成更高级的块。

如何写出一个很棒的函数？没有简单的答案，但我可以分享一些好的技巧:

# 1.用值对象替换输入参数验证

在方法开始时验证输入参数似乎是开发人员应该遵循的防御性编程的一个很好的例子。

```
public void ApplyDiscount(double discount)
{
    if (discount < 1 || discount > 100)
        throw new Exception(); //...
}
```

然而，这样的输入参数检查经常指出域设计的问题。使用基本类型来表示折扣域概念将导致需要在所有其他接受`double discount`参数的方法中复制验证规则。

这个问题的解决方案是引入一个`Discount`值对象:

```
public class Discount
{
    public double Value { get; private set; } **//Factory method to create an instance of Discount type** 
    public static Discount Create(double value)
    {
        if (value < 1 || value > 100)
            throw new Exception(); return new Discount() { Value = value };
    }
}public void ApplyDiscount(**Discount discount**)
{
    //No validation! The object is already valid. //...
}
```

验证折扣值的验证规则封装在一个地方。任何采用`Discount discount`参数的方法都不应该验证它，因为不可能用无效状态创建它。

# 2.跟踪方法输入参数的数量

有许多输入参数的方法很难维护。然而，一长串参数本身并不是问题。

一长串方法参数通常是一种代码味道，通常表明一个方法正在做许多事情，即违反了**单一责任原则**。在这种情况下，如果一个方法实际上做了几件事，它应该被分成几个更小的方法，每个小方法做一件事。

拥有长参数列表的另一个原因通常是**缺少域模型**。

```
public decimal Calculate(decimal amount, string currency)
{
    //...
}
```

通过分析两个输入参数`amount`和`currency`的性质，我们可以得出结论，它们表示“货币”的单个域概念。这意味着参数必须归入一个`Money`值对象。

```
public class **Money**
{
    public decimal Amount { get; set; }
    public string Currency { get; set; }
}public decimal Calculate(**Money money**)
{
    //...
}
```

此外，如果您看到应用程序中有几个不同的方法接受相同的基本参数组合，那么这些方法最有可能组合成一个单一的值对象。

# 3.创建尽可能多的纯函数

开发人员喜欢处理纯函数，因为它们更容易阅读、调试和维护。

纯函数的第一个关键特征是，对于相同的输入，它总是返回相同的输出，因此函数的行为总是确定的。

当函数的行为取决于以下因素时，函数的执行可能会产生不确定的结果:

*   外部(全局)状态。纯函数的行为应该只取决于它的输入参数。
*   日期和时间逻辑或多线程。在[我的另一篇文章](/how-to-avoid-the-headache-with-non-deterministic-bugs-in-software-e24457f05c9b)中有更多关于这些问题的内容。

纯函数的另一个重要特征是没有副作用。一个函数应该尽可能不改变状态。

```
**//Not a pure function**
public class Math
{
   public int Value { get; set; } public void **Increment**() =>
      Value = Value + 1; //Modifying external state
}**//Pure function**
public int Increment(int value) => 
   value + 1; 
```

并不是应用程序中的每个函数都是纯函数。某些功能肯定会改变外部状态，比如将数据保存到数据库中。但是，开发人员应该努力在应用程序中创建尽可能多的纯函数，以确保代码具有良好的可维护性。

# 4.从方法名中排除输入参数名

在查询某些数据的方法名称中包含输入参数的名称通常很有诱惑力。这通常指的是存储库方法。

```
public Order **GetById**(int **id**)
{
   //...
}
```

乍一看，这样的命名约定应该可以提高代码的可读性，但实际上效果恰恰相反。

在方法名中包含输入参数名是方法签名级别的一种干违规，可能会导致一些问题:

*   重命名参数时，开发人员可能会忘记相应地更改方法名。
*   当添加或删除参数时，开发人员可能会忘记更改方法名。
*   长的参数列表会导致麻烦的方法名，比如`GetByThisAndThatAndAlsoThat...`

最好的选择是简单地从方法名中排除参数名:

```
public Order **GetBy**(int **id**)
{
   //...
}
```

现在，方法签名中只剩下基本要素。

# 摘要

*   注意输入参数验证吗？考虑将该参数和有效性规则包装到值对象中。
*   通过将该方法分成更小的方法或/和将原语的组合包装成值对象来减少该方法的输入参数的数量。
*   纯函数是确定性的，不会导致副作用。在应用程序中创建尽可能多的纯函数。
*   不要在方法名中重复方法输入参数名，因为这违反了方法签名级别的 DRY 原则。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [## 5 种免费提高 C#代码性能的方法

### 慢速代码是可选的。

levelup.gitconnected.com](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da)