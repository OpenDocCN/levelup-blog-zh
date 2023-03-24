# 去恐慌和恢复:满足边缘情况

> 原文：<https://levelup.gitconnected.com/go-panic-and-recovery-meet-the-edge-case-71054747a5da>

![](img/b63d794bc18f40b98d263fcd5a68aa2b.png)

Golang 标志

惯用的 Go 通常使用以下模式处理错误情况:

但是还有一种更极端的错误反应，那就是[](https://gobyexample.com/panic)*。使用 *panic()* 函数通常表示无法恢复的意外错误。调用 *panic* 用一个非零值和一个错误信息退出你的程序。**嘭！**有办法让*从*恐慌中恢复*，*不过。如果你不能想象为什么你想要从恐慌中恢复过来，考虑一下 Go 在算术上被零除的恐慌，那么简单的数学会意外地导致恐慌！*

*好吧，那么我们如何从恐慌中恢复过来呢？这里有一个函数:*

*现在你渐渐发现，在一些不可避免的场景中，*这里的魔法*被零除，恐慌了。你想要的是它返回一个普通的错误，而不是惊慌失措。有一个解决方案:*

*您可以在*延迟*函数中使用 *recover()* 来从死机中恢复。这几乎就是我们想要的，如果在*计算某些东西*而不是退出的过程中出现死机，它会恢复并设置`err`为死机消息。*

## *遇到边缘情况*

```
*panic(nil)*
```

*当用参数`nil`调用 panic 时会发生什么？荒谬但有可能。由于 *recover* 返回传递给 *panic* 的参数，我们第 5 到 7 行的逻辑有问题:*

```
*if r := recover(); r != nil {
   err = fmt.Errorf("panic: %+v", r)
}*
```

*值或`r`将是`nil`，因此`err`不会被设置。好吧，那么:*

```
*r := recover()
err = fmt.Errorf("panic: %+v", r)*
```

*失败，现在`err`被设置 ***每*** 调用一次就算健康完成。*

## *解决方案*

*我不知道一个真正优雅的解决方案。你绝对需要调用*恢复*来短路*死机*，**，但是**如何使用它的返回值可能视情况而定。*

*其中*粗*的解决方案是:*

*如果您没有到达`done = true,`，这将恢复并设置`err`。它并不优雅，但很有效。*

# *分级编码*

*感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)*