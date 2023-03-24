# 利用惰性求值防止依赖注入中的参数钻取

> 原文：<https://levelup.gitconnected.com/using-lazy-evaluation-to-prevent-parameter-drilling-in-dependency-injection-dde1763ffa9a>

![](img/5bb133780d2c7df24a97c70ff19f3074.png)

在我的[上一篇文章](https://medium.com/@justintoja/why-i-dislike-mocking-in-software-development-a3076b44393c)中，我谈到了为什么我不喜欢软件开发中的嘲讽:

1.  当涉及到预测产品中模仿所代表的对象的行为时，模仿通常会产生较低的保真度。
2.  模拟鼓励开发其他模拟，这会占用开发时间。
3.  mock 伤害了接口代码，因为它们倾向于干净地使用 mock(根据其机制)，而不是它们所表示的实际对象(其本身通常具有不同的机制)。
4.  模仿回避了大多数测试的核心问题——对象的行为而不是它们的存在

在一个关于模仿的解决方案中，我提到了依赖注入是一种最佳的方法，当从感兴趣的对象实例化为测试对象时，这种方法可以解耦产生副作用的对象。虽然我认为注入确实解决了解耦的问题，但是当您有很高的类关系树，并且需要通过树来执行依赖关系以供低级类使用时，它可能会面临一个巨大的缺陷。这个过程在很多开发者圈子里被命名为“参数钻取”。

看一个例子。假设我们有一个应用程序，它有一个依赖项，可以从某人的信用卡上收费。让我们假设实例化信用卡本身包含一个在我们的测试环境中不存在的副作用:

```
Class A(dependency: creditCard) {

	Item: B = B(dependency) Func parentFunc1(): SomeEntityToBuy {
		Return Item.parentFunc2()
	}}Class B(dependency: creditCard) {
	Item: C = C(dependency) 
          Func parentFunc2(): SomeEntityToBuy {
		Return Item.parentFunc3()
	}
}… and so on, we keep passing the dependency across constructor after constructor, and the parentFuncs call deep within the stackClass E(dependency: creditCard) {
	Item: F = F(dependency) Func parentFunc5(): SomeEntityToBuy {
		Return Item.buyItem(dependency)
	}
} Class F(dependency: creditCard) {
	Func buyItem(): SomeEntityToBuy {
		//evaluate some value so that we get a val named input
		Val input = someEntity
		dependency.chargeMoney(input.cost)
		Return input
	}
}
```

正如你在上面看到的，纯依赖钻取的一个问题是，当我们的调用栈在创建一个产生低级类实例的对象方面变得很高时，所有这些实例都需要依赖，我们会得到许多样板代码。类 A 到 E 对使用 creditCard 类型的依赖不感兴趣，但是在这种情况下，我们被迫通过构造函数来“线程化”它们。如果我们不得不改变依赖关系(信用卡)本身，我们可能会被迫重构信用卡的多个实例作为参数！

虽然注入背后的思想在将对象实例化的技术知识从有问题的对象中分离出来方面是好的，但是如果它导致依赖钻取，我们可能会失去理智！它的伸缩性很差，肯定会有其他方法。

为了一个解决方案，我将对一个解决方案设置一个明确的限制:我们不会试图降低调用树的高度。让我们假设这段代码的作者正在尽最大努力不无缘无故地创建疯狂的调用树(并且尽最大努力不创建不必要的类)，但是它确实是应用程序实现所需要的。

我将不再讨论上下文 API(在上下文树中存储来自高级类的对象实例，然后允许低级类获取它以供使用)。虽然我认为它们是解决当前问题的优秀工具(当然应该考虑)，但有时它们可能并不总是可行的，这取决于您使用的框架。一些框架在依赖类型的实例化方面非常严格，而实际上一个特定的实现可能需要一个依赖的多个实例，而这些实例不能全部放在上下文中。此外，特定代码项的上下文 API 可能有点矫枉过正，因为它们本身可能会引入自己的样板代码。

在这种情况下，除了上下文 API，我个人会使用什么替代方法来解决这样的问题呢？

**如果你接受不了，就放弃吧！**

让我们明确一点——如果我们放弃传统的依赖注入方法，假设我们的调用栈很高，并且我们不打算使用上下文 API，我们没有办法将参数的表示从高级传递到低级(解决方案本身根本不是依赖注入)。但是没有人讨论我们从函数中发出了什么！

只有当我们实际实例化参数时，实例化参数的副作用才会暴露出来。然而，如果我们构建一个利用参数的计算，而不评估计算(从而避免评估参数)，我们也避免了实例化参数的副作用！我们可以选择在函数的结果中返回这样一个计算，这样一个拥有获取参数特权的对象就可以为我们运行它！

在函数式编程中引入一个被称为*关注点分离*的概念——我们将计算的描述与它的实际评估分离开来！我们在 FP 中的一个常见实践中看到了这一点，称为“懒惰评估”——我们可能会引用对一个对象的评估的描述，但对象本身永远不会得到评估，除非我们在实际和绝对需要它的时候明确地调用它(请将其视为超级拖延)。

例如:

```
Func genericLazyDog() {
	Lazy val a:Dog = Dog()
        //no canine here! 
}
```

如果我们运行一个这样的函数，在 genericLazyDog 的范围内，我们根本没有狗！我们将返回一个懒惰的 val，它将给出一条狗，但是因为 val 本身从来没有被调用过，所以我们没有得到这条狗。但是，如果我们这样称呼它:

```
Func dogBarks() {
	    Lazy val a:Dog = Dog()
            a.bark()
           //Fido, you're back!
}
```

在这种情况下，我们调用一个，并调用对象的一个方法，在这种情况下，我们的狗朋友就在这个函数的范围内！

让我们用它来决定如何编写类 F 的实现，以避免参数钻取，但也有其参数实例化的逻辑解耦。

在 F 中，我们可以明确地计算 val 输入，但是我们没有依赖本身的方法。我们能做的是建立依赖于匿名表达式的依赖关系的计算，然后在我们的结果中返回它供调用者使用。

```
Class F() { // we don’t need the credit card injected anymore in the //class, goodbye parameter drilling! Func buyItem(): (SomeResult, (cc:creditCard) => Void) {
		//evaluate some value so that we get a val named input
		Val input = someEntity
		Return (input, (cc:creditCard) => cc.chargeMoney(input.cost)) 
                 //we've built up the operation involving creditCard
                 //but didn't do the heavy-lifting of evaluating it
	}
}
```

通过这种方式，我们创建了某种保证，即我们的买家不会在没有为他们的产品付款的情况下跑掉购买(咳，咳，来自 LoZ 的链接:唤醒 DX，我正在看着你)，但我们也没有将我们的代码钻入地球的核心！这样，我们不能简单地使用 SomeResult，而不与某种形式的指示符交互，即我们需要放弃支付(通过 lambda 表达式)！

![](img/5103fb26db268e0c17a9419ed0c3f7c9.png)

当 A 调用 parentFunc1 时，A 将是在结果中评估匿名函数时“扣动扳机”的人，并且暴露于创建依赖关系的副作用。虽然类 F 可能有一个对计算的引用，当计算时，会暴露副作用，但它从不计算它，而是为其他有资格计算它的类构建它。

![](img/a99fe3276006a065f15aef719017a8d7.png)

是的，不是所有的物体都能胜任这项工作，对吗？

a 对 parentFunc1 的实现很简单！

```
Class A(dependency: creditCard) {

	Item: B = B() // no need for B to take the dependency, since it doesn’t care and we don’t need to thread down the tree! Func parentFunc1(): SomeEntityToBuy {
		Return Item.parentFunc2() { //the function ends up //evaluating to a pair of the item we want and the lambda that makes //us not thieves 
			(a,b) =>
				b(dependency) //pay some dough 
				Return a //get your delicious treat! 
		}
	}}
```

简而言之，仅仅因为你必须建立计算并不意味着你必须做实际评估它的繁重工作！

总之，如果有一次你打算在一长串函数中使用一个参数，那就在你的计算中偷懒，让其他合格的对象来做所有困难的工作🙂