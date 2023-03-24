# 关门时间到了

> 原文：<https://levelup.gitconnected.com/its-closure-time-7a71615d30a>

什么是闭包？我们为什么需要它们？我将在这里回答这些问题。

![](img/497ff959d04ea3937b9ac0fa7ecac220.png)

由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们之前讨论的关于 [reduce](https://jeffca.me/the-power-of-reduce-i-4caca8b44034) 和[如何 reduce](https://jeffca.me/the-power-of-reduce-ii-c1d5e3db330b) 的文章是最容易被误解的 JavaScript [数组方法](https://jeffca.me/the-power-of-reduce-iii-931a47ba1cdc)。今天我要解释闭包。在我的公司里流传着这样一个笑话:我 90%的问题都是通过使用闭包来解决的，废话不多说，
**是闭包的时候了**。

## 什么是终结？

闭包是一个在其内部作用域中引用外部作用域中的变量的函数。闭包的这个定义一开始可能有点混乱，最好用代码来解释它。

在这个例子中，我们有一个名为 outerFunction 的函数，它接收一个名为 name 的参数。这个函数还在内部设置了一个变量 myName，它被初始化为‘Jeff’(我保证我不是一个自恋者)。最后，我们返回另一个函数 innerFunction。这个返回的函数将把一些东西打印到屏幕上。看起来没什么大不了的，但是让我们详细看看发生了什么。

当我们运行 JavaScript 函数并且该函数完成时，由该函数创建的所有局部变量都消失了，噗！在本例中，参数名称将被视为一个局部变量，与变量 myName 相同。我们返回 innerFunction，outerFunction 完成它的执行，所以 outerFunction 创建的变量消失了，或者…

通过返回 innerFunction，我们间接地将 name 和 myName 值带入到那个函数中，就像 innerFunction 有一个小袋子，他在里面添加了所有来自外部作用域的变量。当 innerFunction 需要这些值中的一个时，比如 myName 或 Name，他只需要查看它的包并使用它们。

在本例中，我们可以做如下事情:

> const greet Danny = outer function(' Danny ')；
> greet Danny()；//很高兴见到你，丹尼。我的名字是杰夫

注意，返回的函数可以访问两个变量 name 和 anotherName，并使用这两个变量来创建问候语。

## 几级？

在使用了一个两层的例子之后，问题出现了，闭包在多少层内有效？让我们写一个例子:

请注意，我们在马里奥兄弟的例子中深入了 4 层，我们仍然可以从第一层访问最外层的范围。我们还将前 9 行写成了一个更简洁的版本。那么有几级呢？你想要多少就有多少，或者你需要多少就有多少。

## 使用闭包有什么好处吗？

这是我第一次看到闭包时问自己的第一个问题。最终，我开始对它们进行越来越多的实验，直到我得到以下答案，封装和代码重用。我们先来解释一下代码重用。

让我们假设我们想要创建一个库，它将与不同的端点进行对话，并且所有这些端点都需要一个 id。我们可以创建一个接受两个参数的函数，一个用于 url，另一个用于 id。这样我们就可以用两个参数调用这个函数。问题解决了。但是如果我们使用闭包来解决这个问题会容易多少呢？让我们看一个例子。

注意到 fakeAPI 函数是我们之前讨论过的，而 withFakeAPI 做完全一样的事情，两者之间唯一的区别是一个使用闭包，另一个不使用。假设我们要呼叫 4 个不同的端点。为了简单起见，我们姑且称那些端点:
/ep-1，/ep-2，/ep-3，/ep-4(真的原创哈？😝)

如果我们使用函数 fakeAPI，我们将会像这样做

> fakeAPI('/ep-1 '，23)；
> fakeAPI('/ep-1 '，24)；
> fakeAPI('/ep-2 '，345)；

这还不错，但并不理想。出现了一些问题，如果其中一个端点发生变化怎么办？是的，我们可能有一个端点常量变量，可以解决这个问题。

> const EP _ 1 = '/EP-1 '；
> 常量 EP_2 = '/ep-2
> 
> fakeAPI(EP_1，23)；
> fakeAPI(EP_1，24)；
> fakeAPI(EP_2，345)；

这是可行的，但也不理想，我们到处都在重复相同的变量。让我们尝试使用闭包来解决这个问题。使用闭包，我们有可能创建新的函数，这些函数将拥有 url 并返回一个需要 id 的新函数。例如:

> 导出常量 calle P1 = withFakeAPI('/EP-1 ')；
> 导出常量 callep 2 = withFakeAPI('/EP-2 ')；
> 导出常量 callep 3 = withFakeAPI('/EP-3 ')；
> 导出常量 calle P4 = withFakeAPI('/EP-4 ')；

既然现在我们导出了这四个函数，我们可以在代码的任何部分导入它们，并执行以下操作

> calle P1(23)；
> calle P1(24)；
> callep 2(345)；

这是一种更干净的调用端点的方式(也可能是更 JavaScript 的方式),这给了我们重用代码的能力，这要感谢闭包，如果其中一个端点改变了，我们可以很容易地更新函数。这也让我们能够在最终需要时添加更多端点。

## 默认隐私

正如我提到的，闭包给了我们重用代码的能力，但也给了我们封装。我们举个简单的例子。

这里有一个简单的用户函数(简单的工厂函数)的例子。该函数接收两个参数 name 和 age，并使用以下函数返回一个新对象:getName、getAge、setName、setAge。多亏了闭包，所有这些函数都可以访问名字和年龄。但是在这些闭包之外，没有人可以直接更改名称和年龄，只能通过 setAge 或 setName。多亏了闭包，我们在默认情况下实现了隐私，而不必像在其他语言或 JavaScript 类中那样使用任何私有关键字。

> const danny = User('Danny '，11)；
> Danny . getage()；//11
> Danny . getname()；//丹尼
> Danny . setage(12)；
> Danny . age//undefined
> Danny . getage()；// 12

## React 处理程序的 Curry 解决方案

有时在 React 或 JavaScript 中，我们通常需要为一个列表创建多个事件处理程序。如果没有闭包，这可能会变得有点烦人，而且不太干净。让我们看看

注意，在每次迭代中，我们都在创建一个 arrow 函数，用我们想要的值调用 fakeAPI 函数，在本例中是我们的 fakeURL 和 fakeID。这是一个非常简单的实现，但并不干净。让我们看另一个使用闭包的例子:

在这个例子中，我们可以看到闭包的强大功能以及如何保持代码更加易读。我们的 withFakeAPI 函数返回一个新函数。我们将该函数分配给 callFakeEndpoint 变量。callFakeEndpoint 将拥有我们的“/my-fake-endpoint”，请注意我们如何捕获该值，并且该值将在 *people.map* 中的每次迭代中保持不变。由于 callFakeEndpoint 返回另一个 onClick 事件可以正确处理的函数，所以我们可以在每次迭代中直接用当前人的 id 调用 callFakeEndpoint。与前面的例子相比，我们不需要在每次迭代中声明一个新的 arrow 函数。

这种一次获取一个值的概念称为 currying，是函数式编程中最重要的概念之一，也是 JavaScript 中最重要的概念之一。这是我在闭包中使用最多的模式之一。一次捕获一个值并返回另一个函数。

## 结论

通过使用闭包，你的代码变得更易维护，可读性更好，而且很有趣。提高它们的最好方法是通过练习。只是为了好玩(请不要在工作中这样做),试着重写接受 2 个以上参数的函数，并把它们改为使用闭包，一次一个参数(currying ),看看使用它们有什么好处或坏处。使用闭包重写一个类，特别是当这个类使用私有和公共关键字的时候。当您开始将闭包和 reduce 结合起来用于管道和/或组合函数时，一切都会变得更有趣。也许我们可以在另一篇文章中把两者结合起来(😉).请在评论中告诉我你对闭包的想法。

> **记得关注或** [**订阅**](https://jeffca.me/subscribe) **这样你就可以每周收到我的内容了。你也可以在推特上关注我:**[**https://twitter.com/jcar787**](https://twitter.com/jcar787)