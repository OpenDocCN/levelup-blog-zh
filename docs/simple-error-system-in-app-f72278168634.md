# 应用程序中的简单错误系统

> 原文：<https://levelup.gitconnected.com/simple-error-system-in-app-f72278168634>

嗨~我又来分享我的经验了。

> 我的一篇文章受到许多读者的欢迎，我想它可能对你有所帮助:
> 
> [**12 招让迅捷更少代码**](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063)

![](img/b042b692085e60d605dacdc076b603ea.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我在过去的工作中经常需要设计误差系统。比如一个 App 依赖多个第三方库，我们需要收集这些第三方库产生的错误信息，然后把错误信息向上传递。接下来，我将分享一个关于如何收集错误的想法。

我总是认为它可以重定向每个错误，然后给这些错误分配一个新的编号。虽然这个方法看起来很有逻辑，但是工作量很大。所以我现在提出的这个方法的核心思想就是**域**。

对，就是**对**不同帧的错误信息进行分类。

比如我们的 App 需要依赖 A.framework 和 B.framework，A.framework 会生成 *AError* (代码> 0 & &代码< 100)，B.framework 会生成 *BError* (代码> 0 & &代码< 100)。我将这样设计误差系统:

方法 *aError* 用于接收 *AError* ，方法 *bError* 适用于接收 *BError* 。域用于表示不同的帧，以便可以重用相同的错误代码。最后，*描述*可用于指示整个错误信息。

# 请跟我来。让我们在接下来的帖子中见面。

以前的故事:

1.  [**Swift 提示:功能**](https://medium.com/@zhuyp/tip-for-swift-from-async-to-sync-in-function-1-c1337f0d60b3) 中从异步到同步
2.  [**UICollectionView:经常出现的错误**](https://medium.com/@zhuyp/uicollectionview-an-error-that-often-occurs-a494ca70fc4b)
3.  [**12 个小技巧让迅捷少代码**](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063)
4.  [**IOS 还是 IOS？**](https://medium.com/@zhuyp/ios-or-ios-7c7d23905c35)

> ***关于我:*** *我是 iOS 的开发者，曾经在 XAG 工作。现在我想在 medium 上分享故事，总结自己的经验来帮助别人。如果你有任何问题，请给我反馈，我会积极给你我的答复。目前，我的期望是获得 100 名追随者。需要你的善意支持，谢谢。*