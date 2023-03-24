# 软件开发中的纯函数

> 原文：<https://levelup.gitconnected.com/pure-functions-in-software-development-d315a4520da1>

## 简单介绍一下纯函数以及它们如何帮助我们拥有一个好的可测试架构

![](img/5a2a58672dac549746033cc4af1ff516.png)

安特·罗泽茨基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

每当我们想到**的纯净**与**的不纯净**时，我们都努力得到尽可能接近 100%纯净的东西。

软件开发对纯度有同样的偏好吗？

# 什么是软件开发中的纯函数？

[](https://en.wikipedia.org/wiki/Pure_function) [## 纯功能-维基百科

### 在计算机程序设计中，一个纯函数是一个具有以下性质的函数:函数返回值…

en.wikipedia.org](https://en.wikipedia.org/wiki/Pure_function) 

如果我们在*维基百科上查找，*我们会注意到这些纯函数有两个属性:

1.  用相同的参数调用时，函数的返回值总是相同的
2.  功能应用没有副作用

嗯……让我们用一些实际例子来更好地理解它:

```
int sum(int a, int b) {
  return a + b;
}
```

*sum* 函数是否尊重纯函数的两个性质？

当然，对于相同的 *a* 和 *b* 值，我们将总是得到这些值的总和，而且，无论它在内部做什么，它都不会改变应用程序/模块的状态。

现在让我们尝试一个反例:

```
DateTime giveMeTheCurrentDate() {
  return DateTime();
}
```

让我们经历同样的过程？是否违背了纯函数的两个性质中的任何一个？

对第一个来说是这样的:每次我们调用它，结果都会不同，因为尽管我们很想这么做，但我们无法冻结时间😭

而现在的**100 万美元问题**？如果我们不能控制它并得到一个确定的结果，我们怎么能为它编写一个单元测试或任何类型的测试，并保证一切都按预期工作呢？

**是时候引入一些新的工具和概念了:**

*   从属倒置原则
*   控制世界

简单地说，**依赖倒置原则**声明我们不应该依赖具体类型，而应该依赖抽象。

让我们看看这在 **Flutter** 中会是什么样子(*抽象类*与其他语言中的*接口*等价——如果乍一看很怪异的话)

```
abstract class IDateManager {
  DateTime retrieveDate();
}class ConcreteDateManager implements IDateManager {
  [@override](http://twitter.com/override)
  DateTime retrieveDate() {
    return DateTime.now();
  }
}class DateService {
  DateTime giveMeTheCurrentDate(IDateManager manager) {
    return manager.retrieveDate();
  }
}
```

如果我们想获得具体实现的日期，这是一个不纯粹的函数，我们可以这样调用它:

```
final currentDate = DateService()
    .giveMeTheCurrentDate(ConcreteDateManager());
```

在这种情况下，一个纯函数会是什么样子呢？

让我们为我们的单元测试创建另一个确定性的类:

```
class DummyDateManager implements IDateManager {
  [@override](http://twitter.com/override)
  DateTime retrieveDate() {
    return DateTime.fromMicrosecondsSinceEpoch(1642167154);
  }
}
```

现在，如果我们用 *DummyDateManager* 调用 *DateService* ，它将始终产生相同的结果*2022 年 1 月 14 日星期五，13:32:34*

> 控制我们的依赖和环境使我们能够快速编写单元测试用例，并轻松重用代码库的部分内容。
> 
> 毕竟，当你写一段代码的时候，你就是它的创造者；为什么你不能控制时间？

我们包里的第二个工具是**控制世界**:我们的世界。在不涉及太多细节的情况下(因为这将在后面的故事中涉及)，这个想法是，所有外部不确定的依赖关系，如时间、存储、网络、设备传感器，都可以通过在应用程序/模块中可用的*环境*结构或*服务定位器*内部的具体实现来实例化。它可能看起来像这样:

```
void setupEnvironment() {
  serviceLocator.registerFactory<IDateManager>(() => ConcreteDateManager());
}
```

现在，为了在 *DateService、*中使用它，我们可以使用服务定位器(抛弃抽象参数*管理器*):

```
class DateService {
  DateTime giveMeTheCurrentDate() {
    return serviceLocator<IDateManager>().retrieveDate();
  }
}
```

感谢你花时间阅读以上所有内容，我希望这个故事能帮助你提高一点点水平。此外，请不要犹豫将知识传播给其他开发人员。

[](https://medium.com/@catalin.patrascu/membership) [## 用我的推荐链接加入媒体- Catalin Patrascu

### 阅读 Catalin Patrascu 的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接…

medium.com](https://medium.com/@catalin.patrascu/membership)