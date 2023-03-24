# 如何写一本科技电子书

> 原文：<https://levelup.gitconnected.com/how-actually-to-write-a-tech-e-book-389723608843>

## 每个人都想卖一本电子书，但是怎么写呢？

![](img/ec5e6ef4a29199ce5120024734b3844e.png)

与 Raj 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[公路旅行照片](https://unsplash.com/@roadtripwithraj?utm_source=medium&utm_medium=referral)

你可能已经看到了一些文章，这些文章为开发人员提供了一些额外的想法，在这些文章中，人们推荐销售技术书籍。写一本关于你最喜欢的技术的技术电子书，并开始获得被动收入，听起来很可爱。但是怎么做呢？用哪个软件？如何添加代码片段？最后，如何将你的文本包装成流行的电子书格式？我将在本文中讨论所有这些主题。

# 软件和源格式

当你想写一本书时，我首先想到的应用程序是谷歌文档或微软 Word。但是经典应用默认不支持代码片段。谷歌文档在他们的[市场](https://workspace.google.com/marketplace/search/Code%20blocks)有免费的扩展，但是我不确定在你的文档中使用它是否安全。

理想情况下，我想要一些简单，快速，离线支持。

看起来你可以以每月 5.99 美元的价格购买尤利西斯，然后毫无问题地写书。这款以作家为中心的应用程序拥有我们写书所需的一切，支持代码片段，并且可以将文本导出到 epub 或 pdf。

但作为一名软件工程师，你可能已经有了一个写书的理想应用。**是 VS 代码！**差不多两年前，我看到了一篇[文章](/turn-vs-code-into-the-perfect-writing-tool-a22a136e4360)作者epub:
pandoc -d config.yml \
`find $(SOURCE) -name '*.md' | sort` \
-o $(DIST)/$(BOOK_NAME).epub

在这两行之间，你可以提到我们使用了`config.yml`文件作为我们的书的配置。该文件将包含 Pandoc、纸张大小、字体大小、页边距、目录等所有图书设置。

此外，这个文件将包含你的书的元数据(标题，作者，关键字，权利，语言等)。).你可以将这个元数据移动到一个单独的文件`metadata.yml`中，并在 config.yml 中链接它

以下是`config.yml`和`metadata.yml`文件的示例:

现在，您的项目应该如下所示:

```
/images        // folder for images
/src           // folder with markdown files
config.yml
Makefile
metadata.yml
```

如果一切正确，现在可以在终端中键入“make epub”或“make pdf”，书籍就会出现在 dist 文件夹中。

是的，这是你的第一本电子书！

> 我在 [Github](https://github.com/Golosay/e-book-starter) 上传了技术电子书的入门模板。

# 下一步是什么？

首先，不要犹豫在你的项目中使用 Git。使用 Git，您将能够回滚更改并在多个设备上工作。当然，如果你打算和合著者合作，Git 是必备的。

如果你想有一个. mobi 版本，你需要检查一些转换器，因为 Pandoc，不幸的是，不支持 Kindle 格式。

现在只剩下写书和开始卖了😇。

**感谢阅读！**
写得开心，销量好！