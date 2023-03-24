# 嵌套的 If-Else 语句是问题所在，而不是 If-Else 本身

> 原文：<https://levelup.gitconnected.com/deeply-nested-if-else-statements-are-the-problem-not-if-else-per-se-e4f689eb07f1>

![](img/92a4a6c7fe874660a718ed4b45a48425.png)

尼克·潘普基迪斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你是一个老练的程序员，那么你永远不要使用 If-Else 语句，否则你就是一个黑客。至少我一直是这么看的。也许这些文章确实有一定的道理，如果它们的措辞不那么极端，也许我不会读它们。

不可避免地，对这些文章的回应指出，任何值得在计算机上做的事情都涉及到决策。事实上，计算机做的任何事情都涉及决策。

例如，假设你去当地的全球银行自动取款机，把你的银行卡放进去。如果你是当地的全球银行客户，你可以继续输入密码，否则自动取款机会询问你是否同意收取 5 美元的服务费。如果你同意，你继续输入密码，否则自动柜员机释放你的卡，你就可以继续你的快乐之路。

如果您输入了正确的 PIN，您可以继续交易选择，否则您还有一两次机会输入正确的 PIN。如果你做了太多不正确的尝试，自动柜员机“捕获”你的卡。

顺便说一下，关于使用反向 PIN 向警察报警小偷的事情完全是一个神话；我从未听过银行员工建议客户不要为他们的 pin 选择回文。所以 ATM 软件要考虑的条件分支就少了一个。

假设您输入了正确的 PIN，并选择从您的储蓄账户中提取 80 美元。如果您的储蓄账户余额为 80 美元或以上(如果收取服务费，则为 85 美元)，那么自动柜员机将会支付 80 美元，否则交易将因资金不足而被拒绝。

但是，如果您选择从您的支票账户中提取 80 美元或 85 美元，并且如果余额不足，但是如果您有一个相关的用于透支转账的储蓄账户，可以弥补赤字，那么自动取款机将提取 80 美元，您的支票账户余额将被清零，并且您的储蓄账户余额将减少透支额。

在芯片级甚至像 Java 虚拟机(JVM)这样的虚拟机级，还需要做出更多、更小的决策。其中一个更小的决定可能是这样的:如果寄存器 A 等于 0，那么转到第 773 行，否则转到下一行。

实际上，在芯片级没有如果-否则。该指令实际上可能类似于“跳零”，其中如果寄存器保存 0，则发生跳转。则指令指针被改变到指定的跳转目的地。否则，芯片只是照常递增指令指针。

也许你听说过 Goto 语句不好。在更高级的编程语言中也是如此。对于虚拟机来说，它们是必不可少的。因此，举例来说，JVM 指令集包括用于`goto`和`goto_w`的操作码。

Java 也将`goto`作为保留关键字，但是没有指定含义或语义。这是因为 Java 有一些有用的抽象概念，对于人类程序员来说更容易理解，比如函数调用。

我们当然可以争论哪个 Java 流控制更好。可以肯定地说，`break`和`continue`并不比被抢占的 Goto 语句好多少。

当 Martin Odersky 创建 Scala 时，他采用了一些 Java 特性，而忽略了其他特性。Scala 中检查异常的概念？消失了，除了一个为了与 Java 互操作的注释。开关盒故障？在 Scala 的 Match-Case 中找不到。

对于 Scala 中的循环？并没有完全消失，而是被 Java 程序员在使用之前可能需要学习的东西所取代。打破继续？存在，但以这种方式阻止他们的使用。

Scala 有 If-Else，Odersky 甚至扩展了它在 Java 中的用途。我认为 If-Else 本身并不是一件坏事。

回到前面的 ATM 场景，假设我们试图将 ATM 软件实现为一个单独的`main()`过程。我们将对 If-Else 和 Switch-Case 进行一些深度缩进。

即使没有 Goto 语句，漫长的`main()`过程也会感觉像意大利面条代码。圈复杂度(衡量有多少可能的执行路径)将会达到顶点。

