# 编写单元测试不仅仅是为了测试

> 原文：<https://levelup.gitconnected.com/writing-unit-test-is-not-just-for-testing-e49ed5fe09cd>

## 软件工程实践

## 我在大学里没有学到的单元测试

![](img/f59a351f404422e4707d9f81c670bc3c.png)

由[在](https://unsplash.com/@hush52?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的

当我第一次了解编写单元测试的概念时，我觉得它们很无聊，不知道有什么好处。我真的不能理解这样做的好处，尤其是编写代码的开发人员也要编写测试。考试一定会通过的！

```
fun plus(valueA: Int, valueB: Int) Int {
    return valueA + valueB
}@Test
fun `test plus`() {
    val x = plus(1, 2)
    assertEqual(3, x)
}
```

后来，我了解到单元测试可以保证我们未来的代码更改不会破坏逻辑。这就是我们在大学里学到的。

经过多年的开发，现在我发现它比测试有更多的价值。让我与你分享一些(也许过于简单的)例子，并希望我得到它有助于弄清楚。

# 识别不在规格范围内的极限情况

我说的角落案例是什么意思？当我们被给定规格时，我们在编码时难道不考虑它吗？

让我们看看规格

> 编写一个生成阶乘数的函数。给定一个 X 值，`Factorial(A)`就是`Ax(A-1)x(A-2)x … 3x2x1`，例如给定 5！，该值应为 5x4x3x2x1 = 120。

很好，让我们编写完全覆盖阶乘规范的代码:

```
fun factorial(value: Int) : Int {
    var temp = value
    var result = 1
    while (temp > 0) {
        result *= temp--
    }
    return result
}
```

在没有为它编写单元测试的情况下，上面的代码看起来都不错。

让我们试着为它编写单元测试

```
@Test
fun `test factorial`() {
    assertEqual(factorial(5), 120)
    assertEqual(factorial(4), 24)
    assertEqual(factorial(3), 6)
    assertEqual(factorial(2), 2)
    assertEqual(factorial(1), 1)
    assertEqual(factorial(0), 1) // Ops, whataboout when it is -1?
}
```

然后我们发现一些范围之外的情况，例如值< 0。如果它不在规范中(也就是验收标准)，我们就需要讨论它应该如何执行。

以上是一个过于简化的例子。如果你以前练习过编写单元测试，这肯定是你经历过的事情。

# 发现一个做太多事情的函数

每个单元测试都用来测试一个特定的功能场景。然而，如果一个场景需要太多的断言，就需要对代码进行一些更新。

例如返回一对中的最小值和最大值的函数。

```
fun minMax(list: List<Int>): Pair<Int, Int> {
    list.*ifEmpty* **{** throw Throwable("list should not be empty") 
    **}** var min = list[0]
    var max = list[0]

    list.*forEach* **{** if (min > **it**) min = **it** if (max < **it**) max = **it
    }** return Pair(min, max)
}
```

当我们做一些测试时，我们注意到对于每个场景，我们需要不止一个断言。

```
@Test
fun `test minMax`() {
    val (min, max) = minMax(*listOf*(1, 2, 3, 4))
    Assert.assertEquals(min, 1)
    Assert.assertEquals(min, 4)
}
```

这可能会让人吃惊。也许我们应该对该职能承担较小的责任，并将责任移交给其他职能

```
fun minMax(list: List<Int>): Pair<Int, Int> {
    list.*ifEmpty* **{** throw Throwable("list should not be empty") 
    **}** return Pair(min(list),max(list))
}

fun min(list: List<Int>): Int {
    var min = list[0]
    list.*forEach* **{** if (min > **it**) min = **it
    }** return min
}

fun max(list: List<Int>): Int {
    var max = list[0]
    list.*forEach* **{** if (max < **it**) max = **it
    }** return max
}
```

编写单元测试过程暴露了对单一责任功能的需求。

# 通过适当的注入改进代码架构

假设我们有一个代码，如果提供的 id 存在，它将通过调用`getData`来执行网络获取

```
class NetworkRequest() {
    fun fetch(id: Int) {}
}

fun getData(id: Int?) {
    id?.*let***{** NetworkRequest().fetch(it)
    **}** }
```

这一切都很好。但是有一个问题，我们如何测试`getData`？

我们没有办法编写一个测试来验证当`id`不是`null`时`NetworkRequest`的`fetch(id)`被调用。

这是不可能的，因为`NetworkRequest`是在`getData`函数中实例化的。它暴露了一些糟糕的架构。

*   `NetworkRequest`每次提取都被反复实例化
*   如果我们想将`NetworkRequest`改为其他代码，我们必须修改`getData`代码。

要解决这个问题，我们必须将`NetworkRequest`从`getData`中分离出来，注入进去。

```
class NetworkRequest() {
    fun fetch(id: Int) {}
}

fun getData(id: Int?, networkRequest: NetworkRequest) {
    id?.*let***{** networkRequest.fetch(id)
    **}** }
```

为了测试，我们现在可以很容易地模仿`networkRequeest`并编写一个测试来验证`fetch(id)`是否被调用

```
@Test
fun `test getData`() {
    val networkRequest: NetworkRequest = *mock*()
    val id = 100

    getData(id, networkRequest)

    *verify*(networkRequest).fetch(id)
}
```

> 注意:理想情况下`NetworkRequest`应该是一个`interface`，但是为了简单起见，我把它作为一个类。

# 可能会暴露一些代码气味

这是我遇到的一个真实案例。为了简单起见，我对它做了一个微妙的改变。

下面是一个类似上面的示例代码。它在大多数情况下工作正常。

```
class NetworkRequest() {
    fun fetch(id: Int) {}
}

fun getData(id: Int?, networkRequest: NetworkRequest) {
    id?.*let***{** Thread **{** networkRequest.fetch(id)
        **}**.start()
    **}** }

@Test
fun `test getData`() {
    val networkRequest: NetworkRequest = *mock*()
    val id = 100
    getData(id, networkRequest)
    *verify*(networkRequest).fetch(id)
}
```

通过运行测试本身，我们在大多数情况下都会得到一个通过的结果。然而，它偶尔会失败。

上面的代码很简单，我们可以快速识别代码中`Thread`的使用，它在执行`fetch(id)`时启动一个不同的线程。

根据机器计算强度，在执行`verify`之前`fetch(id)`可能会也可能不会及时运行。因此，它可能会不时地失败，这导致了一个奇怪的混乱，在哪里出了问题。

但是幸运的是我们有一个测试，虽然感觉测试很脆弱，但是有味道的是代码。

上面的代码不好是因为

*   在测试中使用`Thread`并产生不确定的结果。
*   如果程序退出，没有代码处理线程活动的取消。

为了解决这个问题，在 Kotlin 的世界里，也许应该考虑使用协程或者 RxJava 线程机制。

幸运的是，我们已经编写了单元测试来帮助暴露一些代码味道。

# TL；DR；

编写单元测试不仅仅是为了确保现有的代码得到测试，以避免由于未来的变化而导致的退化，它还有助于提高代码的健壮性和质量。编写单元测试可以被认为是开发人员自己执行代码评审的另一种方式。这是值得花在未来的时间-证明代码。

感谢阅读。你可以在这里查看我的其他话题。

您可以在此订阅或关注我的 [*中的*](https://medium.com/@elye.project) *，*[*Twitter*](https://twitter.com/elye_project)*，* [*脸书*](https://www.facebook.com/elye.project/) *，以及* [*Reddit*](https://www.reddit.com/user/elyeproj/) 获取关于移动开发、中写等相关话题的小技巧和学习。~Elye~