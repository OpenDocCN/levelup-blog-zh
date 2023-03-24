# 思想实验:如果 Java 的 AWT 是今天发明的会怎么样？

> 原文：<https://levelup.gitconnected.com/thought-experiment-what-if-javas-awt-was-invented-today-7644c96d7d94>

![](img/aea402e1a54eae298b02b58f567b0a77.png)

照片由 [Rica Naypa](https://unsplash.com/@ricanaypa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Java 已经存在了将近四分之一世纪。从一开始，Java 就有抽象窗口工具包(AWT ),它提供了一种简洁而简单的方法来创建跨平台的图形用户界面。

AWT 远非完美。使用 AWT 的 Java 程序从来不像是为运行它的特定操作系统编写的 C++程序。在我看来，这是在多种操作系统上运行能力的一个可接受的折衷。

即使考虑到这一点，AWT 也有几个问题，其中一些在当时非常明显，而且，正如我稍后将解释的那样，由于我们对封装和不变性的偏好，其他问题在今天对我们来说比几年前更加明显。

AWT 之后不久，Sun Microsystems 推出了 Swing 和后来的 JavaFX。即使在 Oracle 下，Java 也保留了 AWT、Swing 和 JavaFX(虽然 JavaFX 被移到了一个单独的模块中)。

长话短说，我不打算讨论整个 AWT 系统。我将集中讨论一个单独的类，来自`java.awt`包的`Point`类。

`Point`类有几个问题，考虑到四分之一世纪前做出的糟糕的设计决策被 JDK 中的其他类所依赖，也许还被我们可能使用的第三方库所依赖，所有这些问题都可能被写在石头上。

所以我们不会提议对官方考虑的`Point`等级做任何改变来改变 JDK 的规格。在任何情况下，AWT 的所有问题都被标记为“无法修复”

通过阅读 Javadoc online 我发现的`Point`的第一个问题是完全缺乏封装。一个`Point`实例的字段`x`和`y`可以被任何可以访问该实例的对象修改。

可以认为封装是不必要的，因为任何 32 位有符号整数都是`x`或`y`的有效值。表示从 2147483648 到 2147483647 的任何整数。

这是在普通消费者考虑购买 8K 显示器之前很久的事了；我认为`x`和`y`的范围还是太大了。

毕竟，`Point`的主要用途是描述屏幕上的点，比如在`PointerInfo`中，它指示用户点击鼠标的位置。但是也许`Point`不应该负责验证。

AWT 的设计者认为，因此没有必要编写 getters 或 setters。然而，由于一些奇怪的原因，`Point`有 getters，但没有 setters，即使类型是可变的。在我看来，`Point`的易变性是一个更大的问题。

如今，我们更喜欢不变性。对于那些需要快速复习的人来说，如果类型`T`是不可变的，这意味着`T`的一个实例只能通过构造一个新的实例来改变。

必要时，类型`T`的变量仍可更改。只是我们没有编写一个改变`T`实例的字段的过程，而是编写一个函数，用期望的字段给出一个新的`T`实例。

使用`Point`，可能会发生这样的情况:我们编写一个单元，向不是我们自己编写的东西发送一个`Point`实例。如果某些事情以我们没有预料到的方式改变了`Point`实例，并且我们的单元需要在调用后对`Point`实例做其他事情，那么，这可能会给我们带来问题，并且花费相当多的时间使用调试器。

也许正是因为这个原因，`Point`提供了一个复制构造器，它创建了一个新的`Point`实例，与现有的`Point`实例具有相同的`x`和`y`。这样，如果我们在发送之后仍然需要原始的`Point`,我们将确保在可能改变它的调用之前复制它。

但是如果`Point`是不可变的，我们就不需要做所有这些。我们的程序可以将`Point`实例发送到第三方库，让它做它需要做的任何事情，当它完成时，我们仍然有我们原来的`Point`实例。

还要注意`Point`不是`Object`的直接子类。`Point`的直接超类是`Point2D`，来自“子包”`java.awt.geom`。

而`Point2D`包含两个嵌套的静态类，`Point2D.Double`和`Point2D.Float`。这到底有什么意义？这听起来像是过度工程化了。

如果我们今天必须用我们今天所知道的来写 AWT，那么 AWT 会是什么样子呢？`Point`会是什么样子？

对于这个思考练习，让我们想象一下，我们可以按照我们认为最好的方式重写`Point`，并且我们有全权重构我们改进后的`Point`中断概念中的任何内容。

在您最喜欢的集成开发环境(IDE)的一个项目中，制作一个类似于`awtredux`的包。实际上，没有什么可以阻止你在你的项目中制作一个`java.awt`包，但是它可能会产生比你在一个思想实验中想要处理的更多的问题。

在`awtredux`包中，让你的 IDE 创建`Point`类，填充封装和不变性的最低要求。

```
package awtredux;public class Point {

    private final int coordX, coordY;

    // TODO: Write getters getX() and getY()

    public Point() {
        this(0, 0);
    }

    public Point(int x, int y) {
        this.coordX = x;
        this.coordY = y;
    }

}
```

为了强调封装，我决定私有字段应该有不太明显的标识符。额外的好处是，这使得构造函数更加清晰。

为此使用测试驱动开发(TDD)也是一个好主意，这有助于防止过度工程化。

Setters 不适用于不可变类型。我们应该测试吸气剂吗？TDD 教条主义者会说绝对不会，永远不会。鉴于这里的字段是原语，我倾向于同意。但是如果你想测试它们，那就去吧，不要在意你团队之外的任何教条主义者会怎么说。

顺便说一下，`java.awt.Point`中的 getters 分别将`x`和`y`作为`double`返回，尽管这两个字段都是类型`int`。如果有一个好的理由，我还没有想出来。

接下来，我们需要覆盖`equals()`和`hashCode()`。您可以让您的 IDE 自动为您生成这些内容。但是那样的话，我们就错过了学习关于`java.awt.Point`的一些有趣的事情。因此，请在`awtredux.Point`中放入以下存根:

```
 // STUB TO FAIL THE FIRST TEST
    @Override
    public boolean equals(Object obj) {
        return false;
    }

    // STUB TO FAIL THE FIRST TEST
    @Override
    public int hashCode() {
        return 0;
    }
```

我认为，下一步应该是参照平等测试。一个`Point`实例应该等于它自己。

让你的 IDE 在测试包下的`awtredux`中创建`PointTest`。NetBeans 可能会创建一些建议的测试。只要这些建议的测试都不会导致关于取消引用空指针的警告，您就可以暂时不去管它们。

编写引用相等测试，看它是否失败，不管`obj`是什么，通过让`equals()`返回 true 来使它通过。

在继续进行`equals()`的其他测试之前，我将编写`toString()`的测试。不需要什么花哨的东西。它可以是类似于“( *x* ， *y* )”的东西，其中 *x* 和 *y* 代表任意数字`coordX`和`coordY`。

如果您希望我们的`Point.toString()`更像 AWT 中的那个，请相应地编辑下面的测试。

```
 /**
     * Test of toString(). Output may have spaces, they will be 
     * ignored for this test. 
     */
    @Test
    public void testToString() {
        System.out.println("toString");
        int x = 53;
        int y = 229;
        Point somePoint = new Point(x, y);
        String expected = "(" + x + "," + y + ")";
        String actual = somePoint.toString().replace(" ", "");
        assertEquals(expected, actual);
    }
```

一旦`toString()`按照您想要的方式工作，编写一个测试，说明`Point`实例不应该被认为等于 null。一定要用 JUnit 的`assertNotEquals()`，不要用`assertNotNull()`。

您也可以使用调用`equals()`的普通 Java 断言，但是您的 IDE 可能会给你一个警告，这不值得得到一个警告，更不用说取消一个警告了。像“`assert somePoint != null`”这样简单的断言不会给我们想要的第一次失败。

到目前为止，以下内容将足以通过`equals()`的测试，同时为我们接下来的几次测试保留第一次失败:

```
 @Override
    public boolean equals(Object obj) {
        return obj != null;
    }
```

下一个测试是确保一个`Point`实例不被认为等同于某个其他类的对象。我推荐用一些完全不相关的类，比如`SQLException`。我把写测试的任务交给你了。确保我们的`Point.equals()`被调用，而不是另一个类的`equals()`被调用。

测试当然会失败，因为`Point.equals()`只是检查`obj`不为空。我们需要更改它来检查`obj`的类型。

```
 @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }
        return obj.getClass().equals(this.getClass());
    }
```

这给我们带来了关于`java.awt.Point`的一个有趣的点:它的`equals()`没有使用`getClass()`，据我所知，从 JDK 1.0 开始就已经有了。而是用`instanceof`。

如果`obj`不是`java.awt.Point`的实例，`Point2D.equals()`在`obj`上被调用。如果`obj`也不是`Point2D`的实例，则调用`Object.equals()`。它只是检查引用的相等性。

我认为如果`obj`不是`Point2D`的实例，那么`Point2D.equals()`简单地返回 false 会更有意义。

回到我们的`awtredux.Point`，当`obj`是具有相同 *x* 但不同 *y* 的`Point`实例时，我们编写测试。你可以从这里接手。

关于`java.awt.Point`的另一件奇怪的事情是它覆盖了`equals()`而不是`hashCode()`。`Point2D`中的`hashCode()`使用`Double.doubleToLongBits()`。但是因为嵌套类`Point2D.Double`，所以需要指定`java.lang.Double`。这有点荒谬。

即便如此，我还是会在`Point2D.hashCode()`的`Point.hashCode()`中借鉴一两个想法。比如将 *x* 的位乘以 31，与 *y* 的位异或。

为了确保我们的`Point.hashCode()`为不同的`Point`实例生成不同的哈希代码，我将编写一个测试，生成大约一百个随机的`Point`实例，将它们放在一个集合中，并将它们的哈希代码放在一个单独的集合中。这两个集合的大小应该相同。

现在，`java.awt.Point`里的什么东西也应该在`awtredux.Point`里？由于后者的不变性，`setLocation()`程序是不必要的。除了它的`@Transient`注释外，`getLocation()`似乎是复制构造函数的一个不必要的替代。

作为一个不可变类中的函数，`move()`沦为了无意义。不过，`translate()`可能会有用。下面是第一次测试失败的原因:

```
 // STUB TO FAIL THE FIRST TEST
    public Point translate(int dx, int dy) {
        return new Point();
    }
```

第一个测试是:

```
 @Test
    public void testTranslate() {
        int x = 522;
        int y = -4309;
        Point originalPoint = new Point(x, y);
        int dx = -89;
        int dy = 9417;
        Point expected = new Point(x + dx, y + dy);
        Point actual = originalPoint.translate(dx, dy);
        assertEquals(expected, actual);
    }
```

你应该不难找到通过这次考试的方法。当然，假设你已经通过了`equals()`的所有测试。

我没有查看 AWT 中每个使用`Point`的类。我怀疑试图将我们对`Point`的更改集成到 AWT 中需要在 IDE 的指导下进行大量的重构。

在我发表这篇文章之前，除了`Point`本身，我只看过`PointerInfo`。在 Java 1.5 中引入的`PointerInfo`看起来不需要任何`Point`突变能力。所以它不会受到我们的改变的影响。

我发表这篇文章后，看了一下`Component`。在那里，我发现了相当多的`Point`的用法，但是只有两次使用了通过直接字段访问变异的`Point`实例，没有一次使用了`move()`或`translate()`。

在这两种情况下，`Point`实例都是通过函数调用获得的，而不是作为调用者的参数。

然而，我发现了一个使用`setLocation()`的非常奇怪的例子，它接收一个`Point`实例作为参数。如果不为空，就对其进行变异并返回。显然这是为了避免堆分配，这是在`Component` Javadoc 中经常提到的问题。

我重申，我们不会提议对 JDK 做任何改变。封装和不变性不会解决 AWT 的所有问题。但我确实认为这会有很大帮助。