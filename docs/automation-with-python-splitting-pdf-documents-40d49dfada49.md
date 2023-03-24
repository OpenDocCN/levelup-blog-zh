# Python 自动化:分割 PDF 文档

> 原文：<https://levelup.gitconnected.com/automation-with-python-splitting-pdf-documents-40d49dfada49>

我为什么喜欢电脑？因为他们能够并且愿意为我们做枯燥的工作。如果我们给他们明确的指示。我相信计算机应该被用来把人们从重复性的非创造性/非哲学的工作中解放出来。他们可以做得更好，人们可以专注于做更有趣的事情。

![](img/acc2182a4c91706004ccb632edda8897.png)

来源:[https://www . needpix . com/photo/1845382/机器人-自动化-工厂-工业-机器-设备-工程-工作-机器人](https://www.needpix.com/photo/1845382/robot-automation-factory-industry-machine-equipment-engineering-work-robotic)

**问题定义**

我的一个非技术同事在处理大量 pdf 文档(几千页)时遇到了问题。他们非常沮丧，因为他们不得不花一整天的时间将几千页分成 20 页的小文档。你可能会猜到这种任务不会让任何人兴奋。

![](img/cf5124c119012c22c3f833525fa4ccfb.png)

来源:[https://pix abay . com/photos/cat-misanthropy-bored-lay-2744950/](https://pixabay.com/photos/cat-misanthropy-bored-lay-2744950/)

看着这个人多么沮丧，我决定开发一个非常简单的程序来代替人类完成这项工作。它可以在几秒钟内完成，而不是一整天。

**脚本**

让我们开始解决问题吧。我决定使用 python 和库 **PyPDF2** ，它允许我们操作 PDF 文档。

首先让我们安装 PyPDF2(确保 pip 已经安装在您的系统中):

```
*pip install PyPDF2*
```

如果安装成功，让我们开始编写代码。

首先我们需要导入 **PyPDF2** 及其方法。对于我们的具体情况，我们将需要读取器和写入器方法:

```
*from PyPDF2 import PdfFileWriter, PdfFileReader*
```

接下来用阅读器方法打开 pdf 文档:

```
*inputpdf = PdfFileReader(open(“document.pdf”, “rb”))*
```

我们还需要设置每个文档应该包含多少页的范围。为此，我们将创建一个最初等于 0 的**开始**变量和等于 20 的**结束**变量。

现在我们准备进入程序的主要部分。

让我们使用一个 **while** 循环来迭代创建所有文档所需的次数。在这个循环中，我们将遍历原始文档，根据需要选择并添加尽可能多的页面到新文档中(在我们的例子中是 20 个)。然后我们将创建一个新文档。此外，我们需要正确命名新文档，以帮助用户看到哪个文档包含哪些页面。在 while 循环的最后，让我们更新 start 和 end 变量，这样我们就可以在下一次迭代中进入页面的下一部分。

![](img/92fcf58ece7f05381eb5ca0fed2e8d18.png)

来源:[https://www . needpix . com/photo/379092/robots-computers-bots-character-technology-person-3d-cyborg-Android](https://www.needpix.com/photo/379092/robots-computers-bots-character-technology-person-3d-cyborg-android)

我们开始吧，新文档被创建。查看我们程序的完整代码:

**结论**

尽管这个脚本非常简单，但它确实为聪明且高效的人节省了一整天的工作。这就是技术应该做的！留点时间给众生！

不断学习，不断成长！

我们上 [LinkedIn](https://www.linkedin.com/in/pavel-ilin) 连线吧！