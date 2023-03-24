# 方法、算法和泛型

> 原文：<https://levelup.gitconnected.com/methods-algorithms-and-generics-a02dbfa3396b>

## 将方法重新定义为泛型扩展

![](img/91e6af87a27827c87f2e23c6eeb76df8.png)

照片由[法鲁尔·拉齐](https://unsplash.com/@mfrazi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我发表了一系列关于构建绘画应用程序的文章，其中的最后一篇文章可以在这里找到。

[](https://betterprogramming.pub/embracing-algorithms-in-your-swiftui-painting-app-652f8b8cd604) [## 在 SwiftUI 绘画应用中采用算法

### 完成构建我们的 iOS paint 应用程序的旅程

better 编程. pub](https://betterprogramming.pub/embracing-algorithms-in-your-swiftui-painting-app-652f8b8cd604) 

但是我一直在想这个系列最后一篇论文的主题，拥抱算法。我用肖恩·帕伦特在他关于使用 STK 算法的 C++调味演讲中提倡的原则重新定义了大多数方法；但是我能做更多吗？许多上述方法有一个共同的主题，它们都改变了表示形状的结构上的一个或多个属性。我能开发一个通用版本，有效地减少我的代码库吗？我可以在其他地方使用的代码。

## 编码

这是我第一次尝试去替换一个在画布上取消选择每一个形状的盲循环。它工作得很好，但是非常具体，使用了三个函数——枚举、过滤和映射。我最终在许多方法中使用了这个组合，我能做得更好吗。

这是第二个解决方案，虽然它有更多的代码行，但我认为它更好，只使用了一个函数和一个片段——尽管我不得不承认这里有一个循环，说实话——有点难看，不是吗？

所以我用递归替换了这个循环——一个代码行更少的解决方案——使用一个函数和片。是的，我知道递归使用堆栈，这可能会溢出——但嘿，现在是 2021 年，拥有干净的代码在这里肯定是优先的。

当然，赋值和比较我可以一气呵成。所以我这么做了，让它少了一行。

我没有比这更好的了——或者说我做到了。你看，这仍然是一段非常特殊的代码，它将我的 Shapes 结构上的 selected 属性设置为 false —我想做更多，我需要更通用的东西。所以在等式上加了一个句号。你可以在这里阅读我写的一篇关于闭包的文章。

[](/4-custom-closures-syntax-and-semantics-illustrated-using-swiftui-c48db97e4210) [## 使用 SwiftUI 展示了 4 个自定义闭包、语法和语义

### 在 Swift 中处理延迟

levelup.gitconnected.com](/4-custom-closures-syntax-and-semantics-illustrated-using-swiftui-c48db97e4210) 

要调用 deselectEA5，您现在需要在 ContentView.swift 中使用类似这样的代码。现在，这要灵活得多，因为没有什么可以阻止我向那个 trailing 方法添加额外的设置。仅用这个方法，我就可以重写几乎所有其他的方法来改变我的形状的属性。方法都有相同的核心代码。

```
cords.deselectEA5(predecessor: 0) { feed in
  cords.objects[feed].selected = false
}
```

虽然我想了更多，但我决定如果我把它重建成一个扩展，然后可以添加到各种各样的库中，它可能会更有用。

但是我无意中发现了一个问题。因为 insideMethod 不会在主线程上执行，除非您明确告诉编译器这样做。

```
cords.objects.deselectEA6(0) { feed in
  DispatchQueue.main.async {
    cords.objects[feed].selected = false
  }
}
```

我已经从绘画的角度完成了，但是我还没有完成。我能实现一个更通用的解决方案吗？如果我可以发送一个谓词作为参数，那肯定会更有用，即使它是“选择形状，选择=真”。所以我试了一下。

这当然需要一个令人难以置信的电话，但它是超级灵活的。现在我有了一个扩展，可以在其上添加任何谓词，后跟对 Shapes 结构的任何操作。

```
cords.objects.deselectEA7(elementsSatisfying:\.selected, predecessor: cords.objects.startIndex, insideMethod: { feed in
  DispatchQueue.main.async {
    cords.objects[feed].selected = false
  }
})
```

所以，到了最后的修订版，事实上我根本不应该称之为取消选择 8，因为它与形状没有任何联系。这是一个通用扩展，您可以在任何可变集合上使用它来选择所述集合的成员，并在所选成员上执行任意一段代码。

你需要用这个代码来调用它。

```
cords.objects.deselectEA8(elementsSatisfying:\.selected, successor: cords.objects.startIndex, insideMethod: { feed in
  DispatchQueue.main.async {
    cords.objects[feed].selected = false
  }
})
```

这差不多把我带到了结尾…因为在看了 [Wayne 的](https://www.youtube.com/watch?v=PcViAYLPWCQ)视频后，我承认递归可能不是最好的前进方式，所以必须在列表中再添加一个，一个在循环中运行的通用例程。

用这段代码调用的方法。

```
cords.objects.deselectEA9(elementsSatisfying:\.selected, insideMethod: { feed in
  DispatchQueue.main.async {
    cords.objects[feed].selected = false
  }
})
```

所有这些促使我结束这篇论文。我希望你喜欢读它，就像我喜欢写它一样。如果你喜欢这篇文章/觉得它有用，请给它几个掌声让我知道，更好的是多读几篇我在过去 18 个月里张贴到 medium.com 的 170 多篇论文，并给其中一些投上你的票。