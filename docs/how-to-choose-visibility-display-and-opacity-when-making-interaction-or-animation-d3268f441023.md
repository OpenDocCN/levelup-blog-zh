# 制作交互或动画时，如何选择可见性、显示和不透明度

> 原文：<https://levelup.gitconnected.com/how-to-choose-visibility-display-and-opacity-when-making-interaction-or-animation-d3268f441023>

## 例如，创建淡入/淡出效果

![](img/5e83adbd4700c237cfc6e39be55d66bf.png)

照片由[加勒特·巴特勒](https://unsplash.com/@glbutler17?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 web 前端开发中，我们可以使用`display`、`opacity`、`visibility`等 CSS 属性来显示和隐藏元素。

这些属性有什么不同？如果我们想在显示和隐藏时制作动画呢？本文将为您介绍。

我们先来看看他们的区别:

可以看出，两者还是有一些区别的，需要根据具体案例来选择属性。

比如一个鼠标悬停淡入淡出效果，我们可以不用`display`。因为它不是一个可动画的 CSS 属性。换句话说，它不能在给定的时间内改变。详情见[动画 CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)。

而`visibility`和`opactiy`都是可动画的 CSS 属性。先用`opactiy`吧:

看起来很棒。但是受限于不透明度，当这个元素被隐藏的时候，我们无法点击隐藏的元素。所以我们可以组合使用`visibility`。

点击上面例子中的重叠区域(第一次隐藏)看效果。查看代码，如果您注释掉代码中的`upper.style.visibility = "hidden";`并重试，您将发现绑定到底层元素的事件不会触发。

所以我们需要根据实际案例来选择这三个 CSS 属性。玩得开心！

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说很重要——谢谢。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)