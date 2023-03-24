# 提高 UI 流畅性的 2 个技巧

> 原文：<https://levelup.gitconnected.com/2-tips-to-improve-ui-fluency-2c6aee86c457>

初学者必看的景点

![](img/7be1e975b26bfe3d3549bd6e8361b4bb.png)

照片由[米勒·塞甘·✳️✳️✳️](https://unsplash.com/@emileseguin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

1.  耗时的任务在子线程中执行

可以在子线程上执行网络请求、数据处理等任务，结果出来后再通过主线程刷新 UI 界面。避免在主线程上进行过多的 UI 无关的处理，以确保应用程序能够及时响应交互。

2.让 UITableViewCell 重用

重用机制可以有效地兼顾性能和效果，我们只需要使用 iOS 已经设计好的规则。

# 请跟我来。让我们在接下来的帖子中见面。

在我之前的故事中，我介绍了一些 swift 技巧和代码简单性的建议:

1.  [**迅捷:让代码更迅捷的 6 个小技巧**](/swift-6-tips-to-make-code-more-swift-a8ff9f8fd919?source=your_stories_page-------------------------------------)
2.  [**提高 Swift 编码的 6 个技巧**](/6-tips-to-improve-coding-of-swift-1498bcc45f63)
3.  [**12 个小技巧让 Swift 少了代码**](/12-tips-to-make-swift-more-concise-4f4ed63f3063)
4.  [**Swift:提高代码质量的几个技巧**](/swift-a-few-tips-for-improving-code-quality-ae39c1220c9)

其他故事:

1.  [**IOS 还是 IOS？**](https://medium.com/@zhuyp/ios-or-ios-7c7d23905c35)
2.  [**Swift 提示:功能**](https://medium.com/@zhuyp/tip-for-swift-from-async-to-sync-in-function-1-c1337f0d60b3) 中从异步到同步
3.  [**UICollectionView:经常出现的错误**](https://medium.com/@zhuyp/uicollectionview-an-error-that-often-occurs-a494ca70fc4b)
4.  [**App 中的简单错误系统**](https://medium.com/@zhuyp/simple-error-system-in-app-f72278168634)

> ***关于我:*** *我是一名 iOS 的开发者，曾经为 XAG 工作。现在我想在 medium 上分享故事，总结自己的经验来帮助别人。如果你有任何问题，请给我反馈，我会积极给你我的答复。*