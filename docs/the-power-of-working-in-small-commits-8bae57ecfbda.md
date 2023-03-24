# 小规模工作的力量

> 原文：<https://levelup.gitconnected.com/the-power-of-working-in-small-commits-8bae57ecfbda>

你是否曾陷入困境，希望在事情进展顺利的时候早点提交，这样你就可以挽回混乱的局面？我将解释为什么在构建软件时小规模提交更好，以及如何将它们融入到您的习惯中。

![](img/21c6c35cc2b270630008d89686e86ab9.png)

照片由[丹·克里斯蒂安·pădureț](https://unsplash.com/@dancristianpaduret?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 为什么

我们人类的大脑容量有限，需要记住几十甚至几百次代码库的编辑是不可能的。

我们都在一个问题上工作了太长时间，只是为了在散步后或第二天回来立即解决它。

用你的工具来帮你记忆不是更容易吗？当你的测试是绿色的时候提交，在重构或尝试不同的范例之前有一个健全的检查点，这是一个游戏改变者。在工作的中途意识到了什么？只需将您的工作恢复到已知的工作状态，然后重新开始。

小提交允许你利用像[天皇方法](/why-you-should-use-the-mikado-method-today-c87e4bd6c493)这样的工具。

![](img/8eb1f06ebab17aa1632fba00b4b9bc17.png)

Robina Weermeijer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 怎么

申请起来很简单。只要你知道如何制作一个`git commit`，那么你就拥有了你需要的所有工具。

从将工作分解成功能或行为块开始。一个例子可能是这样的:

*   更新依赖项以引入库 X
*   向前端添加新表单
*   为新表单添加新的 API 端点
*   添加后端服务层与数据库对话
*   将新表单连接到新端点

在这些步骤中的每一步，你都可能有你实现的测试是绿色的，或者现有的测试也应该通过。

在进入下一个检查点之前，在列表中的每个检查点提交。

做出承诺后，深呼吸，想想在开始工作时，你是否学到了任何会影响工作顺序的新东西，或者是否出现了一些应该解决的新问题。

假设当您开始向前端添加新表单时，您决定提取一个公共表单组件，而不是从头开始编写另一个表单。您有两个选择，要么从头开始实现表单，要么通过 copy-pasta 实现表单，要么回滚当前的工作，更新依赖项并开始重构。

无论哪种方式，您都应该在 green 上进行重构，并提交一个您能够回滚到的提交。这让你有能力退回去重新开始，而不是在一团乱麻中混日子，因为你已经知道这不是前进的方向。

总而言之:

*   计划你的工作，并设置检查点
*   在每个检查点之后提交
*   评估如何最好地继续下一步

注意:我个人更喜欢将小的提交到 main 中，因为它讲述了一个很好的故事，但是如果你不这样做，那么你可以在合并你的更改之前将所有的提交挤在一起。

![](img/f9c2edef01c8ddde8147c982945eef5b.png)

托马斯·博尔曼斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

即使您不总是使用这种方法，您仍然可以受益，至少在测试是绿色的时候或者就在您开始重构之前提交。口袋里有一个已知的良好状态会让你专注于手头的任务，而不是担心你是否能“及时完成所有事情”。如果你有其他对你有帮助的软件开发过程或技术，请在评论中告诉我！

如果你喜欢这篇文章，考虑[订阅媒体](https://medium.com/@ascourter/membership)！

如果你或你的公司有兴趣找人进行技术面试，那么请在 Twitter ( [@Exosyphon](http://twitter.com/Exosyphon) )上给我发 DM，或者访问我的[网站](https://andrewcourter.com/)。如果你喜欢这样的话题，那么你可能也会喜欢我的 Youtube 频道。如果你喜欢 3D 打印的东西，可以去我的 [Etsy 商店](https://www.etsy.com/listing/1273702925/6-sided-fidget-cube)看看。祝您愉快！