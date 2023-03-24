# 如何消除 Java Lambdas 中的 Try-catch 块

> 原文：<https://levelup.gitconnected.com/how-to-eliminate-try-catch-blocks-in-java-lambdas-72c1c9f878f5>

## 并将库部署到 Maven Central 来完成它。

![](img/4fe8e48b380926ec2acf1c81d82debce.png)

Ryan Quintal 在 [Unsplash](https://unsplash.com/s/photos/building-blocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

这是我上一篇文章[处理函数式 Java 中的异常](https://medium.com/dev-genius/dealing-with-exceptions-in-functional-java-8d1c322a050e)的续篇，有点吃我自己的狗粮，来自我的文章[分享很难](https://medium.com/dev-genius/sharing-is-hard-dc06310340d9)。在处理函数式 Java 中的异常的最后，我指出了仍然缺少一些 lambda 类型的地方，并且我不打算尝试实现它们。但是命运对我另有安排。

写完《分享很难》之后，我决定着手一个新的过程，将一些东西推送到 Maven Central 供所有人分享。事实证明这相对容易，最难的部分是设置我的 PGP 密钥，这样我就可以对我的 jar 进行签名，这是部署到 Maven Central 的一个要求。我不会讨论如何部署这个库，你可以在这里找到很好的说明。但在大多数情况下，它只是确保你的`pom.xml`遵守一些基本标准，并在构建部分包含一些插件。

我不得不改变原始代码的某些方面，在 Functional Java 中处理异常，所以我没有继续使用同一个存储库，而是创建了一个名为[可能是](https://github.com/rkamradt/Possibly)的新存储库，以库中的主类命名，即可以是值或异常的`Possibly`类型。

我改变的一个根本性的东西是`groupId`；通过将它设置为`io.github.rkamradt`，我将被自动识别为`groupId`的所有者，因此不会有名称冲突的机会。我还把包名改成了`io.github.rkamradt`，这是我让基础包和`groupId`相同的常用模式，下一个包名是`artifactId`，也就是`possibly`。

在处理函数 Java 中的异常的最后，我用类替换了`Function`和`Supplier`。为了让这个库更加完整，我想要替换掉`Consumer`和`Predicate`。那些替换有我必须处理的独特问题，主要是我不能返回一个可能的类来包含异常，所以我必须想出一些新的东西。

所以对于`Consumer`和`Predicate`，我添加了一个额外的字段`Consumer<Exception>`，它是在构建器中设置的。因此，要使用`Consumer`函数，就像在`Stream.peek`中一样，您可以使用:

```
List<String> list = Stream.of(GOOD_VALUE, BAD_VALUE)
    .peek(PossiblyConsumer.of(s -> perilousLogging(s), 
                              e -> log.error(e)))
    .collect(Collectors.toList());
```

`periousLogging`函数记录传入的字符串，除非该字符串等于`BAD_VALUE`，在这种情况下，它抛出一个异常。在这种情况下，`Consumer<Exception>`只是记录抛出的异常。如果你想忽略这个异常，只要不传入`Consumer<Exception>`它就会忽略它。

`PossiblyPredicate`类似于`Consumer<Exception>`对可能抛出的`Exception`做一些事情。如果抛出一个`Exception`，它也将返回 false，但是未来的一个增强可能是能够在抛出一个`Exception`时设置返回。

在很大程度上，这些允许你忽略一个`Exception`并提供合理的行为。它还允许您记录异常或其他副作用。此外，如果您真的想让一个`Exception`上的处理失败，您可以作为一个`RuntimeException`或一些其他未检查的异常重新抛出。

## 吃我自己的狗粮

为了测试这个新库，我使用了自从我的文章[理解反应式 Java](/understanding-reactive-java-e8aaee9a204b) 以来我一直在做的代码。这是一组读写消息队列和数据库的服务，都使用了 Project Reactor reactive Java。目标是消除代码中的每一个尝试捕捉。由于它已经被部署到 Maven Central，我们只需在`pom.xml`中添加一个依赖项:

```
<dependency>
   <groupId>io.github.rkamradt</groupId>
   <artifactId>possibly</artifactId>
   <version>1.0.1</version>
</dependency>
```

大多数 try-catch 块都在 JSON 的解析和序列化中。在每种情况下，我都需要决定在出现异常的情况下该怎么做。在大多数情况下，当解析时，我简单地记录并忽略该事件。当序列化时，这几乎总是一种不应该发生的情况，因为我们正在序列化我们知道是可序列化的对象。同样，记录并忽略。

在很多情况下，在我的原始代码中，我用 try-catch 获取块，并将它们放在一个函数中。例如，读取代表汽车的`String`有效载荷的代码有这样的功能:

```
private static Payload readCarJson(String data) {
  try {
    return carReader.readValue(data);
  } catch (JsonProcessingException ex) {
    ContextLogging.log("unable to parse car");
    ex.printStackTrace(System.out);
    Payload empty = new Payload();
    empty.eventId = "no event id";
    empty.car = new Vehicle.Car();
    return empty;
  } catch (IOException ex) {
    ContextLogging.log("unable to parse car");
    ex.printStackTrace(System.out);
    Payload empty = new Payload();
    empty.eventId = "no event id";
    empty.car = new Vehicle.Car();
    return empty;
  }
}
```

然后我在一个地图函数中使用了它:

```
.map(d -> readCarJson(new String(d.getBody())))
```

使用这个新的库，我能够去掉`readCarJson`函数，直接在 map 函数中使用 Jackson 解析器:

```
.map(PossiblyFunction.of(d -> 
        carReader.readValue(new String(d.getBody()))))
.map(p -> p.getValue().orElseGet(() -> 
        new Payload("unknown event id", new Vehicle.Car())))
```

在这种情况下，第一个地图返回一个`Possibly<Payload>`，它或者有一个`Payload`或者有一个`Exception`。第二个 map 使用`Possibly.getValue`方法返回一个`Optional<Payload>`，如果有异常，这个值将为空。如果抛出了一个异常，我们忽略它，我们只返回一辆空车，稍后我们可以过滤掉它。如果我们想要记录异常，我们可以使用`Flux.doOnNext`方法和`Possibly.doOnException`方法来记录:

```
.doOnNext(p -> p.doOnException(e -> log.error("error", e)))
```

因为我所做的大部分工作是从`Object`映射到`String`然后再映射回来，所以我只能测试出 map 所使用的`PossiblyFunction`。所以在可能库的其他函数类型中可能潜伏着错误。但是我能够从应用程序中删除所有的 try-catch 块。他们没有消失，只是被埋在我久经考验的图书馆里。

本文中使用的存储库:

[](https://github.com/rkamradt/Possibly) [## rkamradt/可能

### 创建可以用作 lambdas 的方法，以合理的方式处理异常。关键类是…

github.com](https://github.com/rkamradt/Possibly) [](https://github.com/rkamradt/usedvehicles/tree/v0.5) [## rkamradt/二手车辆

### 在 GitHub 上创建一个帐户，为 rkamradt/usedvehicles 的开发做出贡献。

github.com](https://github.com/rkamradt/usedvehicles/tree/v0.5) 

提到的其他文章:

[](https://medium.com/dev-genius/dealing-with-exceptions-in-functional-java-8d1c322a050e) [## 在函数式 Java 中处理异常

### 像专家一样抛出异常

medium.com](https://medium.com/dev-genius/dealing-with-exceptions-in-functional-java-8d1c322a050e) [](https://medium.com/dev-genius/sharing-is-hard-dc06310340d9) [## 分享很难

### 但是不分享更难

medium.com](https://medium.com/dev-genius/sharing-is-hard-dc06310340d9) [](/understanding-reactive-java-e8aaee9a204b) [## 理解反应式 Java

### 因为你的线程阻碍了我的表现。

levelup.gitconnected.com](/understanding-reactive-java-e8aaee9a204b)