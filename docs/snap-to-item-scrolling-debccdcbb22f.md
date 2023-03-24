# 在 SwiftUI 中捕捉到项目滚动

> 原文：<https://levelup.gitconnected.com/snap-to-item-scrolling-debccdcbb22f>

## 使用此自定义 ViewModifier 技术在 SwiftUI 中实现“捕捉到项目”滚动。这个快速提示展示了如何为水平堆栈实现它。

![](img/5c7800151e0d0afd6383cafe58ae041b.png)

SwiftUI 最大的缺点之一就是，当我滚动到`ScrollView`时，无法捕捉到视图。下面是我使用的一种技术，通过自定义的`ViewModifier`在我的`HStack`上实现了捕捉。

> 在开始之前，请考虑使用此[链接](https://trailingclosure.com/signup/?utm_source=trailing_closure&utm_medium=blog_post&utm_campaign=snap_scrolling)订阅，如果您没有在[TrailingClosure.com](https://trailingclosure.com/?utm_source=trailing_closure&utm_medium=blog_post&utm_campaign=snap_scrolling)上阅读此文，请找时间来查看！

## 概观

这种技术不是使用`ScrollView`，而是使用具有动态变化的 x 偏移的`HStack`来模拟滚动。要更改偏移，我使用`DragGesture`计算滚动距离。你会在下面看到里面的`DragGesture` `onChanged()`闭包那我更新一下`dragOffset`。这使得`HStack`在用户拖过屏幕时呈现为滚动。最后，当调用`DragGesture`函数`onEnded()`时，我计算哪个视图项最接近，然后应用最终偏移。这将创建“捕捉”效果。

一个潜在的缺点是，创建`ViewModifier`时必须提供视图项的宽度和间距。这意味着具有动态变化宽度的视图不能使用此技术。`ViewModifier`必须知道宽度，才能计算视图项目的正确位置。

# 使用示例

给定一系列颜色，你可以看到这个自定义`ViewModifier`如何应用于`HStack`。

## 让我们看看你的成就吧！

我们想看看你用这个教程做了什么！把照片发给我们！可以在 Twitter[@ trailing closure](https://twitter.com/TrailingClosure)、Instagram 上找到我们，或者发电子邮件到howdy@TrailingClosure.com联系我们。