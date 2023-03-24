# 合理可选还是非此即彼？

> 原文：<https://levelup.gitconnected.com/reasonable-optional-or-either-2359e536cf64>

![](img/275a2fdc42ca16e22804abb5ae0e644f.png)

照片由 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇博客文章以我的基于 Java 的应用程序中的一个简单问题开始:我已经获得了一个 API 访问令牌，如果它有效，我想返回相关的 API 客户机标识，否则不返回标识。瞧，现在已经不是 2013 年了，我们正是为了这个目的而举办了`Optional`！所以我们有了一个`Optional<Identity> getIdentityForValidToken(Token token)`函数，有一段时间，它是好的。

然后出现了更多的验证规则，并且需要将令牌被拒绝的原因传播给调用者。`**Optional**` **只能在场或缺席而不能解释为什么**。类似的传播原因的需求是很常见的，所以让我们来看看一些可能的解决方案。

在这篇文章的其余部分，我们将研究在不同的上下文和语言中解决类似问题的两种模式，并以我们在 Java 中能做什么来结束。

# 例外

您首先想到的可能是使用异常。异常表示无法检索到请求的值，并且包含原因。在原因确实异常的情况下，这是一个有效的解决方案，而不是因为预期的情况，尤其是因为[性能](https://stackoverflow.com/q/299068/2032064)。我承认对于过期的令牌属于哪一类有不同的看法(库确实会在解析或验证失败时抛出异常)，但是为了便于讨论，让我们假设它不是异常的。

# 异步处理

我们的问题已经得到很好解决的一个领域是异步执行，这可能是因为它更加普遍。

例如，让我们看一个 JavaScript `Promise`。它可以有三种状态:待定、已实现或已拒绝。当构造一个`Promise`时，你会得到函数引用，这些引用可以使它被满足或者被拒绝:

```
new Promise((resolve, reject) => {
  setTimeout(() => { resolve(‘foo’); }, 300);
});
```

可以用任何类型的理由拒绝 A `Promise`。它还提供了一个带有`then()`、`catch()`和`finally()`方法的方便流畅的接口，可以对任何一种结果状态做出反应，甚至可以将承诺转换为另一种状态。

## Java 的未来

在 Java 中，我们让`CompletableFuture`实现了`CompletionStage`和`Future`接口。它提供了与`Promise`相似的功能，只是使用了更复杂的 API(从 Java 8 开始有 52 个实例方法)。但是使用 Java Futures 也不是解决我们原始问题的合适方法。它们是为另一个目的而设计的，不适合同步代码。异步函数[像病毒一样](https://blog.softwaremill.com/will-project-loom-obliterate-java-futures-fb1a28508232)，不像 JavaScript 的`Promise`，它们只能用一个`Throwable`拒绝，而且，嗯，异步。这不是我们想要的。

# 反应流

解决方案常见的另一个领域是反应流。 [RxJava 的单个](http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/rxjava3/core/Single.html)允许表示一个值或一个被拒绝的状态，并通过进一步的方法调用如`map()`和`flatMap()`来处理结果。缺点是，就像`CompletableFuture`一样，一个错误只能用一个`Throwable`来表示，如果你还没有使用 RxJava，你不希望仅仅为了得到 Single 而添加它。

# 多个返回值

Go 既没有`Optional`也没有异常。可以用 [nil 或指针](https://stackoverflow.com/questions/30731687/how-do-i-represent-an-optional-string-in-go)来表示一个缺失的值，但是当你也需要原因的时候，有一个简单的方法可以让[从一个函数返回多个值](https://gobyexample.com/multiple-return-values)。这是一种通常用作异常的[替代的模式。按照惯例，值在左边，可能的误差在右边。在使用该值之前，应该检查错误。](https://medium.com/@hussachai/error-handling-in-go-a-quick-opinionated-guide-9199dd7c7f76)

```
func Marshal(v interface{}) ([]byte, error) {
    e := &encodeState{}
    err := e.marshal(v, encOpts{escapeHTML: true})
    if err != nil {
        return nil, err
    }
    return e.Bytes(), nil
}
```

上面的[错误](https://golang.org/pkg/builtin/#error)返回值是一种类型，因此您可以灵活地表示它。在我看来，仍然有改进的空间，例如，因为这只是一个惯例，它使得更难执行或静态检查你的代码(想想 IDE 抱怨你没有在 Java `Optional`上的`get()`之前检查`isPresent()`)。一个被流畅的界面宠坏的人也可能会失望。但是我们越来越接近了。

# 非此即彼。

除了`Option`类型，Scala 还提供了`Either`和`Try`。`[Try](https://www.scala-lang.org/api/2.9.3/scala/util/Try.html)`适用于例外情况——失败总是与可抛性联系在一起。所以我们来看`[Either](https://www.scala-lang.org/api/2.9.3/scala/Either.html)`代替。

`Either`的实例可以是`Left`或`Right`子类的实例，其中`Left`代表约定的错误状态。这些类型是通用的，所以您可以自由选择 Left 可以包含什么类型的值。以下是文档中的一个示例:

```
val l: Either[String, Int] = Left(“flower”)
val r: Either[String, Int] = Right(12)
l.left.map(_.size): Either[Int, Int] // Left(6)
r.left.map(_.size): Either[Int, Int] // Right(12)
l.right.map(_.toDouble): Either[String, Double] // Left(“flower”)
r.right.map(_.toDouble): Either[String, Double] // Right(12.0)
```

正如你所看到的，你可以使用`Either`作为一个[单子](https://medium.com/free-code-camp/demystifying-the-monad-in-scala-cc716bb6f534)，并通过`flatMap()`等函数流畅地左右映射。万岁，这似乎是目前为止最好的解决方案:在拒绝原因值上是通用的，并且具有流畅的(一元的)接口。

# 其他人

为了完整起见，我也许应该提到其他语言中 Optional 的替代品，比如可空类型 [C#](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types) 或 [Kotlin](https://kotlinlang.org/docs/reference/null-safety.html) 。据我所知，它们没有提供任何选项来返回丢失值的原因。

# 要么用 Java

在 Java 中，我们只剩下[库](https://stackoverflow.com/q/26162407/2032064)。

[Vavr](https://www.vavr.io/) 和 [Functional Java](https://github.com/functionaljava/functionaljava) 是一个`Either`类型的 Java 函数库。但是，它们包含的内容远不止这些，所以在将它们作为依赖项添加之前，您可能需要更详细地检查它们。

[亚特兰蒂斯赋格](https://bitbucket.org/atlassian/fugue/src/master/)提供了便利类，比如更聪明的`Option`、`Either`和`Pair`。这里有一个`Either`用法的例子:

```
Either<Integer, String> either = Either.right(“value”);
either
    .map(String::toUpperCase)
    .left().map(this::doSomethingWithRejection);
    .getOrNull();
```

神游是更轻量级的包含其他不错的实用程序，如[检查转换为可选的](https://docs.atlassian.com/fugue/4.5.1/fugue/apidocs/io/atlassian/fugue/Functions.html#isInstanceOf-java.lang.Class-)。

我要讲的最后一个也是最简单的选项是[矛盾心理](https://github.com/poetix/ambivalence)。它主要提供其`Either`类型:

```
Either.ofRight(23);
    .left().flatMap(this::myFunction)
    .join(String::toUpperCase, Object::toString); // string “23”
```

# 结论

鉴于我经常遇到需要传播一个丢失的值以及它丢失的原因，我很惊讶标准库提供的帮助是如此之少。对于带有未来/承诺的异步执行，这个问题似乎被巧妙地解决了。在同步世界中，Go 更接近，但只有 Scala 提供了合理的内置解决方案和`Either`。

我更愿意做得更好，例如，我不喜欢`Either`的惯例，即`Left`通常代表拒绝——对于不了解惯例的人来说，这违反了[最小惊喜](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)原则。除非你自己创造一个更好的解决方案，否则目前 Java 中最好的解决方案似乎是求助于带有`Either`的库。我从上面选择的是[亚特兰蒂斯赋格](https://bitbucket.org/atlassian/fugue/src/master/)。