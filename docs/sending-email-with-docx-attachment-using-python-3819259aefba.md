# 用发送电子邮件。使用 Python 的 docx 附件

> 原文：<https://levelup.gitconnected.com/sending-email-with-docx-attachment-using-python-3819259aefba>

![](img/03ddaf7acfcba44618511b698b832a2c.png)

Solen Feyissa 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

本文将探讨如何用 Python 发送带附件的电子邮件，以及如何将其集成到您现有的数据科学项目中。

本文中使用的技术假设读者已经基本理解了用 Python 发送基本电子邮件的[的结构。](https://pyshark.com/sending-email-using-python/)

**目录**

*   介绍
*   创建多部分消息
*   补充。docx 附件
*   发送电子邮件
*   结论

# 介绍

发送带有附件的电子邮件对生产中的项目有很大的帮助。例如，它可以集成为在每次成功运行程序后发送详细的日志(等等)。

那么，使用 Python 发送带附件的电子邮件需要什么呢？实际上没有那么多:一些 Python 编程技巧和所需库的知识。

正如在关于使用 Python 的[基本电子邮件的教程中一样，我们将使用 Gmail 地址(但是您可以将其扩展到任何其他电子邮件服务提供商。](https://pyshark.com/sending-email-using-python/)

# 用 Python 创建多部分消息

要继续本文，请导入所需的库:

与 Python 中的[基本文本邮件发送者不同，我们需要分别构建消息的各个部分。这意味着我们将分别制作每个部分，然后将其组装成一个消息对象。](https://pyshark.com/sending-email-using-python/)

首先，我们将定义我们的发件人和收件人地址:

然后我们制作一个 **MIMEMultipart()** 类的实例。这是一个 MIME 消息类，它包含多个部分，然后这些部分被合并成一条我们将要发送的消息:

下一步，我们要指定我们的 **msg** 对象的具体部分。我们将在其中添加发件人和收件人的电子邮件地址，以及主题:

接下来，我们将处理电子邮件的正文。到目前为止，这里的事情需要比前一部分多注意一点。让我们看一下代码并进一步讨论它:

我们首先创建一个字符串，其中包含我们希望在您的电子邮件中使用的文本内容，并将其存储为 **body** 。然后，我们使用 **msg** 对象的 **attach()** 方法(它是 MIMEMultipart()类的一个实例)将它添加到我们的电子邮件中。
一个重要的注意事项，当我们将它添加到我们的 **msg** 对象时，我们需要使用 **MIMEText()** ，它用于创建 text (string)类型的 MIME 对象。

# 补充。使用 Python 将 docx 附件添加到电子邮件

在文章的这一部分，我们将学习如何将文件作为附件添加到电子邮件中。与其他部分相比，本文的这一部分具有最复杂的代码。不过没什么好担心的，我们会详细讨论每一步。

现在，让我们假设我们想要添加一个简单的 word 文档( **myfile.docx** )。让我们首先定义我们想要用作附件的文件的路径，并将其保存为一个名为 **files** 的列表:

接下来，我们将以读/写模式打开该文件，并对其进行一些操作:

*   最初，我们以读/写模式将 Word 文件作为附件**读取。**
*   然后我们用“application”和“octet-stream”作为参数创建一个 **MIMEBase()** 类的实例，并将其存储为**部分**。它的作用是指定内容类型，在这个例子中是一个二进制文件。我们这样做的原因是因为我们希望在电子邮件应用程序(如 Gmail、Outlook 等)中打开该文件。
*   现在，我们需要使用 MIMEBase()类的 **set_payload()** 方法将有效载荷转换为编码形式，并将读取的文件作为参数传递给它。
*   一旦我们有了编码的表单，我们将使用**将编码器设置为 Base64。encode_base64(部分)**。在计算机科学中，Base64 是一种二进制到文本的编码。
*   正如您可以跟踪代码一样，**部分**变量是我们对其应用了一些修改的 MIMEBase()类的一个实例。现在我们要做的是使用**。add_header()** 方法，带有特定的[“Content-Disposition”](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)参数。
*   最后，我们将使用**将其添加到输出**消息**中。attach()** 方法并将其转换为字符串格式。

精彩！我们的电子邮件是以正确的格式构建的。我们准备发送了！

# 使用 Python 发送电子邮件

我们剩下要做的就是登录到我们的电子邮件服务器，发送以 **msg** 为内容的电子邮件。

在本文中，我们将使用 Gmail 电子邮件地址发送邮件:

关于这个代码块的详细解释，请参考我之前关于使用 Python 发送基本电子邮件的文章。

# 结论

这段代码的完整版本应该如下所示:

本文主要探讨如何使用发送电子邮件的过程。 [Python](https://pyshark.com/) 中的 docx 附件。这应该是理解该过程并具有将其集成到现有数据科学项目中的知识的良好基础。

如果你有任何问题或者对编辑有任何建议，请在下面留下你的评论。

*原载于 2020 年 6 月 28 日 https://pyshark.com*[](https://pyshark.com/sending-email-with-docx-attachment-using-python/)**。**