因此，我们利用结构化编程和面向对象编程将程序分解成可管理的单元。这些单元中的每一个都应该具有低圈复杂度。

圈复杂度低的单元更容易理解，也更容易维护和更新。

例如，一个函数有一个 If-Else 后跟一个 Return，它只有两个可能的执行路径。很好，圈复杂度很低。

一个只有一层 If-Else 嵌套的函数可能有足够高的圈复杂度，以至于 linter 会警告你。如果嵌套涉及过早返回和异常，就更有可能让你的队友甚至你感到困惑。

整个程序需要具有完成工作所需的圈复杂度。但是单个的单元应该是简单的，因为这对于理解它们的目的是必要的。有时这意味着一个 If-Else，有时有一个更好的方法，根本没有 If-Else。

在银行场景中，我们肯定应该有`CheckingAccount`和`SavingsAccount`类。这些可能应该有一个抽象的`Account`超类，拥有所有子类共有的功能。

`Account`类可能应该有一个`processWithdrawal()`函数或过程，在帐户余额上有一个 If-Else。如果余额对于取款请求对象足够，则取款通过，否则交易被拒绝，可能抛出`InsufficientBalanceException`。

对于透支保护，我们也许能够完全避免 If-Else 嵌套。我们用`CheckingAccount`覆盖`processWithdrawal()`来检查支票账户和相关储蓄账户的总余额。如果这足够了，取款就会成功，余额也会相应地更新。

所以至少，多亏了多态性，我们避免了写“`if (account instanceof CheckingAccount)`”也许我们可以想出一些聪明的方法来使用多态来避免在金额比较上的 If-Else。但是……这值得吗？

如果我们被一种心理冲动冲昏头脑，不惜一切代价避免 If-Else，我们可能会编写出一个和充满 Goto 语句的程序一样难以理解的程序。

多态性并不是避免 If-Else 语句的唯一方法。有时候，一个 If-Else 语句放在合适的位置会帮你省去一堆 If-Else 语句。

作为一个例子，我现在转向一些不那么世俗的东西，但希望仍然有用:分数，如 1/2，7/3 等。在 Java 中，我们是这样开始的:

```
package fractions;public class Fraction { final long numerator, denominator; @Override
    public String toString() {
        return numerator + "/" + denominator;
    } public Fraction(long numer, long denom) {
        this.numerator = numer;
        this.denominator = denom;
    }}
```

很简单，对吧？也许`numerator`和`denominator`字段应该是私有的并有 getters，但这是另一篇文章的讨论内容。这符合我在这里的目的。

我们希望能够比较`Fraction`实例。比如 7/8 <是 8/7 吗？是啊。`Fraction`类应该实现`Comparable<Fraction>`。但是在我们到达那一步之前，我们需要由`Object`授权的旧`equals()`正常工作。

因此，让您最喜欢的 Java 集成开发环境生成`equals()`和`hashCode()`覆盖。

