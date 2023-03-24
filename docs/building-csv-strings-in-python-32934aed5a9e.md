# 用 Python 构建 CSV 字符串

> 原文：<https://levelup.gitconnected.com/building-csv-strings-in-python-32934aed5a9e>

![](img/b250854533c4c24de6e8cdc096d4b187.png)

照片由[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

任何使用 Python 处理过 CSV 的人都应该熟悉 Python 强大的`csv`模块。对于那些不熟悉的人来说，这是一个 Python 库，可以读写带有各种格式选项的 CSV 文件。本文不是如何用 Python 读写 CSV 的老调重弹。它是关于引出一个相当不寻常(但有用)的模块用法。

在我的公司，我们有一个报告系统，每天执行报告，并将结果以纯文本、JSON 和 CSV 格式存储在数据库中。当我被要求写一份新报告时，我意识到最终目标不是生成一个 CSV 文件，而是一个 CSV 字符串。幼稚的方法是以编程方式构建一个 CSV 字符串，包括构建行、将它映射到一个标题并用一个分隔符连接它。此时，我非常需要使用`csv`模块。我知道你在想什么，

> "但是`csv`模块只能写入文件对吗？"

让我们看看[文档](https://docs.python.org/3/library/csv.html#csv.writer)是怎么说的:

```
csv.**writer**(*csvfile*, *dialect='excel'*, ***fmtparams*)Return a writer object responsible for converting the user’s data into delimited strings on the given file-like object. *csvfile* can be any object with a **write()** method.
```

鸭打字万岁！`csvfile`对象不一定是文件对象。它可以是任何实现了`write`方法的对象。有了这些新发现的知识，让我们开始编码吧。

我们新的 csvfile 对象

我们构建了自己的名为`CsvTextBuilder` 的`csvfile`对象，它实现了一个`write`方法。该方法接受一行作为输入，并将其追加到成员变量。

使用 csv.writer 生成 CSV 字符串

格式化和定界由`csv`模块负责。很整洁，不是吗？但是等等，还有更多！我知道大蟒蛇有多喜欢字典。把这个能力扩展到`csv`模块的`DictWriter` *该有多牛逼。别担心，我会掩护你的。我要感谢 [Pranjal Singi](https://medium.com/u/19ae03e7c47b?source=post_page-----32934aed5a9e--------------------------------) 提出这个想法并实施*。*我们来看一些代码。*

使用 csv 生成 CSV 字符串。词典作者

就这样，字典列表被无缝地转换成 CSV 字符串。你可以尝试一下`csv.writer`提供的所有选项，比如`delimiter`、`quotechar`、`quoting`等等，看看哪些适合你。这个小练习向我们展示了如何利用像[鸭子打字](https://en.wikipedia.org/wiki/Duck_typing)这样的语言特性来增强模块的行为。期待写更多关于我的 Python 实验。

保持好奇，不断学习，不断成长。

参考资料:

1.  [https://docs.python.org/3/library/csv.html](https://docs.python.org/3/library/csv.html)
2.  [https://en.m.wikipedia.org/wiki/Duck_typing](https://en.m.wikipedia.org/wiki/Duck_typing)