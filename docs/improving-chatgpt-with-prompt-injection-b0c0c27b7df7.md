# 用即时注射改善 ChatGPT

> 原文：<https://levelup.gitconnected.com/improving-chatgpt-with-prompt-injection-b0c0c27b7df7>

## 人工智能

![](img/fa7d5d9bac252640703aa1c214d4e716.png)

由 [Jacob Ferus](https://medium.com/@dreamferus) 使用 Midjourney 生成的图像。

ChatGPT 经常提供冗长、全面和有见地的回答，但很少要求用户提供新的信息。正如我在[上一篇关于 ChatGPT](https://medium.com/gitconnected/chatgpt-is-mind-blowing-everything-you-need-to-know-9e03fdb0b370) 的文章中所讨论的，对话式人工智能的一个有用特性是能够使用后续问题来调整答案，使其更加精确和个性化。为了增强这种类型的行为，我决定使用 [*提示注射*](https://medium.com/@dreamferus/how-ai-can-be-hacked-with-prompt-injection-9a394a94516) *来“重新配置”模型。*

这可以通过以下问题来测试:我如何学习 Python？首先，在没有任何修改的情况下，ChatGPT 给出了以下答案:

![](img/e968bc84b5c307f56927025e498744cc.png)

答案还不错，但相当模糊笼统。我可以改进问题以获得更好的答案，但是如果我不熟悉 Python，可能很难知道我在寻找什么和要问什么。现在，我将尝试调整人工智能来回答我的问题，同时寻求提取更多信息来改进其答案:

![](img/8b34ac021dd2909a1752323cb12069f3.png)![](img/944989522b9c50ad39025546582da19e.png)![](img/970652826833ecb6b28c03a397f740e1.png)![](img/37b23abd3484875ab630611731cfd606.png)

可以看出，人工智能在每次解释后都使用后续问题来获得更多信息并改进其答案。这样，它可能会问用户不知道会影响学习过程的问题。互动也变得更加协作。

另一种选择是，不要反复回答问题，而是先弄清楚所有必要的细节，然后再回答。我重新配置了人工智能来做这个:

![](img/0729a6254aa6f78d86924e05de890647.png)![](img/d3481ceb2c97697022e187a5fa813e48.png)

可以看出，人工智能能够使用从它提出的问题中获得的信息来构建更好的学习路径。我认为在背景真的很重要的情况下，这种类型的互动非常重要。例如，如果我问我应该学习什么编程语言，这当然取决于我想用它做什么。或者，如果我想购买新的运动器材，这取决于我的级别、预算等。

# 摘要

使用提示注入，您可以更精确地定制语言模型的行为，以满足您的需求。也许将来 ChatGPT 可以使用这种选项，而不需要立即注射。

如果你有兴趣阅读更多关于人工智能的文章，请查看我下面的阅读列表:

![Jacob Ferus](img/3fe8a4152942d124e84f4370bdeaa704.png)

[雅各布·费罗斯](https://medium.com/@dreamferus?source=post_page-----b0c0c27b7df7--------------------------------)

## 人工智能

[View list](https://medium.com/@dreamferus/list/ai-ea01474f2db5?source=post_page-----b0c0c27b7df7--------------------------------)35 stories![](img/55f405937bb9e60d75ddc10053056fd9.png)![](img/1e1d6ae693671af0fb53238b6a321d86.png)![](img/ddc10e2a657adb0dca6dc40fbab3ef74.png)

如果你想成为中级会员，你可以使用我的推荐链接。祝你有愉快的一天。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)