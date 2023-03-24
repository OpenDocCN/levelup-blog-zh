# 在 JUnit 5 中断言超时

> 原文：<https://levelup.gitconnected.com/asserting-timeouts-in-junit-5-4477054f82d3>

![](img/c43a021a4710c39c3bc718b113d75d8a.png)

照片由[坎德拉·温纳塔](https://unsplash.com/@candrawnt_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这些天来，我们希望尽快得到正确的结果。作为程序员，要求自己写出最高效的 Java 程序来快速交付正确的结果是没有错的。

JUnit 4 提供了多种方法来测试一个进程在指定的时间段(比如说两秒钟)之前完成的情况。这段时间称为超时。如果任务花费的时间超过超时时间，则测试失败。

JUnit 5 还提供了两种超时测试方法，但它们与 JUnit 4 提供的方法不同。

举个例子，我将使用斐波那契数列。也许你从丹·布朗的《达芬奇密码》中知道它们。它们很容易理解，有许多不同的方法来计算它们，包括一些效率极低的方法。这正是我们说明超时所需要的。

前十三个斐波那契数列是 0，1，1，2，3，5，8，13，21，34，55，89，144。这些对应于指数 0 到 12。前两个之后，每个数字都是前两个的和。

其实负指数也有斐波那契数。一旦我们为 0 和正索引的斐波那契数编写了一个函数，就很容易修改这个函数来给出负索引的斐波那契数。我稍后将回到这一点。

一个数学家可能会把公式写成这样: *F* (0) = 0， *F* (1) = 1，对于 *n* > 2，*F*(*n【T22)=*F*(*n*—2)+*F*(*n*—1)。这就暗示了我们应该如何编写这个函数。*

请注意，斐波那契数列很快变得非常大。具体来说，`int` (32 位)在到达第 50 个斐波那契数之前溢出，而`long` (64 位)在到达第 100 个之前溢出。

这表明我们应该在这里使用`java.math.BigInteger`。对于一个`BigInteger`实例可以表示多大的整数，在实践和理论上都有限制，但是我们不会接近其中任何一个。

由于 Java 没有操作符重载或隐式转换，使用`BigInteger`的基本算法有点笨拙，尽管它是可管理的。尽管我真希望他们把加法函数命名为`plus()`而不是`add()`。

```
 public static BigInteger fibonacci(int n) {
        switch (n) {
            case 0: return BigInteger.ZERO;
            case 1: return BigInteger.ONE;
            default:
                return fibonacci(n - 2).add(fibonacci(n - 1));
        }
    }
```

这看起来像是数学公式的一个很好的翻译，并且它对于非常小的正数 *n* 工作得相当好。我用 *n* = 12 和 *n* = 13 运行了几次测试，它很快给出了 144 和 233 的正确结果。

然而，实现实际上效率极低:它需要 465 次函数调用来计算 *F* (12) = 144，需要 753 次函数调用来计算 *F* (13) = 233。

为了计算*F*(100)= 354224848179261915075，这个实现将需要 1146295688027634168201 个函数调用(参见斯隆在线整数序列百科全书中的[条目 A1595](https://oeis.org/A001595) )。

假设 Java 虚拟机每毫秒可以执行 1000 次函数调用，完成这个计算需要 3600 万年。但是你可能会在一两分钟后厌倦等待，并且你会在引发`StackOverflowError`之前很久就停止测试。

如果我们不知道确切的答案是什么，一些过程必须被允许运行到完成，即使它们花费的时间比我们希望的要长一点。但是一个花费太长时间来给出一个我们已经知道的答案的过程应该被停止和拒绝，取而代之的是一个更有效的过程。

如果您使用的是 NetBeans 或 IntelliJ IDEA 这样的集成开发环境，您可以通过按一个按钮来停止长时间运行的测试。但是更有条理的方法是提前指定一个测试应该完成的特定时间段。

这就是暂停的原因。在 JUnit 4 中，以毫秒为单位将超时间隔指定为`@Test`注释的属性。如果已经导入了注释，就不需要再从 JUnit 导入任何东西。

值得注意的是，以这种方式定义超时的测试是在一个独立于运行测试设置和拆卸的线程的线程中运行的。对于这里的例子来说，这可能重要，也可能无关紧要，但是请记住这一点。

如果您想让测试在同一个线程中运行，您需要来自`org.junit.rules`包的`Timeout`规则，所以如果您选择走这条路，这可能是测试类的额外导入。

假设我们的函数应该能够在不到两秒的时间内计算第一百个斐波那契数。使用`@Test`注释的`timeout`属性，我们的测试看起来会像这样:

```
 @Test(timeout = 2000)
    public void testFibonacci100() {
        BigInteger expected 
            = new BigInteger("354224848179261915075");
        BigInteger actual = fibonacci(100);
        assertEquals(expected, actual);
    }
```

这比当您厌倦等待时停止测试要好，因为如果您有`@After`或`@AfterClass`过程，即使测试超时，它们也可能仍然会运行。

对于这个特殊的例子，我有一个函数调用计数器。在超时之前，它累计调用了 139851545 次。我们需要一个更好的算法，要么需要更少的递归，要么缓存以前的结果。

但是在我们了解如何通过这个测试之前，我们将把这个测试重写为一个 JUnit 5 测试。JUnit 5 改变了超时测试，使用 Java 8 lambdas 并避开注释属性，就像预期异常测试一样。

在我看来，JUnit 5 超时测试是对 JUnit 4 超时测试的改进，尽管没有预期异常测试的改进大。更加清晰，但也有更多的概念开销。

对于同线程测试，使用`assertTimeout()`。但是请注意:这并不能保证失控的过程会被停止。因此，对于这个理论上可以运行亿万年的斐波那契数列的例子，我们肯定要使用`assertTimeoutPreemptively()`来代替。

无论哪种方式，我们都不需要导入 JUnit 规则，但是我们需要导入`java.time.Duration`来指定超时时间。在这个例子中，显而易见的选择是`Duration.ofSeconds(2)`，但是我们也可以用毫秒(两千)甚至纳秒(2000000000)来指定它。

让我们以秒为单位，不要担心我们可能会错误地转换成毫秒或纳秒。这在清晰度上是一个进步，因为 JUnit 4 `timeout`属性中的数字感觉像是一个“神奇的数字”(声明一个像`TWO_SECONDS_TO_MILLISECONDS`这样的命名常量对于一个超时测试来说可能是多余的)。

超时断言的第二个参数是 lambda，它包含有望在分配的时间内运行的内容。与预期的异常断言没有太大的不同。

但是这里有一个概念性的开销:如果你不需要 lambda 的结果，lambda 就是一个`Executable`；但是如果您确实需要 lambda 的类型为`T`的结果，那么 lambda 就是一个`ThrowingSupplier<T>`(这是在`org.junit.jupiter.api.function`包中定义的接口，但是您实际上不需要导入它来使用它)。

对于我们的例子，我们需要一个`ThrowingSupplier<BigInteger>`。第一次做的时候，这可能看起来令人生畏，但实际上这很容易。

我坦率地承认，在这一点上我确实需要 IntelliJ IDEA 的帮助。在 IntelliJ 的帮助下，我得出了以下结论:

```
 @Test
    public void testFibonacci100() {
        BigInteger expected 
               = new BigInteger("354224848179261915075");
        BigInteger actual 
               = assertTimeoutPreemptively(Duration.ofSeconds(2),
                                           () -> fibonacci(100));
        assertEquals(expected, actual);
    }
```

这个 JUnit 5 测试实际上比 JUnit 4 测试更冗长，但也更清晰。显然，`expected`的赋值不应该导致超时。

但是“超时”这个词出现在对`actual`的赋值上要清楚得多。步骤 Arrange-Act-Assert(来自有用的 AAA 助记符)之间的区别非常明显。

我还不得不将`@After`注释改为`@AfterEach`。我运行了这个测试，有 124351254 个函数调用。比以前稍微好一点的性能，但是在超时之前仍然没有结果。

想想看，我可以重写测试，这样`assertEquals()`就在过程 lambda 中，而不是在函数 lambda 之后:

```
 @Test
    public void testFibonacci100Alt() {
        BigInteger expected
                = new BigInteger("354224848179261915075");
        assertTimeoutPreemptively(Duration.ofSeconds(2), **() -> {
            BigInteger actual = fibonacci(100);
            assertEquals(expected, actual);
        }**);
    }
```

我觉得这模糊了排列-动作-断言步骤之间的界限。总的结果是一样的:我们低效的 Fibonacci 函数实现在超时前调用了 132094676 次函数。

您的系统可能会变得更好或更差，但无论哪种方式都不会在任何合理的时间内给出结果。

所以现在让我们试着通过测试。这可能就行了…以下是我想到的:

```
 public static BigInteger fibonacci(int n) {
        switch (n) {
            case 0: return BigInteger.ZERO;
            case 1: return BigInteger.ONE;
            default:
                BigInteger[] fibos = new BigInteger[n + 1];
                fibos[0] = BigInteger.ZERO;
                fibos[1] = BigInteger.ONE;
                for (int i = 2; i <= n; i++) {
                    fibos[i] = fibos[i - 2].add(fibos[i - 1]);
                }
                return fibos[n];
        }
    }
```

它不是很优雅，也没有任何缓存，但也许它通过了一个超时测试。确实如此，它在不到 40 毫秒的时间内通过了两项测试。现在我们来谈谈重构。

这个实现让我感到困扰的是`BigInteger.ZERO`和`BigInteger.ONE`各出现了两次。我认为通过实现缓存，我们可以消除这种明显的冗余。

确保导入`java.util.ArrayList<E>`。我们将添加一个`BigInteger`实例的静态`ArrayList`和一个静态初始化器，该初始化器将`BigInteger.ZERO`和`BigInteger.ONE`放入`ArrayList`。

```
 private static final ArrayList<BigInteger> FIBO_CACHE 
            = new ArrayList<>(); static {
        FIBO_CACHE.add(BigInteger.ZERO);
        FIBO_CACHE.add(BigInteger.ONE);
    }
```

然后，我们修改`fibonacci()`函数，从缓存中获取答案(如果可以找到的话),否则构建缓存:

```
 public static BigInteger fibonacci(int n) {
        if (n >= FIBO_CACHE.size()) {
            for (int i = FIBO_CACHE.size(); i <= n; i++) {
                FIBO_CACHE.add(FIBO_CACHE.get(i - 2)
                        .add(FIBO_CACHE.get(i - 1)));
            }
        }
        return FIBO_CACHE.get(n);
    }
```

这个重构的版本通过了所有的测试。并且它还具有降低`fibonacci()`函数的圈复杂度的好处。

也许我们不需要对负指数斐波那契数列进行超时测试。如果 *n* 为正奇数，则*F*(-*n*)=*F*(*n*)，但如果 *n* 为正偶数，则*F*(-*n*)=*F*(*n*)。所以*F*(100)= 354224848179261915075。

由于乘以 1 是瞬时的，至少从人的角度来看是这样，因此如果我们测试的函数能够在合理的时间内给出 *F* (100)，那么它也应该能够在合理的时间内得出*F*(100)。

因此，两个带有等式断言的测试(一个带有负的奇数索引，一个带有负的偶数索引，比如 100)应该足够了。我认为对于这个特殊的例子，我们不需要另外一个超时的测试。

但是，如果您认为有必要再进行一次超时测试，那么现在您知道如何用 JUnit 5 的方式编写了。