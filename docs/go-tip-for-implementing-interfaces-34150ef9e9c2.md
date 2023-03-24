# Go:实现接口的技巧

> 原文：<https://levelup.gitconnected.com/go-tip-for-implementing-interfaces-34150ef9e9c2>

![](img/461f3e0cc78c041ac22eaf9792817da9.png)

如果你曾经使用过支持*接口*概念的其他语言，你会注意到 Go 的方法的一个特点是它不能帮助你做对，它会因为你做错而给你一巴掌。

假设您已经声明了一个类型，一部分 UUIDs，如下所示:

```
type UUIDs []uuid.UUID
```

你会想要排序这些，所以你想使用*排序。排序()。*你需要类型*uuid*来实现*排序。然后接口*。幸运的是，你已经记住了所有的接口和方法签名，对吗？我不知道。将帮助您实现该类型的接口。不主动，当你试图使用它时，它只会扇你一巴掌，例如:

```
var uuids = UUIDs{uuid.New(), uuid.New, uuid.New()}
sort.Sort(uuids)
```

在这里，您将得到一系列错误的详细说明，在某种程度上，您不能只是剪切和粘贴，缺少的方法。太少，太晚。相反，如果你想在类型声明的地方强制接口，因为那是你实现方法的地方，这里有一个简单的技巧:

```
type UUIDs []uuid.UUID// Implements sort.Interface
var _ sort.Interface = (*UUIDs)(nil)
```

这是一种无害的方法，可以使错误立即发生在类型声明中您想要的地方。有了一个像样的 IDE，你甚至可以得到一个提议，让*实现缺失的方法*，这将为你生成存根。

比在代码中等待在其他地方被它绊倒要好得多。