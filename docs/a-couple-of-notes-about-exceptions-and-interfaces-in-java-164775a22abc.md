# 关于 Java 中异常和接口的几点说明

> 原文：<https://levelup.gitconnected.com/a-couple-of-notes-about-exceptions-and-interfaces-in-java-164775a22abc>

![](img/d084df848319467752df4901865c000e.png)

照片由 [Adi Ulici](https://unsplash.com/@adiulici?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在典型的 Java 接口中，您指定了类必须实现的函数和过程，除非它们是抽象类。您指定调用什么功能和过程；参数的类型，如果有的话；并且指定函数的返回类型。

你能在接口中指定实现能抛出什么异常吗？是的，你可以。事实上，大多数接口本质上禁止实现抛出检查异常。

例如，假设您编写了一个实现了`Comparable<Fraction>`的`Fraction`类。仅仅为了满足您的好奇心，您希望`compareTo()`函数抛出`Exception`。

```
 **@Override**                     **// ERROR: WON'T COMPILE**
    public **int compareTo(Fraction other) throws Exception** {
        throw new Exception("Sorry");
    }
```

这不会编译，因为`Comparable<T>`接口没有指定`compareTo()`可以抛出的任何检查过的异常。Javadoc 确实指定了`compareTo()`可以抛出`ClassCastException`或`NullPointerException`，这两个都是运行时异常。

在一些合理的情况下，您确实需要指定接口中某个单元的实现需要抛出一个检查异常。

例如，一个涉及文件的接口可能需要一些单元，当一个特定操作所需的文件找不到时，这些单元抛出`FileNotFoundException`。

在`RuntimeException`中包装一个检查过的异常总是一种选择，但通常不是最好的选择。

如果您确定编写您的接口实现的程序员需要能够声明要抛出的检查异常，那么在相关的接口中编写一个 Throws 子句。这里有一个玩具例子:

```
package fileops;import java.io.File;
import java.io.FileNotFoundException;public interface ChecksumCorrelatable { long checksum(); boolean checksumCorrelate(File file) 
            throws FileNotFoundException;}
```

接口中这样的 Throws 子句最终只是一个建议。如果`ChecksumCorrelatable`是真实世界类必须实现的真实世界接口，那么它们可能都必须声明它们的`checksumCorrelate()`函数可能抛出`FileNotFoundException`。

但是，现实世界中需要打开一些文件来关联其校验和，而许多 JDK 文件访问方法会抛出检查异常，这两个因素结合在一起，就形成了这种要求。

换句话说，编译器不会强制要求每个实现接口的类抛出接口中指定的相同的检查异常。

我想你们中的一些人想在自己的电脑上继续学习，但是不太清楚如何继续这个例子。所以我要换一个不同的例子:分数和罗马数字。

也许这个例子感觉不太现实，但是应该更容易理解。在一个新的或者现有的项目中创建一个`Fraction`类和一个`RomanNumeralsNumber`类。

我假设你知道如何用分数做算术:加、减、乘、除。也许你需要一点复习，但你不应该有任何问题。

至于罗马数字，它们从来就不适合算术。我猜一个古罗马人会在所有的中间步骤中使用算盘，并且只在结果中使用罗马数字。

如果现在需要的话，您可以将操作数转换成十进制，用十进制执行计算，然后将结果转换回罗马数字。我想我们的`RomanNumeralsNumber`类将会做同样的事情，但是可能是用二进制而不是十进制。

所以`Fraction`和`RomanNumeralsNumber`都将定义算术函数，我们可能希望有其他类来表示不同种类的数字，比如`ComplexNumber`和`ModularInteger`，它们也定义了算术函数。

如果所有这些类中的算术函数都被统一命名就好了。我们可以通过接口做到这一点，这也给了我们减少冗余的机会。将此接口放在您的项目中，必要时更改包名:

```
package arithmetic;public interface Arithmeticable<T extends Arithmeticable<T>> {

    T plus(T addend);

    T negate();

    default T minus(T subtrahend) {
        return this.plus(subtrahend.negate());
    }

    T times(T multiplicand);

    T divides(T divisor) throws NotDivisibleException;

}
```

注意，这个接口只能由为自己实现它的类`T`来实现。例如，我们可以有“`class ModularInteger implements Arithmeticable<ModularInteger>`”，但不能有“`class ModularInteger implements Arithmeticable<Fraction>`”

我知道“算术”不是一个恰当的词。绝对不要试图在拼字游戏中玩它。如果你对拼写检查没有标记的一个或两个单词的术语有建议，请在评论中告诉我。

在这里，我还将可分性的概念引入到面向对象的设计中。我要你把这个检查过的异常和`Arithmeticable<T>`放在同一个包里:

```
package arithmetic;public class NotDivisibleException extends Exception {

    // TODO: Write getters
    private final Arithmeticable badDividend, badDivisor; public NotDivisibleException(String msg, 
            Arithmeticable dividend, Arithmeticable divisor) {
        super(msg);
        this.badDividend = dividend;
        this.badDivisor = divisor;
    }

}
```

确保从`Exception`子类化，而不是`RuntimeException`。

绝对清楚的是，这并不意味着除以 0。对除以 0 使用运行时异常。而且无论如何，我们的`RomanNumeralsNumber`类不应该代表 0。

考虑 XIV 除以 VII 等于 II，因为 II 乘以 VII 等于 XIV。但是试图将 XIV 除以 VIII 应该会引起`NotDivisibleException`。

如果类型`T`的一个实例不能被`T`的另一个实例整除，那么`NotDivisibleException`就意味着整除不能用`T`来表示，但它也意味着可以舍入到一个`T`可以表示的数。

在 XIV 除以 VIII 的例子中，小数位数为 1.75 的除法可以向下舍入到 I，或者更有可能向上舍入到 II。

这里有一个`RomanNumeralsNumber`的草稿:

```
package numerics;import arithmetic.Arithmeticable;
import arithmetic.NotDivisibleException;public class RomanNumeralsNumber 
        implements Arithmeticable<RomanNumeralsNumber>, 
        Comparable<RomanNumeralsNumber> {

    // TODO: Write getter or add [@Getter](http://twitter.com/Getter) annotation
    private final short value; // TODO: Override toString() // TODO: Override equals(), hashCode() // TODO: Write Arithmeticable<T> functions @Override
    public int compareTo(RomanNumeralsNumber other) {
        return Short.compare(this.value, other.value);
    }

    public RomanNumeralsNumber(int n) {
        // TODO: Add validation: no 0, no negative numbers
        // TODO: Determine maximum n and add validation
        this.value = (short) n;
    }}
```

明确构造函数参数`n`必须大于 0。而且应该很可能不到 4000。或者你可以选择一些“时代错误”的系统，将罗马数字系统扩展到`short`的范围之外。

让我们不要担心构造函数中`n`的验证。TODO 注释可以暂时保留。这也意味着我们应该编写我们的单元测试来暂时避免溢出。当您决定`RomanNumeralsNumber`构造函数应该取的最大值是多少时，编写一个测试。

这个练习中最耗时的部分是覆盖`toString()`，例如，给定`new RomanNumeralsNumber(1729)`，对其调用`toString()`会给出“MDCCXXIX”。

如果您想暂时搁置，以后再回头讨论，您可以像这样放入一个占位符:

```
 // TODO: Write proper override
    @Override
    public String toString() {
        return "PLACEHOLDER (" + this.value + ")";
    }
```

要覆盖`equals()`，只需检查`this.value == obj.value`是否是`RomanNumeralsNumber`的非空实例。而`hashCode()`扩大转换成`int`后正好可以是`this.value`。

虽然计算机做罗马数字算术肯定比人快得多(如果编程得当)，但让它像处理普通整数一样进行计算会容易得多，例如，

```
 @Override
    public RomanNumeralsNumber plus(RomanNumeralsNumber addend) {
        return new RomanNumeralsNumber(this.value + addend.value);
    }
```

`RomanNumeralsNumber`类不应该表示负数，所以这给`negate()`函数带来了一点问题。

嗯，我们可以让它扔出`UnsupportedOperationException`。这是一个未检查的异常，对于不提供“可选操作”的`java.util.List<E>`的实现也推荐使用这个异常

如果您使用的是 NetBeans(而不是 IntelliJ IDEA ),它可能填充了一个 stub 来完成这个任务，但是所提供的异常消息暗示我们的类将来可能会支持这个操作。调整异常消息，以明确该类不表示负数。

当然这意味着默认的`minus()`实现对`RomanNumeralsNumber`无效，所以我们必须覆盖它。这一点很明显，你很容易就能弄清楚。同样适用于`times()`。

用`divides()`，界面建议扔`NotDivisibleException`。`RomanNumeralsNumber`中的`divides()`会做到这一点。在编写了一些 JUnit 测试之后，我想到了这个:

```
 @Override
    public RomanNumeralsNumber divides(RomanNumeralsNumber divisor) 
            throws NotDivisibleException {
        if (this.value % divisor.value == 0) {
            return new RomanNumeralsNumber(this.value 
                    / divisor.value);
        } else {
            String excMsg = "The number " + this.toString() 
                    + " is not divisible by " + divisor.toString();
            throw new NotDivisibleException(excMsg, this, divisor);
        }
    }
```

这种整除的概念不适用于分数。任何分数都可以被任何非零分数整除。例如，14 除以 8 就是 7/4。

我们当然不希望将对`Fraction`实例的每个`divides()`调用都包装在一个 Try 块中，并为永远不会被调用的`NotDivisibleException`捕获。

我们不需要。下面是我如何在`Fraction`中得到`divides()`:

```
 public Fraction reciprocal() {
        return new Fraction(this.denominator, this.numerator);
    } **@Override
    public Fraction divides(Fraction divisor) {
        return this.times(divisor.reciprocal());
    }**
```

Javadoc 可能会比这长得多。

这编译得很好。只要我们注意避免被零除，在使用`Fraction`中的`divides()`时就不会有任何例外。

编写接口时，您应该考虑实现类的检查异常可能需要抛出什么。

大多数时候不需要这样做。但是当特定的和可预见的检查异常可能需要时，应该在接口中提供。

然后，当编写需要它的实现类时，程序员甚至不必考虑在运行时异常中包装检查过的异常，同时当检查过的异常不适用于特定的实现类时，可以自由选择退出。