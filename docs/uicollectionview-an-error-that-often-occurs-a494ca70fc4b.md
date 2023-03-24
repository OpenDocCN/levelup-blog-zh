# UICollectionView:经常发生的错误

> 原文：<https://levelup.gitconnected.com/uicollectionview-an-error-that-often-occurs-a494ca70fc4b>

![](img/40c4525ba9d6f5ed693a87601f6f399b.png)

妮可·沃尔夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

嗨，继上一个故事[ [*让 Swift 更简洁的 12 个小技巧*](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063) ]之后，我又来了。这次我要告诉你我遇到的一个错误。

在我的日常开发中，很少用到 **UICollectionView** 。再用的时候经常 Google 一些如何使用 **UICollectionView** 的文章。即便如此，我还是像往常一样遇到一个错误。下面，我详细阐述这个过程。

**答:我做了以下编码**

*   初始化集合视图
*   为 CollectionView 设置委托和数据源
*   实现数据源的方法

*特别是有这样一个代码:*

```
let layout = UICollectionViewLayout()
let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
```

**B .观察到的现象**

以下方法可以正常回调:

```
func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int
```

以下方法无法正常回调:

```
func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell
```

**C .原因:**

我错误地使用 **UICollectionViewLayout** 来实例化，我应该使用它的子类，比如:**UICollectionViewFlowLayout**

```
let layout = UICollectionViewFlowLayout()
let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
```

这个错误虽然简单，但是浪费了我太多的时间去思考到底哪里错了。

希望各位开发者能够避免。

# 如果你喜欢，请跟我来。让我们在接下来的帖子中见面。

你可能也会感兴趣:

*   [*IOS 还是 IOS？*](https://medium.com/@zhuyp/ios-or-ios-7c7d23905c35)
*   [*让 Swift 更简洁的 12 个技巧*](https://medium.com/@zhuyp/12-tips-to-make-swift-more-concise-4f4ed63f3063)

> ***关于我:*** *我是一名 iOS 的开发者，曾经为 XAG 工作。现在我想在 medium 上分享故事，总结自己的经验来帮助别人。如果你有任何问题，请给我反馈，我会积极给你我的答复。目前，我的期望是获得 100 个关注者。需要你的善意支持，谢谢。*