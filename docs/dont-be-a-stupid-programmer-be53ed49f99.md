# 不要做一个愚蠢的程序员

> 原文：<https://levelup.gitconnected.com/dont-be-a-stupid-programmer-be53ed49f99>

## [思维程序员](https://medium.com/tag/thought-programmer)

## 我们应该避免的 6 种代码气味

*这些是原则，不是法律！*

![](img/35624e4cdbe3cf50a22535f31b2032a3.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

愚蠢是基于六种代码气味的首字母缩略词，我们在编写程序时应该避免这六种气味。

这就是我们的代码愚蠢的地方。

*   **英格尔顿模式**
*   **T** 右侧联轴器
*   不稳定性
*   P 剩余优化
*   I 非描述性命名
*   **D** 复制

> 代码味道指的是可能的迹象，表明我们的代码中有些地方不太对劲，我们可能不得不重构它。

# S 代表什么？一个

我相信你知道这个模式，因为它是一个简单而广泛的模式。它通过确保一个类只有一个实例，同时提供对该实例的全局访问点，使您的设计保持简单。

```
import java.io.Serializable;

public class SerializedSingleton implements Serializable{

    private static final long serialVersionUID = -7604766932017737115L;

    private SerializedSingleton(){}

    private static class SingletonHelper{
        private static final SerializedSingleton instance = new SerializedSingleton();
    }

    public static SerializedSingleton getInstance(){
        return SingletonHelper.instance;
    }

}public static void main(String[] args) {...
}
```

## 什么使它变坏了？

```
Stop using the singleton pattern from now
```

这就是为什么我们不应该使用这种模式

*   我们不确定将来是否会需要任何额外的东西，但是需求总是会改变，而 singleton 不会。
*   它通常将我们的对象实例暴露给应用程序的全局上下文，使它容易在任何时候被修改并失去控制。
*   进行单元测试可能是地狱，因为每个测试必须完全独立于前一个测试，这是不可能的，所以通过保持状态，应用程序变得难以测试。

## 如何避免？

我们应该将类的生命周期管理与类本身分开。

# **T 代表什么？T** 右联轴器

也称为强耦合。

```
Tight coupling is a generalization of the **singleton** problem.
```

耦合是衡量在一个模块中进行更改所需的其他模块中的更改的标准。它越大，代码的耦合性就越强。

紧密耦合的模块很难使用，更改成本很高，也很难测试。基本上，您应该减少模块之间的耦合。

# **U 代表什么？不稳定性**

```
Most of the time, untestability is caused by **tight coupling**.
```

一般来说，紧密耦合以及隐藏的依赖使得测试模块更加困难和昂贵。

每当我们`don’t unit test`我们的代码是因为`there is no time`，真正的原因是我们的代码是`bad`。

# P 代表什么？剩余优化

根据高德纳的说法:

```
Premature optimization is the root of all evil. Only costs alone, and no good.
```

优化的系统比仅仅写一个循环或者用前增量代替后增量要复杂得多。你最终会得到不可读的代码。这就是系统优化更加困难的原因。

优化应用程序有两个规则:

*   不要这样做
*   暂时不要对此进行优化

# 我是为了什么？I 非描述性命名

我们应该使用有意义的名字来命名变量、类、方法。等等。

```
Don’t write code that only you can understand.
```

既然我们是给人写代码，而不是给机器写代码(*计算机只理解 0 和 1* )。

*   适当地命名你的类、方法、属性和变量
*   不要缩写它们，永远不要！

```
// you shouldn't
const n = 3.14;
const r = 5const a = r * n * n // you should
const piNumber = 3.14;
const radius = 5;const areaOfCiricle = radius * piNumber * piNumber
```

请记住，我们总是让我们的程序对其他人可读。

# **D 代表什么？D** 复制

程序员很懒:

```
Be lazy the right way — write code only once!
```

重复的代码是不好的，所以请*不要重复自己*，还有*保持简单，笨蛋*。

*   “*不要重复自己*原则背后的想法是重用你以前写的代码，而不是重复它。
*   “*保持简单，愚蠢的*”原则指出，如果事情保持简单而不是复杂，它们会更好地发挥作用。

# 结论

今天，我试图简单明了地解释你在编写代码时应该避免的原则。愚蠢的方法导致难以维护和难以测试的代码设计。在实践中，为了不成为一个愚蠢的程序员，你可以应用 SOLID⁴原则。

*   ***单一责任原则*** :一个类的改变不应该有一个以上的原因。
*   ***开闭原则*** :软件实体对于扩展应该是开放的，对于修改应该是封闭的。
*   ***利斯科夫替换原则*** :程序中的对象应该可以用它们的子类型的实例替换，而不改变程序的正确性。
*   ***接口分离原则*** :多个客户端专用接口比一个通用接口要好。
*   ***依赖倒置原则*** :高层模块不应该依赖低层模块。两者都应该依赖于抽象。

我之前的一篇文章《[重新思考坚实的原则](https://javascript.plainenglish.io/rethinking-solid-principles-in-javascript-7effdd4dc37d)》可以帮助你在编程中获得更多新的思路。

很简单，对吧？

# 参考

[1][https://en.wikipedia.org/wiki/Singleton_pattern](https://en.wikipedia.org/wiki/Singleton_pattern)

[https://martinfowler.com/ieeeSoftware/coupling.pdf](https://martinfowler.com/ieeeSoftware/coupling.pdf)

[3][http://wiki.c2.com/?PrematureOptimization](http://wiki.c2.com/?PrematureOptimization)

[4][https://JavaScript . plain English . io/refreshing-solid-principles-in-JavaScript-7 eff DD 4d c37 d](https://javascript.plainenglish.io/rethinking-solid-principles-in-javascript-7effdd4dc37d)