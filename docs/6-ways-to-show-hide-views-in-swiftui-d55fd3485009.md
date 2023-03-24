# 在 SwiftUI 中显示和隐藏视图的 6 种方式

> 原文：<https://levelup.gitconnected.com/6-ways-to-show-hide-views-in-swiftui-d55fd3485009>

## 探索在 SwiftUI 中显示和隐藏视图的不同方法

![](img/b6aad4550177b20961919e155091836d.png)

保罗·斯科鲁普斯卡斯在 [Unsplash](https://unsplash.com/s/photos/view?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

SwiftUI 是一个关于视图或窗口的框架，这是过去的叫法。想要显示的视图和想要隐藏的视图。SwiftUI 中的编码与 UIKit 非常不同，因为它使用声明性语法，关注逻辑而不是控制流。因此，对于一个经验丰富的面向对象编码人员来说，理解如何显示和隐藏视图有时并不明显，而我们大多数人都是这样。让我们来看看六种不同的方法。

在我开始之前，这里有一个运算符的快速提示，它将在讨论中多次出现，这个卑鄙的运算符称为三元运算符。一个我永远记不住哪边是真哪边是假的算子。

```
struct ContentView: View {
  var body: some View {
  let number = 2
  let result = ((number % 2) == 0) ? "Even" : "Odd"
  Text(result)
  }
}
```

第四行是三元运算符，带问号的那一行。这一行表示，如果该语句为真，则打印偶数，如果为假，则打印奇数。这很重要，记住。

邦，回到磨刀石上——回到简报上。我想看看您可以在 SwiftUI2.0 中显示和隐藏视图的不同方式。我确信这个列表并不详尽，也没有按照偏好的顺序列出。我只是从简单/显而易见的选项开始，慢慢地但肯定会变得不那么简单。

去编码。在代码中使用简单的 if/else 来显示和隐藏视图的最简单方法。

一段代码看起来像这样。但是，好吧，这不是使用三元运算符，这是。我们可以用这段代码来实现。

但是等等——尽管这两种视图交替出现，但是有一个重要的区别。第二个版本在屏幕上为两个单词都保留了空间。改变不透明度确实会改变这一点。所以在第一个选项中，单词 true 与单词 false 在屏幕上的位置发生了变化。在第二个选项中，true 出现在 false 之上的不同地方。也就是说，如果您将两个 Text()视图修饰符放在 ZStack 之间，您将获得相同的效果。因此代码的行为是相同的。

继续，hide()视图修改器怎么样。这是我在上找到的一个扩展，它构建了一个视图修改器来使用它。

```
**extension** View {
  @ViewBuilder **func** hidden(_ shouldHide: Bool) -> **some** View {
    **switch** shouldHide {
      **case** true: self.hidden()
      **case** false: self
    }
  }
}
```

有趣的是，这似乎与不透明度视图修改器有相同的效果。所以如果你不使用这里显示的 ZStack，你会在不同的地方得到 true 和 false。

接下来，使用这样的 case 语句怎么样？比 and if/else 选项更有潜力，它可以迅速消失在一个分支丰富的兔子洞里，完全失去读者。

但是我们开始使用的那个干净的三元操作符呢？我认为它不经常被使用，因为有一个陷阱能抓住程序员。我提到的问题是，需要确保返回的两个选项属于相同的类型。如果没有，您会得到这样的错误消息。

![](img/a0adde38aa43db1b4c1ebe4d32c41075.png)

我们可以使用你在这里看到的任何视图，尽管我被告知这样做并不理想。我怀疑这并不理想，因为最终你可能能够编译它，但是当你运行它的时候我会崩溃。

当然，为了保持这种美好和干净，我把真实和虚假移到了他们自己的观点中。

最后，用视图构建器再试一次怎么样。我不禁觉得这个选择有很大的潜力。

所有这些让我想到这篇短文的结尾。我希望你喜欢读它，就像我喜欢写它一样。如果你在这个问题上寻找更多相同的东西，试着看看我关于 Segue 恶作剧的论文或者使用 SwiftUI 中的 Delegation to Segue。请关注 medium.com，我在 medium.com 发表了一百多篇关于 2020 年编码的文章。

[](https://medium.com/better-programming/segue-shenanigans-with-swiftui-237f73370f51) [## SwiftUI 的 Segue 恶作剧

### 使用 SwiftUI 和 Combine 更改视图的不同方式

medium.com](https://medium.com/better-programming/segue-shenanigans-with-swiftui-237f73370f51) 

保持冷静，继续编码。