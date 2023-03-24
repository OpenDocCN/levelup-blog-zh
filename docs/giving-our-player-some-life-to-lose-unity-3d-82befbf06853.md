# 给我们的玩家一些生命…去失去！(Unity 3D)

> 原文：<https://levelup.gitconnected.com/giving-our-player-some-life-to-lose-unity-3d-82befbf06853>

## //在此插入邪笑

![](img/87d39594960eccf53f2ba6102f7c0b1f.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

生活是视频游戏的经典元素之一，但是实现起来有多难呢？今天我们来看看这有多简单。

我们需要的第一件事是一个容器来容纳我们的玩家拥有的生命数量。我们可以通过玩家脚本中的一个变量来实现这一点。

```
[SerializeField] private int _lives;
```

我们使用私有变量是因为我们不想让游戏中的其他脚本直接修改生命变量。相反，我们将使用在需要改变时间接更新生命变量的方法。

我们将使用两种方法 **AddLife()** 和 **RemoveLife()。**首先我们来看看 AddLife():

```
private void AddLife()
{
    _lives++;
}
```

然后删除 Life()

```
private void RemoveLife()
{
    _lives--;
}
```

现在你可能会注意到这两个方法都是私有的。那么其他剧本将如何影响生活呢？这就是设计发挥作用的地方。我的设计如下:

**AddLife()** -只有当玩家在游戏中收集一个物体时才会被调用，想想超级马里奥的绿蘑菇吧。当该对象被收集时，它调用 AddLife()。

**RemoveLife()-** 这个方法只能从游戏中另一个名为 **DamagePlayer(int dmgAmount)的公共方法调用。在破坏方法中，我会根据我制作的游戏做不同的事情。这真的取决于我的球员有健康和生命，或者只是有生命。如果我的玩家只有生命，那么每个伤害源调用带有 1 点伤害的伤害方法。如果玩家有健康，那么伤害的数量会有所不同，瞬间死亡会造成玩家不应该受到的巨大伤害。损害方法检查玩家的健康等于零，如果等于零，则调用 RemoveLife()。**

因此，有了防止外部访问的生命变量和一些方法，我们现在有了一个拥有生命的播放器。那现在怎么办？

## 玩家反馈

如果玩家有生命，我们需要给他们关于他们当前生命数量的反馈。我们通过生命计数器的 UI 文本元素来实现这一点。我们的 UI 脚本有一个名为**UpdateLivesUI(int current lives)**的公共方法，它将更新屏幕显示。这个方法在 **AddLives()** 和 **RemoveLives()** 中以完全相同的方式被调用，我的使用 UIManager 的一个实例，但是如果你更喜欢使用 GetComponent < >也可以做同样的事情:

```
UIManager.Instance.UpdateLivesUI(_lives);
```

就是这样！差不多吧。你需要添加一些逻辑来处理玩家生命的耗尽，然后你就有了一个功能齐全的生命系统。

久而久之，像生命这样的东西变得越来越不常见，因为开发者在寻找新的方式与玩家互动。作为游戏的支柱之一，把它放在你的后口袋里是件好事。