你可能知道我要说什么。`equals()`覆盖将需要 If 语句。现在我不关心`hashCode()`(尽管我有[一篇关于它的长文](https://medium.com/@alonso.delarte/understanding-the-generated-hash-code-override-in-java-line-by-line-72b285f8cc74))。

Eclipse、NetBeans 和 IntelliJ IDEA 为`equals()`生成了几乎完全相同的东西，它们在`hashCode()`中有一点不同。`equals()`的 IntelliJ 默认值确实让我大吃一惊。

这就是 IntelliJ 生成的内容，除了对我的偏好和缩进调整进行了一些风格上的修改:

```
 @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if **(obj == null || this.getClass() != obj.getClass())** 
                 return false;
        Fraction other = (Fraction) obj;
        if (this.numerator != other.numerator) return false;
        return this.denominator == other.denominator;
    }
```

提前回报使得 Else 变得没有必要。加粗的条件通常作为两个单独的 If 语句来处理(例如，NetBeans 如何生成它)。我不认为这改变了圈复杂度的度量。

尽管如此，这个`equals()`覆盖有足够多的执行路径，如果不是因为它是从如此熟悉的模板中生成的，我会认为它需要被分解成更小的函数。

实际上，我们需要*添加*执行路径，测试将会显示。使用您喜欢的 Java 单元测试框架添加`FractionTest`。我将使用 JUnit 5，所以如果使用 JUnit 4，您可能需要添加 public access 修饰符。

```
 @Test
    void testNotLowestTerms() {
        Fraction someFraction = new Fraction(3, 4);
        Fraction sameFraction = new Fraction(6, 8);
        assertEquals(someFraction, sameFraction);
    }
```

这些是分数，不是音乐拍号，所以应该认为它们是相等的。然而，测试失败了。我们需要一个最大公约数(GCD)函数。叫它`gcd()`。我把它的实现留给你。

然后我们重写`equals()`:

```
 @Override
    public boolean equals(Object obj) {
        // omitting referential equality, null, type checks
        Fraction other = (Fraction) obj;
 **long numerA = this.numerator;
        long denomA = this.denominator;
        long d = gcd(numerA, denomA);
        if (d != 1) {
            numerA /= d;
            denomA /= d;
        }
        long numerB = other.numerator;
        long denomB = other.denominator;
        d = gcd(numerB, denomB);
        if (d != 1) {
            numerB /= d;
            denomB /= d;
        }
        if (numerA != numerB) return false;
        return denomA == denomB;**    }
```

这通过了测试，但是我们已经增加了圈复杂度，并且我们仍然没有覆盖我们需要覆盖的每一种情况。

```
 @Test
    void testNegativeDenominator() {
        Fraction someFraction = new Fraction(1, -3);
        Fraction sameFraction = new Fraction(-1, 3);
        assertEquals(someFraction, sameFraction);
    }
```

用浮点近似值来比较分数怎么样？

```
 @Override
    public boolean equals(Object obj) {
        // omitting ref eq, null, type checks
        Fraction other = (Fraction) obj;
        **double approxA = (double) this.numerator 
                / this.denominator;
        double approxB = (double) other.numerator 
                / other.denominator;
        return approxA == approxB;**
    }
```

这是可行的，它消除了我们添加的 If 语句，但是它打开了一扇大门，7/0 和 7/0 可能被视为不同的分数，当它们都应该被视为无效分数并且都应该被`Fraction`构造函数拒绝时。

检查它接收的参数是否有效实际上是构造函数的工作。如果`denom`为 0，构造函数应该抛出一个运行时异常，比如`ArithmeticException`(我有一篇关于在 JUnit 中测试预期异常的文章)。

您可能会问:“保留原始构造函数参数有什么好的理由吗？”我回答说，如果有的话，我想不出那会是什么。所以你建议构造函数应该确保分数是最低的，并且分母是正的。

在编写了必要的测试之后，下面是我们得出的结论:

```
 public Fraction(long numer, long denom) {
        **if (denom == 0) {
            String excMsg = "Denominator 0 is not allowed";
            throw new ArithmeticException(excMsg);
        }
        long adjustment = gcd(numer, denom);
        if (denom < 0) {
            adjustment *= -1;
        }**
        this.numerator = numer **/ adjustment**;
        this.denominator = denom **/ adjustment**;
    }
```

随着测试的通过，我们可以相信这两个分数都是最低的，所以我们可以将`equals()`重构回它的原始版本。再次运行所有测试以确保一切正常。

有一种方法可以去掉构造函数中的第二个 If 语句，通过使用适当的符号函数。

```
 public Fraction(long numer, long denom) {
        // zero denominator check goes here
        long adjustment = gcd(numer, denom) *** Long.signum(denom)**;
        this.numerator = numer / adjustment;
        this.denominator = denom / adjustment;
    }
```

为了消除分母不为零的检查，也许我们可以创建一个类，将一个`long`封装到一个状态机或其他一些有趣的东西中。这样做值得吗？在我看来，没有。你可以在评论中让我知道你的观点。