# 改进 Swift 编码的 6 个技巧

> 原文：<https://levelup.gitconnected.com/6-tips-to-improve-coding-of-swift-1498bcc45f63>

> 在我之前的文章中，我解释了如何使 Swift 代码更少:
> 
> [***十二招使迅捷少码***](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063)

**这篇文章简单地谈了一些关于代码质量的技巧。**

![](img/6293415a03c93b4e6e2d5e8e2e08110a.png)

照片由[蒂莫西·戴克斯](https://unsplash.com/@timothycdykes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

1.  **尽量使用值类型。**

> 值类型比类轻。

❌：

✅：

**2。使用“！”越少越好，而且只用“！”在一些地方法规中。**

> 这确保了更少的崩溃发生。

❌:

✅：

**3。使用确定性类型，减少 Any/AnyObject 的使用。**

> 不明确的类型总是错误的来源。

❌:

✅：

**4。使用确定性类型，并使用自定义类型来替换字典。**

> 字典的键值具有类型不确定性。

❌:

✅：

**5。使用枚举和常量代替硬编码。**

> 硬编码不好控制，修改的时候总会被泄露。

❌:

✅：

6。使用“KeyPath”而不是硬编码字符串。

> 字符串很容易写错，用编译器检查更可靠

❌:

✅：

希望这篇文章能帮到你。另外，你还有什么问题吗？欢迎给我留言。

# 请跟我来。让我们在接下来的帖子中见面。

以前的故事:

1.  [**Swift 提示:功能**](https://medium.com/@zhuyp/tip-for-swift-from-async-to-sync-in-function-1-c1337f0d60b3) 中从异步到同步
2.  [**UICollectionView:经常出现的错误**](https://medium.com/@zhuyp/uicollectionview-an-error-that-often-occurs-a494ca70fc4b)
3.  [**12 个小技巧让迅捷少码**](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063)
4.  [**IOS 还是 IOS？**](https://medium.com/@zhuyp/ios-or-ios-7c7d23905c35)
5.  [**App 中的简单错误系统**](https://medium.com/@zhuyp/simple-error-system-in-app-f72278168634)

> ***关于我:*** *我是一名 iOS 的开发者，曾经为 XAG 工作。现在我想在 medium 上分享故事，总结自己的经验来帮助别人。如果你有任何问题，请给我反馈，我会积极给你我的答复。*