# 意大利面条代码的 3 个组织技巧

> 原文：<https://levelup.gitconnected.com/the-life-changing-magic-of-tidying-up-your-spaghetti-code-4e18a2eef86>

整理遗留代码的改变人生的魔力。

![](img/39b9d3063d9b560c5d782188eeeeae68.png)

由[希拉·乔伊](https://unsplash.com/@sheilajoy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你家里有垃圾抽屉吗？你知道，就是你存放各种各样偶尔有用的东西的那个。当你打开抽屉时，你会看到多套剪刀、安全别针、图钉、橡皮筋、收据、备用零钱和电池，以防停电。

> 如果你没有条理，你的代码库中可能也有一个垃圾抽屉。

它从几个一次性的、未分类的功能开始。您不确定这些函数应该放在哪里，所以您将它们放在一个名为 *utils* 或 *helpers* 的文件夹中。您永远无法在这些文件夹中找到您需要的东西，因此您最终会一遍又一遍地实现相同的功能。最终，你的垃圾文件夹变成了一团乱麻，接管了你的整个项目。

不要害怕——玛丽·近藤在这里用她积极的态度和[组织技巧](https://konmari.com/what-is-konmari-method/)整理你的意大利面条代码。

# 带着感激丢弃

在整理你的垃圾文件夹之前，搜索并删除所有不用的代码。未使用的代码会占用你的精神空间，当你开始整理东西时会分散你的注意力。

使用自动化工具，如 Chrome 的 DevTools 中的 [Vulture](https://github.com/jendrikseipp/vulture) 或 [Coverage tab](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage) 来帮助您更有效地检测未使用的代码。如果你想在按下退格键时感到自信，就要构建一个健壮的代码覆盖率高的测试套件。

庆祝你将要删除的代码达到了它们的目的。感谢他们的作者将项目向前推进了一步。然而，现在是时候让未使用的代码离开您的代码库，退回到您的版本控制历史的阴影中了。

# 按类别整理，而不是按位置

当你走进一所房子，发现每个房间都有脏衣服，玛丽·近藤会建议你先把它们收集成一大堆，而不是试图一个房间一个房间地整理。

![](img/4f3a2144858135bf56fcb2ae06ed23ba.png)

照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

类似地，当您打开一个项目并发现字符串格式方法散落在代码库中时，将它们移到一个位置。您将能够更快地发现任何重复的代码或可重用的模式。

您可以按照 [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) 中给出的类别来组织您的类和函数。从一个简单的类别开始，热身你的重构肌肉，一次处理一个类别。请随意创建您自己的类别，否则，这是我推荐的顺序:

*   节目主持人
*   控制器
*   服务
*   用例
*   适配器

# 给一切一个家

意大利面代码是由它们不属于任何地方的事实造成的。给他们一个家，他们会开始像拼图一样各就各位。玛丽·近藤方法的一个特点是垂直存放物品。她使用分隔物、透明容器，甚至垂直折叠衣服来增加它们的可见性。

![](img/2ba17e36e68b5924491ec9cf3810dd68.png)

照片由[加雷思·哈伯德](https://unsplash.com/@gadgapho?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当你为你的代码开辟一个新家时，多花几分钟想出一个简单易懂的名字，并注意整体的内聚性。

问问你自己:

*   一个新队友要多久才能找到这个功能？
*   这个函数和这个文件中的其他函数有相似的用途吗？
*   我可以改变这个函数的实现而不破坏依赖它的代码吗？
*   这个功能有没有什么副作用会让打电话的人大吃一惊？

整理的真正目的是让你可以用代码库做你真正想做的事情。通过问自己一个函数属于哪里，你可以让代码库更接近你的愿景。通过清除混乱，您可以更容易地在将来引入代码变更。

毕竟，你定期扫地，擦窗户，整理家里的物品，这样你才能过上舒适的生活。为什么不为您的代码库做同样的事情呢？

# 进一步阅读

[](/4-things-you-have-to-unlearn-to-become-a-better-programmer-547adf476445) [## 成为一名更好的程序员必须忘记的 4 件事

### 取下编码训练轮。

levelup.gitconnected.com](/4-things-you-have-to-unlearn-to-become-a-better-programmer-547adf476445) [](https://codeburst.io/8-rules-for-coding-with-style-25ae4c11f22d) [## 用风格编码的 8 条规则

### 改进编码风格的技巧

codeburst.io](https://codeburst.io/8-rules-for-coding-with-style-25ae4c11f22d) [](/3-coding-practices-for-solving-the-right-problem-526b188241a2) [## 解决正确问题的 3 种编码实践

### 解决错误的问题往往比构建错误的解决方案代价更高。

levelup.gitconnected.com](/3-coding-practices-for-solving-the-right-problem-526b188241a2)