# 棘手案例#1。语言检测

> 原文：<https://levelup.gitconnected.com/trickycases-1-language-detection-18ec5ef2240b>

***免责声明:****【tricky cases】是一系列包含相当短的代码片段的帖子，在日常的 ML 实践中非常有用。在这里你可以找到一些你想在 StackOverflow 中搜索的东西。*

![](img/7eb98ef815a6ee730c1ef23583ec173c.png)

照片由 [1983 年](https://unsplash.com/@unonueveochotres?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

即使 dataframe 的前 5 个条目只包含这种语言的样本，你也不能总是假设你在处理英语文本。特别是在处理众包或从网页解析的数据时。为了避免猜测，您可能希望有一段代码，能够以快速和高质量的方式检测该语言。为了完成这项任务，我回顾了最流行的 Python 工具，并编写了一个简短的故事与大家分享。

出于测试目的，我创建了一组用 Google 翻译的“hello world”示例。如果你的母语是下面列表中提到的一种或多种语言，并且你发现了一个错误，请告诉我。

# **语言检测**

**准确率得分:** 0.464

# 空间语言检测器

**准确率得分:** 0.5

# Langid

**准确率得分:** 0.428

快速文本

**准确率得分:** 0.607

# 摘要

*   FastText 已经显示出比其他工具更高的准确性，但是它需要将相对较大的模型下载到内存中
*   Spacy LanguageDetector 在 en_core_web_sm 和 en_core_web_lg 上给出相同的结果。当然，这对于这个特殊的例子是有意义的，因为我使用的单词是“hello”和“world ”,并且存在于两个模型的词汇表中。在现实生活中，性能可能会有所不同。
*   在这种情况下，我测量了 29 种语言的准确性，但不太可能使用这么多种语言。请随意重用代码，并根据您希望在数据集中包含的语言生成您自己的精度集。

**完整代码:**[https://gist . github . com/the desti/e 03 db 827 bfb 7 b 8 B2 b 71 a9 c 9031d 6128 c # file-language _ detector-ipynb](https://gist.github.com/theDestI/e03db827bfb7b8b2b71a9c9031d6128c#file-language_detector-ipynb)