# 永远不要吞下异常

> 原文：<https://levelup.gitconnected.com/never-swallow-exceptions-5e01c6bb5b8a>

## 避免不必要的头痛——无论是象征性的还是字面上的

![](img/bedf17b6673787a996ecc4b886e9ec9c.png)

照片由 [Tim Gouw](https://unsplash.com/@punttim?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 我刚刚目睹了另一起犯罪。

> “一个人中枪了。
> 
> 他屏住呼吸，有足够的力气坐公交车。
> 
> 10 英里后，这个人下了公共汽车，走了几个街区，然后死了。”— [奥斯卡瑞兹](https://stackoverflow.com/questions/921471/why-do-java-people-frequently-consume-exceptions-silently/921583#921583)

警察碰巧发现了尸体，但不知道刚刚发生了什么。

上面描述了吞下一个异常是什么感觉。玩笑归玩笑，用`throw` it 代替`surrounding it with try-catch`*可能已经是常识了，但是如果不能呢？你会诉诸吞咽例外吗？事实证明你可以做得更好。*

# *当您不能抛出异常时——场景*

*对于检查异常，您可能会遇到不允许抛出它的情况。为了进行演示，请考虑以下场景:*

*上面的代码甚至无法编译。鉴于`Runnable`是一个预定义的界面，对它进行修改不在我们的控制之内。人们可能会尝试使用 IDE 提供的唯一建议，即`surround with try/catch`选项。不要这样做。除非你真的有理由这么做，或者你真的能从中恢复过来。否则，不要做。*

# *好的，坏的，和马车。*

*在进入正确的解决方案之前，我假设对于你们中的一些人来说，考虑到场景的简单性，这是显而易见的，让我们首先探索导致*吞咽异常的不同实现。**

*我们怎样才能做得更好？对于这个场景，一个好问题应该是:“我们真的需要读取`run()`方法中的文件吗？有没有可能在别的地方做？”。幸运的是，它是。*

*从这个解决方案中，我们可以看到我们现在是`throwing`这个例外。为了避免混淆，值得一提的是我们在这里使用的`try`是一个`try-with-resources`。我们在这里使用它的唯一原因是确保资源在使用后被关闭。*

# *结论*

*回到我们之前的故事，如果有人没有吞下这个异常，场景可能是:*

> *一名男子被枪击中，当场死亡，尸体就躺在刚刚发生谋杀的地方 [OscarRyz](https://stackoverflow.com/questions/921471/why-do-java-people-frequently-consume-exceptions-silently/921583#921583)*

**当警察到达时，所有的证据都已就位。**