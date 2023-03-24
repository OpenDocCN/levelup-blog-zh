# 用 Kotlin 写流畅的代码

> 原文：<https://levelup.gitconnected.com/write-fluent-code-in-kotlin-133647f2a869>

![](img/6c875c328599af633776828f19bc2f67.png)

[迈克·刘易斯智慧媒体](https://unsplash.com/@mikeanywhere?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你有没有看过一段代码，心想“我不是在看代码，我是在看故事！”。流畅的代码读起来像散文；它讲述了作者试图达到的一个故事，读起来像简单的英语。代码只是流动；阅读几乎不费吹灰之力。就像优美的散文一样，流畅的代码需要努力去创造。当你阅读一段代码时，它只是自我阅读，作者代表你付出了努力来塑造它。

我赞同“让它工作，让它漂亮，让它快”的想法，漂亮代码的一个组成部分是它的流畅性。在编写和审查代码时，我经常发现自己频繁地使用一些“风格选择”来实现可读的代码。然而，一段代码的可读性是主观的，与其说是科学，不如说是艺术，因此您的收获可能会因您的偏好和环境中的约定而异。

*免责声明:以下建议是我的观点，可能不是普遍接受的最佳实践。*

## 尽量少用注释

注释不是代码的一部分。它们被编译器忽略，只对编码人员存在。它们也是另一个维护点，随着代码的独立发展，它们可能会过时。

鲍勃大叔的《干净的代码》一书用“用代码解释你自己”这句话很好地总结了这一点。我们应该在代码本身中记录我们的意图，通过使用清晰的命名和下面提到的使代码可读的其他结构。如果我们认为代码需要一些解释，而不是写一个注释，尝试重构以将含义编码到代码本身中。

回想一下你读过的最后一条评论。有用吗？准确吗？可以修改代码来包含这些信息吗？

但是有一个例外:如果我们正在编写一个其他人会使用的库，那么按照适当的约定向面向公众的 API 添加注释对于文档生成可能是有用的。

## 相反，使用异常来表示异常行为

如果我们想发出异常行为的信号，抛出一个`Exception`而不是返回一个错误值，原因如下:

*   异常行为不会被悄悄忽略，相反，它会传播到第一个错误处理程序，这通常是我们想要的
*   调用者不需要“记住”检查错误场景，这意味着更少的错误处理样板文件
*   许多 Kotlin 标准库构造很好地处理了面向异常的信号(例如，`Result`，`Preconditions`)
*   Kotlin 不像 Java 那样强制声明/处理检查异常，所以 Kotlin 中不存在常见的样板文件。

对于正常程序行为的信号，不要使用`Exceptions`。相反，我们有两个选择:

*   为了区分流程状态，使用密封的类，它与`when`结构配合得很好(下面将详细介绍)
*   为了处理预期的失败(例如用户输入验证)，使用`null`返回值(例如，使用 Kotlin 的许多`toFooOrNull()`方法)

## 使用 require/check 验证不应发生的条件

Kotlin 有一套前提条件验证函数(在一个叫做`Preconditions.kt`的文件中)。不使用`if`条件，而是使用`require`或`check`来增加代码的含义并减少冗长。

*   如果我们正在检查一个输入或参数。这一扔`IllegalArgumentException`。
*   `check`针对其他场景。这抛出了`IllegalStateException`。

如果我们正在使用一个 Kotlin 可空对象或一个平台类型，并且不希望它在这一点上为空，那么使用`requireNotNull`或`checkNotNull`。

```
require(arg.length < 10) {
    "message"
}

val result = checkNotNull(bar(arg)) {
    "message"
}

/////////////// instead of ///////////////

if (arg.length < 10) {
    throw IllegalArgumentException("message")
}

val result = bar(arg)
    ?: throw IllegalStateException("message")
```

## 使用扩展功能添加含义并启用链接

如果我们有一个需要注释的代码块，这个代码块应该变成一个方法，就像这样(但是继续读下去；还有更多):

```
val user = getUser(id)
validate(user)
activate(user)

private fun validate(user: User) { 
   // validate
}

private fun activate(user: User) { 
   // activate
}

/////////////// instead of ///////////////

val user = getUser(id)

/*
 * Validate user
 */
// validate 

/*
 * Activate user
 */
// activate
```

当我们创建像上面这样的方法时，我们已经封装了一些含义，但我们可以进一步使它更加流畅。流畅代码的很大一部分是设计[流畅界面](https://www.martinfowler.com/bliki/FluentInterface.html)，其中很大一部分是将相关操作链接在一起的能力。如果我们控制了`User`类，我们可以添加`validate`和`activate`作为方法，但是如果这两个方法只在这个类中有意义(或者如果我们不拥有`User`)，那么[扩展函数](https://kotlinlang.org/docs/reference/extensions.html#extension-functions)将会帮助我们。

其实是[推荐](https://kotlinlang.org/docs/reference/coding-conventions.html#using-extension-functions)尽量使用扩展！

> 大量使用扩展函数。每当你有一个主要作用于一个对象的函数时，考虑让它成为一个扩展函数，接受那个对象作为接收者。

```
private fun User.validate(): User { 
   // validate
   return this
}

private fun User.activate(): User { 
   // activate
   return this
}

...

val user = getUser(id)
   .validate()
   .activate()
```

## 考虑使用或创建中缀函数来减少冗长

Kotlin 标准库中有许多中缀函数，它们用来减少嵌套括号。

```
val x = mapOf(1 to "a")
val range = 1 until 10
val loop = listOf(...) zip listOf(...)

/////////////// instead of ///////////////

val x = mapOf(1.to("a"))
val range = 1.until(10)
val loop = listOf(...).zip(listOf(...))
```

我们可以创建自己的中缀函数，只有当函数满足以下标准时，我才会推荐它:

*   没有副作用
*   简单的逻辑
*   简称
*   用在可以少用括号的地方

最后一点很重要，给出了一个使用中缀函数的场景*而不是*:由于缺少括号，中缀函数可能不会以可读的方式链接。例如，如果我们在一个链中使用两个函数:

```
private fun process(arg: String) = // some string
private fun String.foo(x: String) = // some string

val bar = process("a")
   .foo("b")
   .min()
```

如果`foo`是`infix`，使用它看起来会很奇怪，因为需要额外的括号来获得预期的行为。在这种情况下，由于它的使用方式，不使它成为中缀是可以的。

```
val bar = (process("a") foo "b").min()  // bad
```

## 使用范围函数来减少冗长

`with`帮助创建一个与对象相关的逻辑部分。

```
...some code...
with(foo.id) {
   LOGGER.info("id is $this")
   doSomething()  // method of id
   doSomethingElse(this)
}
...some code...

/////////////// instead of ///////////////

...some code...
val id = foo.id
LOGGER.info("id is $id")
id.doSomething()
doSomethingElse(id)
...some code...k
```

`apply`与只有 setters 的对象交互时会产生奇迹。

```
val foo = Foo().apply {
   field1 = 1 
   field2 = "a"
}

/////////////// instead of ///////////////

val foo = Foo()
foo.field1 = 1
foo.field2 = "a"
```

`also`可以让我们重用一个对象来添加额外的效果

```
requireNotNull(foo) {
   "message with ${foo.id}"
      .also { LOGGER.error(it) }
}

/////////////// instead of ///////////////

requireNotNull(foo) {
   val message = "message with ${foo.id}"
   LOGGER.error(message)
   message
}

/////////////// or worse ///////////////

requireNotNull(foo) {
   LOGGER.error("message with ${foo.id}")
   "message with ${foo.id}"
}
```

我写了一个对[范围函数](https://medium.com/dont-code-me-on-that/kotlin-cheatsheet-scope-functions-let-run-apply-also-with-308c8e5533f4)的快速引用。

## 默认情况下省略类型信息

Kotlin 具有类型推断功能，IDE 将类型信息显示为提示，因此我们几乎总是想省略用代码编写它，除非语法要求这样做。

```
val x = "a"

override fun foo() = 1

/////////////// instead of ///////////////

val x: String = "a"

override fun foo(): Int = 1
```

但是，在一些情况下，类型信息会很有用:

*   当一个函数或字段的返回类型过于复杂而无法一眼确定时，例如`Map<Int, Map<String, String>>`
*   返回[平台类型](https://kotlinlang.org/docs/reference/coding-conventions.html#platform-types)时。

## 如果函数有 1 个表达式，则使用表达式语法

只要函数由一个表达式组成，不管它有多长，我们都应该优先使用表达式语法。

```
fun foo(id: Int) = getFoo(id)
   .chain1()
   .chain2()
   .chain3()
   .chain4 {
      // some lambda
   }

/////////////// instead of ///////////////

fun foo(id: Int): Bar {
   return getFoo(id)
      .chain1()
      .chain2()
      .chain3()
      .chain4 {
         // some lambda
      }
}
```

这与范围函数结合使用效果很好。

```
fun foo(arg: Int) = Foo().apply {
   field1 = arg
   field2 = "a"
   field3 = true
}

/////////////// instead of ///////////////

fun foo(arg: Int): Foo {
   return Foo().apply {
      field1 = arg
      field2 = "a"
      field3 = true
   }
}
```

例外:当返回类型为`Unit`时，即使被调用的方法也返回`Unit`或`void`，也不要使用表达式语法。这使得一眼就能看出该函数不返回任何内容。

```
fun foo() {
   barThatReturnsUnitOrVoid()
}

/////////////// instead of ///////////////

fun foo() = barThatReturnsUnitOrVoid()
```

## 使用 typealias 或内联类为常见类型添加含义

有些类型是通用的或者是满嘴的(满眼？).我们可以使用 [typealias](https://kotlinlang.org/docs/reference/type-aliases.html#type-aliases) 或[内联类](https://kotlinlang.org/docs/reference/inline-classes.html)来为难以理解的类型添加编码。

```
typealias CustomerId = Int
typealias PurchaseId = String
typealias StoreName = String
typealias Report = Map<CustomerId, Map<PurchaseId, StoreName>>

fun(report: Report) = // ...

/////////////// instead of ///////////////

fun(report: Map<Int, Map<String, String>>) = // ...
```

内联类是相似的，除了我们声明一个实际的类来包装原始类型。相对于 typealias 的好处是，`CustomerId` typealias 接受任何`Int`，而`CustomerId`内联类将只接受其他`CustomerId`。

## 使用精度标记来指定数值的精度

Kotlin 有[精度标签](https://kotlinlang.org/docs/reference/basic-types.html#literal-constants)来区分 Double/Float 和 Int/Long。更喜欢用它们来指定类型。

```
val x = 1L
val y = 1.2f

/////////////// instead of ///////////////

val x: Long = 1
val y: Float = 1.2
```

## 使用下划线对数值进行可视化分组

在文字中尽可能使用下划线。

```
val x = 1_000_000

/////////////// instead of ///////////////

val x = 1000000
```

## 使用字符串模板和原始字符串

[字符串模板](https://kotlinlang.org/docs/reference/basic-types.html#string-templates)(或字符串插值)在大多数情况下优先于串联、`String.format`或`MessageFormat`。[原始字符串](https://kotlinlang.org/docs/reference/basic-types.html#string-literals)在处理多行文本或含有大量特殊字符的文本时也很有用，否则这些字符需要转义。

```
val x = "customer $id bought ${purchases.count()} items"
val y = """He said "I'm tired""""

/////////////// instead of ///////////////

val x = "customer " + id + " bought " + purchases.count() + " items"
val y = "He said \"I'm tired\""
```

## 如果为空，使用 Elvis 运算符返回

处理可空类型的一个常见场景是，如果需要空值，则返回某个值，在这种情况下，我们可以使用 if-null-then-return 模式。在这种情况下，elvis 操作器很方便。

```
val user = getUser() 
   ?: return 0

/////////////// instead of ///////////////

val user = getUser()
if (user == null) {
   return 0
}
```

但是，如果`user`来自方法参数呢？没有`getUser()`附`?:`。为了保持一致性，我们仍然更喜欢使用`?:`而不是`if`。

```
fun foo(user: User?): Int {
   user ?: return 0
   // ...
}

/////////////// instead of ///////////////

fun foo(user: User?): Int {
   if(user == null) {
      return 0
   }
   // ...
}
```

## 正确使用流和序列

从 Java 过来，我们在转换集合的时候可能会习惯性的使用`.stream()`。如果我们这样做，我们实际上是在使用 Java 流 API。相反，Kotlin 将其流方法定义在`Iterable`上。

```
listOf(1).map { ... }

/////////////// instead of ///////////////

listOf(1).stream().map { ... }.collect(...)
```

科特林的“流”方法是热切的，而 Java 的方法是懒惰的，所以等同于科特林的`Sequence`方法。对于处理大型集合或多步转换，使用`Sequence`方法将会带来[更好的性能](https://blog.kotlin-academy.com/effective-kotlin-use-sequence-for-bigger-collections-with-more-than-one-processing-step-649a15bb4bf)而不会牺牲太多的可读性。

```
listOf(1).asSequence()
   .filter { ... }
   .map { ... }
   .maxBy { ... }

/////////////// instead of ///////////////

listOf(1)
   .filter { ... }
   .map { ... }
   .maxBy { ... }
```

## 使用面向方面的模式来附加副作用

我在这篇[文章](https://medium.com/dont-code-me-on-that/aspect-oriented-programming-in-kotlin-95ff8598913)中阐述了更多，但是总的来说，这种模式让我们无需过多赘述就能添加副作用。举个简单的例子，如果我们有这样一个函数:

```
fun getAddress(
   customer: Customer
): String {
   return customer.address
}
```

我们可以通过使用该模式定义一个`cachedBy`函数并只修改一小部分代码来轻松添加缓存:

```
fun getAddress(
   customer: Customer
): String = cachedBy(customer.id) {
   customer.address
}

/////////////// instead of ///////////////

fun getAddress(
   customer: Customer
): String {
   val cachedValue = cache.get(customer.id)
   if (cachedValue != null) {
      return cachedValue
   }

   val address = customer.address
   cache.put(customer.id, address)
   return address
}
```

## 使用密封类来处理流程状态

我在这篇[文章](https://medium.com/dont-code-me-on-that/handle-process-results-with-kotlin-sealed-classes-d9a9375b8e09)中阐述了更多，但是一般来说，密封类让我们减少了评估状态代码的冗长性，并且避免了维护额外的状态枚举。

## 测试方法名称使用反斜线

如果我们正在编写冗长的方法测试名称，使用反勾号使其更容易阅读通常是有用的。这将让我们使用空格和一些特殊字符。

```
fun `test foo - when foo increases by 3% - returns true`() { ... }

/////////////// instead of ///////////////

fun testFoo_whenFooIncreasesBy3Percent_returnsTrue() { ... }
```

## 更喜欢广度优先而不是深度优先的代码

*这不是 Kotlin 特有的，但我们将探索 Kotlin 如何使它变得更容易。*

深度代码是有许多“层”的代码，或者对 unfamiliar^方法的调用。这不是一个技术问题，而是一个认知问题。一般来说，我们的[工作记忆](https://en.wikipedia.org/wiki/The_Magical_Number_Seven,_Plus_or_Minus_Two)有限，在阅读代码时，每次调用另一个不熟悉的方法都会给工作记忆增加更多的上下文，增加认知负荷。

最好用一个例子来解释。比方说我们有这些`fun`:

```
fun foo(): Foo {
  val foo = getFoo()
  return foo1(foo)
}

private fun foo1(foo: Foo): Foo {
   ...something with foo...
   return foo2(foo)
}

private fun foo2(foo: Foo): Foo {
   ...something with foo...
   return foo3(foo)
}

private fun foo3(foo: Foo): Foo {
   ...something with foo...
   return foo
}
```

当我们读取对`foo()`的调用时，我们看到对`foo1()`的调用。现在我们把`foo()`放到工作内存“栈”的后面，读取`foo1()`。如此往复，直到我们到达`foo3()`，然后我们开始往回走`foo()`。这意味着为了理解`foo()`，我们需要在我们的工作记忆中添加许多“条目”，每个条目都是被调用函数的上下文。

将其与同等宽度的产品进行比较:

```
fun foo(): Foo = 
   getFoo()
      .foo1()
      .foo2()
      .foo3()

private fun Foo.foo1(): Int {
  ...something with this (which is foo)...
   return this
}

private fun Foo.foo2(): Foo {
   ...something with this (which is foo)...
   return this
}

private fun Foo.foo3(): Foo {
   ...something with this (which is foo)...
   return this
}
```

使用第二种方法，我们只需要在我们的“堆栈”中保存一个上下文，即`foo()`的上下文。每次另一个`fun`被调用时，它都返回到相同的上下文，所以我们不必“深入”了。

作为另一个例子，这一行有许多嵌套层:

```
return Foo(
   bar.doSomething(
      getId(SomeEnum.ENUM_1),
      "string"
   )
)
```

对于一些扩展函数和作用域函数，这可以是“非嵌套的”,如下所示:

```
return SomeEnum.ENUM_1
   .getId()
   .let {
      bar.doSomething(it, "string")
   }.let {
      Foo(it)
   }
```

这些例子被夸大了，这个经验法则并不适用于所有的场景。然而，在大多数情况下，广度优先的方法更容易阅读。这也可以在 Java 中实现，但是 Kotlin 使用了一些构造，比如扩展函数和作用域函数，使得这变得更加简单。

总之，Kotlin 提供了一些有助于“取消嵌套”的语法特性。当处理有许多层的代码时，考虑用构造来减少层数，并选择对我们的读者来说更可读的方法。

*^A 关于“不熟悉的”方法的注释:这些方法不期望读者一眼就能认出来，因此它排除了大多数“内置”方法，如* `*String.split*` *以及我们代码中常用的方法，这些方法可能很复杂，但读者可以立即理解。*

## 隔离不流畅的代码

有时，无论我们如何努力，一段代码看起来仍然不可读，因为:

*   它有笨拙的操作或者使用笨拙的 API，上面的建议都不能驯服它
*   有一些性能考虑决定了代码的结构
*   我们只是目前没有时间投资制作它

此时最好的做法是隔离代码，这样代码库的其他部分就不必直接与之交互。不管我们是使用一个独立的类还是一个单独的方法，都要花一些时间来精心制作可读且有意义的方法签名。

隔离代码通常还会使代码更具可测试性，这在底层代码可读性较差时至关重要，因为它是预期行为的活文档。

```
... some fluent code ...
val fee = activateCustomerAndCalculateFee(
   userId,
   FeeType.SIMPLE,
   DEFAULT_CUSTOMER_TYPE
)
... other fluent code ...

fun calculateFeeForCustomer(
   userId: String,
   feeType: FeeType,
   customerType: Int
): Double {
   ... some complicated or not-so-readable code
}
```

总的来说，如果我们在编写代码的多种方法之间进退两难，我会使用这些经验法则来做出选择。选择以下选项:

*   让代码的意图变得清晰。未来——你会感谢你自己！
*   优化略读。选择代码结构，使读者容易浏览到代码中的正确位置。
*   不太冗长。写得越差，越不容易分心。

你已经到达终点了！这是一篇活的文章，随着我遇到更多的“风格选择”，它将会成长，所以请不时回来查看更新！

## 参考

*   [让它工作，让它美丽，让它快速](https://tomharrisonjr.com/make-it-work-make-it-beautiful-make-it-fast-three-realities-df7255a8fa09)
*   干净的代码:敏捷软件工艺手册

**你可能喜欢的其他故事**

*   [探索科特林的双重平等](/double-equality-in-kotlin-f99392cba0e4)
*   [软件评估是一种双边关系](/opinion-software-estimates-are-a-two-sided-relationship-6247108ace41)
*   [使用 Java 可空性注释来简化到 Kotlin 的转换](/dont-code-me-on-that/use-java-nullability-annotations-to-facilitate-conversion-to-kotlin-7196f0cee9e9)
*   我也写关于 [TryHackMe 房间](https://medium.com/dont-code-me-on-that/tryhackme-writeups-and-ctf-logs-catalogue-f9814cdb698f) (Pentesting)！

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)