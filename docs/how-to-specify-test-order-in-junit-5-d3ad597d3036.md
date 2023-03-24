# 如何在 JUnit 5 中指定测试顺序

> 原文：<https://levelup.gitconnected.com/how-to-specify-test-order-in-junit-5-d3ad597d3036>

![](img/52c5bcba3c1dda82fa1d723197ff6ec6.png)

Nguyen Dang Hoang Nhu 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当 JUnit 运行一组测试时，测试通常以看似随机的顺序运行。例如，如果你的测试是 A、B、C、D 和 E，它们可能按照 C、A、B、E、D 的顺序运行。

对于绝大多数项目来说，这样就可以了。毕竟在现实生活中，你并不能总是预测事件会以什么顺序发生。

但是，如果您确实需要以特定的顺序运行测试，该怎么办呢？JUnit 5 为此提供了一种机制，JUnit 4 也是如此(JUnit 3 可能也是如此)。在本文中，我将向您展示 JUnit 5 是如何做到这一点的。

当然，仍然有很多人在使用 JUnit 4，包括我自己(为了我的 NetBeans 项目)。但是最新版本的 Eclipse 和 IntelliJ IDEA 都来自 JUnit 5。

我希望您能在自己的计算机上使用您最喜欢的集成开发环境(IDE)。在三个主要的 ide 中，只有 IntelliJ IDEA 提供了随需应变的单一测试运行，但是在这种情况下测试顺序并不重要。

我将用一个玩具例子来说明这一点。我们测试的类将是一个不必要的计算器。大概是这样的:

接下来，我们将编写一个测试类。没有什么太复杂的，只是对每个函数进行一次测试，只涉及小正整数的简单算术运算。

在这个玩具示例中，除了说明如何完成之外，没有什么好的理由指定测试顺序。

我们将需要`TestMethodOrder`注释，它位于类声明之前。该注释接受一个参数，即一个`MethodOrderer`实现的类。

我相信你可以自己写一个，但是`MethodOrderer`接口附带了三个嵌套类的实现，对于大多数目的来说已经足够好了。

按字母顺序，三个中的第一个是`Alphanumeric`，它使用了测试名称。例如，`testDivide()`会在`testMultiply()`之前运行。我知道我命名这些测试的方式是 JUnit 3 的延续，但是我更喜欢这种命名测试的清晰方式。

如果我们使用`Alphanumeric`，我们可能会这样开始我们的测试类:

```
package calculators;import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.TestMethodOrder;**@TestMethodOrder(MethodOrderer.Alphanumeric.class)**
class UnnecessaryCalculatorTest **{** // TODO: Write tests**}**
```

但是我希望它按照源代码的顺序进行，而不必笨拙地命名(例如，`test1Add()`、`test2Subtract()`等)。).

然后我们应该使用`OrderAnnotation`，这将要求每个测试除了`Test`注释之外还有一个`Order`注释。该注释采用单个整数参数，最好是一个小正整数。测试可能是这样的:

```
 @Test **@Order(1)**
    void **testZ()** {
        fail("Just for the sake of example");
    }
```

如果所有其他测试都有参数大于 1 的`Order`注释，`testZ()`应该在所有其他测试之前运行，然而它可能是最后一个使用`Alphanumeric`的测试。

综上所述，我们有:

这些应该按以下顺序运行:加、减、乘、除。他们都失败了，但这不是重点。

我提到过,`MethodOrderer`接口有三个嵌套类的实现。我还没有提到的是`Random`，它可能看起来和平常没什么区别。

它确实在一个重要方面有所不同。如果我们没有指定测试顺序，一组测试运行的顺序似乎是随机的。在我们的计算器例子中，可能是除、加、乘、减。

如果我们在不添加或删除测试的情况下重新运行，它们可能会再次以相同的顺序运行。如果我们添加一个测试，新的测试可能会先运行，可能会最后运行，也可能会在中间运行。但是，除了新的测试，测试顺序没有改变。

`Random`与通常的不同之处在于，顺序几乎肯定会在运行之间发生变化。更准确地说，它被称为“伪随机”，但在我看来，它足够随机。

我将上面引用的测试类改为使用`Random`而不是`OrderAnnotation`，并且移除了`Order`注释。如果您在自己的 IDE 中跟随，我希望您也这样做。

然后我让 JUnit 运行测试，它们按照加、乘、除、减的顺序运行。又运行了两次，它们都是加、乘、减、除(真正的随机性意味着有时，但不是很经常，相同的值会连续出现两次)。

另一次运行的顺序是乘、除、减、加。最后，乘、加、减、除。好了，说够了，你明白了。

现在您知道了如何在 JUnit 5 中指定测试顺序。

我用了一个玩具的例子来演示这个概念，因为我想不出一个现实的例子。如果你能想到一个现实的例子，请告诉我。

除了玩具的例子，我从来没有需要在我的任何项目中指定测试顺序。

如果您正在编写可变类，测试可能应该在新的实例上操作。或者，如果您正在编写不可变的类，实例“新鲜度”不是很相关。而且对于只有静态函数的实用程序类来说肯定是不相关的。

有一次，当我还是单元测试的新手时，我遇到了一种情况，我认为我需要控制测试顺序。但是，当我想了更多，我意识到我只是需要测试设置和拆除。

JUnit 4 提供了`BeforeClass`、`Before`、`After`和`AfterClass`注释。JUnit 5 也有类似的注释。

我把`TestMethodOrder`放在“很高兴知道”下面，即使我不知道我是否真的需要它。

考虑测试顺序是否与特定的测试类相关是绝对值得的，即使您认为通常的 JUnit 顺序是合适的。