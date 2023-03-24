# 分组反应挂钩与编曲

> 原文：<https://levelup.gitconnected.com/grouping-react-hooks-with-arranger-2c465928400>

![](img/b2d8ad5edad114b3d95a3be0ea018541.png)

由[苏阿德·卡玛丁](https://unsplash.com/@skmuse_?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/hooks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

我真的很喜欢用钩子。我认为这是很长一段时间以来前端架构中仅有的创新事物之一。

当钩子被宣布的时候，在库[重组](https://github.com/acdlite/recompose)被否决后不久。尽管我理解其中的原因，但我仍然觉得在`recompose`中使用的模式是我想继续使用的。

[Arranger](https://github.com/cevr/arranger) 是我做的一个小库(1kb gzipped)，用钩子延续了这些模式。

让我来演示一下。

这里有一个显示配置列表的组件，您可以编辑其名称。每当添加新配置时，它都会滚动到该配置并显示 3 秒钟。

带挂钩

下面是使用 Arranger 的相同组件:

带编曲器

如您所见，功能是相同的。虽然 API 有点不同，但我认为钩子的本质被保留了下来。

我发现将逻辑从视图中分离出来使我更容易阅读和推理一个组件。React hooks 允许人们很容易地做到这一点，而 arranger 允许人们更进一步。

感谢阅读！请自己去图书馆试试。如果您有任何建议或改进，请随时投稿！