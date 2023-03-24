# 如何用 CSS 制作围棋棋盘

> 原文：<https://levelup.gitconnected.com/how-to-make-a-go-board-with-css-ac4cba7d0b72>

CSS 网格、线性梯度、核形和围棋历史中的一个瞬间。

![](img/bc965342b75bce1ce0b67d94442fe96a.png)

[Hassan Pasha](https://unsplash.com/@hpzworkz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近看了女王的棋后，我有了写围棋的灵感。学习一个五百年前的国际象棋开局是很诱人的。现代计算机可以在几分之一秒内模拟数百万种游戏模式。但它永远不会知道学习一步棋背后的名字和历史的快乐。

这篇文章将用 HTML 和 CSS 展示吴清源和霍尼波·舒赛·梅津比赛中著名的开场片段。

1933 年 10 月 16 日，18 岁的中国神童吴清源迎战日本自 1612 年以来最著名的围棋机构——霍宁博棋院的第 21 任掌门人霍宁博·舒赛·梅津。当时它被称为世纪游戏，因为它是在两人各自祖国紧张局势加剧的背景下一代人战争的缩影。

# 游戏板

围棋棋盘由 19×19 的线网格组成。围棋子放在线的交叉点上，包括边和角。在 CSS 中构建这个板的最简单的方法是使用网格布局。

```
.grid {
  width: 720px;
  height: 720px;
  display: grid;
  grid-template-columns: repeat(36, calc(720px / 36));
  grid-template-rows: repeat(36, calc(720px / 36));
}
```

您可以使用`grid-template-columns`和`grid-template-rows`属性定义网格中的列和行。当您想要多次指定相同的轨道尺寸时，`repeat`功能非常方便。

该代码示例中的另一个细节是使用`calc`动态计算值。您可以将`720px`提取到一个常量中，以便以后更改电路板尺寸。或者，您可以使用特殊单位`fr`，它代表可用空间的一部分，代替`calc`功能。

```
.lines {
  border-right: 1px solid grey;
  border-bottom: 1px solid grey;
  background-size: calc(720px / 18) calc(720px / 18);
  background-image:
    linear-gradient(to right, grey 1px, transparent 1px),
    linear-gradient(to bottom, grey 1px, transparent 1px);
}
```

可以用 CSS 属性`linear-gradient`和`border`画线。请注意，我正在 36×36 的网格上绘制 19×19 的线条。这是因为我们必须在十字路口放置标记和石头。

围棋棋盘有九个均匀分布的标记。每个标记称为一颗星。下面的例子演示了如何用一组坐标在网格上叠加一个元素。

```
.star {
  grid-column-start: 6;
  grid-column-end: 8;
  grid-row-start: 6;
  grid-row-end: 8;
}
```

运行下面的代码来查看一个空 Go 板的完整示例。

# 游戏石

围棋子是黑色或白色的圆形物体。为了玩这个游戏，玩家将轮流把石头放在棋盘上。你可以使用基本的阴影和渐变来设计石头。

```
.stone {
  background: linear-gradient(145deg, #f6cfa6, #000000);
  box-shadow:
    2px 2px 4px 0 rgba(0, 0, 0, 0.25),
    -2px -2px 4px 0 rgba(255, 255, 255, 0.25);
}
```

我从趋势神经形态设计中学会了阴影技术。在 neumorphism 中，元素两端有两个阴影，代表光线，元素表面有一个渐变以匹配光线角度。

下面是黑白宝石的工作示例。

# 开场

在 1933 年的比赛中，吴清源首先拿下两个角球，然后拿下中心点。一个颠覆性的开场和对当权派的反抗。

拥有世袭头衔 Honinbo 和日本最强选手 Meijin 的 Shusai 在整场比赛中多次被推到失败的边缘。然而，舒赛每次都能够回来，滥用他的特权，暂停比赛，回到他的家里与他的学生讨论。

这场比赛持续了三个半月，以霍尼波家族的胜利而告终。尽管失败了，吴清源的地位还是扶摇直上，并因他的新开放理论而闻名。

五年后，吴清源的朋友 Kitani Minoru 在一场耗时近六个月的比赛中击败了 Shusai Honinbo。日本小说家、诺贝尔奖获得者川端康成的半小说《围棋大师》记录了这场比赛的过程。

## 参考

*   [CSS 技巧网格](https://css-tricks.com/snippets/css/complete-guide-grid/)完全指南
*   [神经变体和 CSS](https://css-tricks.com/neumorphism-and-css/) 作者阿德里安·贝斯
*   阿列克谢·乌索尔采夫的木纹