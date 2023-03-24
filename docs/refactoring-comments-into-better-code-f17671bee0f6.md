# 将注释重构为更好的代码

> 原文：<https://levelup.gitconnected.com/refactoring-comments-into-better-code-f17671bee0f6>

*function(commented code){ return better code；}*

![](img/a97e8855f8c30b9c395652a981bef0b5.png)

照片由 [thr3 眼睛](https://unsplash.com/photos/MlpyeuKs8GY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在我的上一篇文章中，我提到了仔细注释代码的重要性。注释一点也不邪恶，但通常它们可以被重写为更干净的代码。

# [检出 git connected>](https://gitconnected.com)

*开发者和软件工程师社区*

我仍然相信稀疏评论是好评论。过多的评论是无用的噪音。我最近偶然发现了这段代码，它是注释可能产生噪音的一个很好的例子。

在第 13 行，用一个注释声明了下一个 looong 行代码将“检查我们是否正在覆盖一个现有的函数”。这违反了长期坚持的 DRY 原则，因为作者必须在代码和注释中重复他们正在做的事情。如果有人*更新了一个而没有更新另一个，那么这个评论可能是错误的。因为第 14 行的精神负担是如此的痛苦，所以作者试图帮助将来的可读性。但是如果我们简单地从函数 methodAlreadyExists(name，prop)返回第 14 行，在这个函数的上下文中 13 和 14 就变成了*

> if (methodAlreadyExists(name，prop)) { }

混乱被剥离，自我重复被压制，我们的代码读起来更像散文。令人愉快！

![](img/2db0315ef9fec5264c80f756afab663d.png)

**令人愉快！**艾伦·金在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

因此，通过一些反思和有见地的反馈，我想赞扬注释代码的精神。每当你想评论一段代码时，首先，我感谢你！你应该表扬一下自己！你在为未来的你和所有其他读者以及你的代码修补者着想。但是接下来问问自己，有没有办法只用代码解决这个问题。有时候答案会是否定的，在这一点上，请写评论！简洁准确，让每个人免于重新理解深奥难懂的代码的负担。注释代码比重构代码更容易获得尽可能干净的代码。重构可能是艰苦的工作，迭代需要时间。我们都为同一个目标而努力:用简单的英语交流那些用代码阅读起来很困难或者很费时间的东西。评论容易腐烂和误导，往往会违背[干原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。代码是媒介。我们越能在代码中更好地表达我们的想法，我们就越不需要注释来解释事情。

评论并不邪恶。评论仅仅是开发者可以用来行善*或作恶*的工具之一。我的意图是警告不必要的，更重要的是*过度*使用注释，通过指出我通过我的经验发现的过度使用的主要缺点——即过时的注释可能具有误导性，并且在开发和维护过程中经常被忽视——并建议简单的方法来克服它，我希望可以为我的编码同事和他们的团队节省一些我已经不必要地遭受的头痛。