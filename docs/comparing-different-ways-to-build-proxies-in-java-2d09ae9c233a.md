# 比较在 Java 中构建代理的不同方法

> 原文：<https://levelup.gitconnected.com/comparing-different-ways-to-build-proxies-in-java-2d09ae9c233a>

![](img/9f97ff269da0e5450a42b396c6a54edd.png)

照片由[克里斯](https://unsplash.com/fr/@chris23?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

流行的 Java 框架和库常用的技术之一是对象代理。尽管这是开发人员日常使用的许多库中使用的流行模式，但许多开发人员从未在自己的代码中直接构建过 Java 代理。虽然用例可能不会在日常代码中经常出现，但它的使用在某些情况下是有意义的，并且了解我们代码的依赖项通常如何完成它们的任务总是值得您花费时间的。

## 什么是代理？

在这个上下文中，代理是一个对象，它有权拦截对特定类的调用。我喜欢把它想象成一个围绕着现有物体的层。这并不意味着每个方法或与代理的交互都会被拦截，但它有这个能力。当代理被调用时，原始方法可能仍然被调用，原始代码可能被完全忽略，或者两者的组合。

## 我为什么要使用代理？

鉴于上述定义，一些用例可能已经浮现在脑海中。也许您想用日志记录或性能监控代码包围方法。或者，您可能希望实现事务控制，并在方法成功执行时提交事务，或者在抛出异常时回滚事务(如果您曾经在 Spring 框架中使用过`@Transactional`注释，这个用例可能听起来很熟悉)。也许你想在一个不支持继承的类中修改一个特定的方法。说到代理的用例，天空是无限的。

## 实现代理的方法

根据你想用你的代理做什么，你会有不同的方法。这可能取决于您是否拥有想要包装的代码，无论它是您正在创建的具体类还是接口。您是否还需要完成其他功能和即时生成。这只是举几个例子。

## 比较方法

在这篇文章中，我选择比较以下几点:

*   继承(功能最弱，但很简单，可以实现代理的一些好处)
*   一对一方法委托。(对于包装类的每个方法，我们在代理中编写一个相同的方法并传递调用(除非我们想改变它的工作方式))
*   [动态代理](https://www.baeldung.com/java-dynamic-proxies)(内置 JDK)
*   byte buddy——mock ITO、Jackson、Hibernate 等使用的代码生成和修改库。
*   [cglib](https://github.com/cglib/cglib) —现在无人维护的代码生成库。(在 Java 17 中不可用)(由 Spring framework 使用([他们自己维护](https://github.com/spring-projects/spring-framework/tree/main/spring-core/src/main/java/org/springframework/cglib)))

其中一些方法比其他方法有更多的功能，但是对于这个测试，我们只关注一些简单的功能。

*   在包装代码之前和之后运行代码。
*   替换包装方法的部分实现
*   替换一个方法的全部功能。

除此之外，我还执行了以下测试

*   创建代理对象实例的性能。
*   调用代理中未经代理编辑的方法，以比较代理的影响，即使它不做任何事情。

让我们深入了解每项测试。

*代理创建*

```
Benchmark                                    Score     Error Units
InitializationSpeed.byteBuddy                0.281 ±    0.003  ops/ms
InitializationSpeed.cglib                 7158.735 ±   14.760  ops/ms
InitializationSpeed.dynamicProxy         54906.036 ±  137.333  ops/ms
InitializationSpeed.inheritanceProxy    201756.605 ± 6483.339  ops/ms
InitializationSpeed.oneForOne           141938.219 ±  405.756  ops/ms
```

这些不同代理选项的创建速度有相当大的差距。创建一个具有继承的类是最快的，因为创建的类利用了相同数量级的组合而不是继承。动态代理是最快的、真正动态的方法。令我惊讶的结果是 ByteBuddy 结果。也许这是一个如何编码的问题，但它比其他方法慢得多。值得注意的是，ByteBuddy 正在编写一个全新的类，并将其添加到类加载器中，这并不是一个轻量级的过程(尽管 cglib 做了类似的过程，但没有性能问题)。

*调用不相关的方法*

```
Benchmark                              Score       Error   Units
UnrelatedMethodTest.byteBuddy          2857.037 ±   10.480  ops/ms
UnrelatedMethodTest.cglib              2236.596 ±   31.475  ops/ms
UnrelatedMethodTest.dynamicProxy       2167.294 ±   10.853  ops/ms
UnrelatedMethodTest.inheritanceProxy   2833.592 ±    5.717  ops/ms
UnrelatedMethodTest.oneForOne          3245.791 ±   10.827  ops/ms
```

在这个测试中，在没有任何特殊处理的代理上调用了一个方法。这次我们所有的方法都在同一数量级上。我们的非继承类是最快的，这并不奇怪。另一个有趣的数据点是 ByteBuddy 略微领先于继承方法，我只能猜测，因为它是一个“真正的类”,而不是一个需要[动态分派](https://en.wikipedia.org/wiki/Dynamic_dispatch)的类。

*用包装方法调用方法*

```
Benchmark                              Score       Error   Units
SurroundingCallsTest.byteBuddy         3987.467 ±    7.130  ops/ms
SurroundingCallsTest.cglib             3567.403 ±   15.483  ops/ms
SurroundingCallsTest.dynamicProxy      3760.674 ±   31.131  ops/ms
SurroundingCallsTest.inheritanceProxy  4028.737 ±   15.962  ops/ms
SurroundingCallsTest.oneForOne         4013.130 ±   18.312  ops/ms
```

现在，对于包装方法，我们再次看到所有方法都具有相同的性能。我们更“经典”的方法确实在性能上略胜一筹，ByteBuddy 再次排在第三位，这似乎是一个常见的故事。

*用已编辑的实现调用方法。*

```
Benchmark                              Score     Error   Units
EditedCallsTest.byteBuddy              1166.783 ±   46.693  ops/ms
EditedCallsTest.cglib                  1114.962 ±   38.014  ops/ms
EditedCallsTest.dynamicProxy           1225.499 ±   37.439  ops/ms
EditedCallsTest.inheritanceProxy       1299.378 ±   19.568  ops/ms
EditedCallsTest.oneForOne              1305.832 ±    4.319  ops/ms
```

在这个测试中，我们替换了部分实现，然后将剩下的部分交给代理类实现。结果很大程度上遵循以前的结果，除了动态代理在这里确实提高了它的性能。

```
Benchmark                               Score      Error   Units
FullReplacement.byteBuddy               293955.382 ±  674.993  ops/ms
FullReplacement.cglib                   64039.112 ±  291.037  ops/ms
FullReplacement.dynamicProxy            62943.089 ±  769.907  ops/ms
FullReplacement.inheritanceProxy        321920.716 ± 1210.949  ops/ms
FullReplacement.oneForOne               347187.859 ± 4885.895  ops/ms
```

最后，我们用静态值替换方法的整个实现。就其本身而言，静态值的返回非常快，因此这个测试突出了函数调用时间的差异。不出所料，一对一和继承方法是最快的，但是 ByteBuddy 紧随其后，并且具有很好的性能。这真正显示了 ByteBuddy 的字节码生成的强大。

有许多方法可以完成为对象创建代理的任务。上面的一些库对于我们试图实现的目标来说是多余的，但是考虑它们是很有趣的。老实说，我原以为两种选择之间会有更大的差异。我怀疑使用动态代理创建代理会更快，但在执行时会更慢。我怀疑动态代理所需的反射会极大地影响它的执行时间。这表明测试性能是确定哪种解决方案最有效的唯一真正的方法。

深入到这些库和模式中，仍然有很多东西需要学习，但是这个对不同代理实现的性能(及其不足)的快速介绍有望在将来对您有所帮助。此外，了解有哪些工具可以用来解决特定问题也很有帮助。

执行测试的代码可在以下位置找到:

[](https://github.com/kylec32/javaproxytests) [## GitHub - kylec32/javaproxytests

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/kylec32/javaproxytests)