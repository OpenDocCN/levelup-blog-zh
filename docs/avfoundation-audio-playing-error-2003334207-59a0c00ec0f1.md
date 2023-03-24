# AVFoundation 音频播放错误#2003334207

> 原文：<https://levelup.gitconnected.com/avfoundation-audio-playing-error-2003334207-59a0c00ec0f1>

![](img/88c1dc7501296776e53f3e84e485d53d.png)

由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

让我们来学习如何解决在 iOS 应用程序中播放音频文件时引发的异常，错误代码为“2003334207”。

假设您正在构建一个 iOS 应用程序，将语音记录上传到您的服务器，并在需要时播放它们。您已经创建了应用程序。录下了你的声音。轻按播放按钮来听刚刚录制的唱片。然后出现了一个错误。打印错误对象后，您会看到编译器像这样对您大喊

> **错误域= NSOSStatusErrorDomain Code = 2003334207“(空)”**

开发人员也大叫这是怎么回事！

错误信息不明确。但是经过一些研究，你可以发现错误代码“2003334207”意味着你想要播放的语音记录不存在或已损坏。简而言之，不可玩。但你确定你已经成功录下来了，对吗？然后检查 URL 对象的 absoluteURL 值，发现 URL 对象不为空，并成功打印了语音记录的路径。从**文件://…** 开始

出现此错误是因为您可能使用了错误的 URL 构造函数。

我们知道 AVAudioPlayer 类有一个构造函数需要一个 url 并抛出一个异常。

> 音频播放器(内容数:)

假设您有一个路径，并需要从该路径获取 url 来播放它。有两种类型的构造函数来处理这种情况。**字符串:**和**文件 URLWithPath:**

有很多关于如何在 iOS 应用程序的服务器上播放音频文件的例子，但其中一些使用字符串构造函数，一些也使用 fileURLWithPath。超级混乱。让我们深入研究一下这两个构造函数。

**URL(string:)** 构造函数是 URL 类中最常用的一种。通常它被用于网站 url 处理。因为这个构造函数关系到 *scheme* 并根据 scheme 的类型验证输入。例如，Http 和 Https 是互联网的众所周知的方案。如果您在服务器上下载语音记录以便稍后播放，该记录将保存在您的 iPhone 上，路径以 file:// *方案开始。*因此，在那种情况下，你应该用 URL(string:)

**URL(fileURLWithPath:)** 用于保存在磁盘上的静态资源。这个路径以斜杠开始，然后像/path/to/voice/record 那样继续。如果使用路径 *file://…带 URL(fileURLWithPath:)* 或 */path/to/voice/record 带 URL(string:)* ，AVAudioPlayer 构造函数会抛出错误，错误代码为 2003334207。

下面是在 iOS 应用程序中播放音频文件的简单方法。

您也可以查看下面的 StackOverflow 线程，了解关于该主题的说明。

[](https://stackoverflow.com/questions/41176531/how-to-decide-if-the-url-is-local-file-path-or-remote-file-path) [## 如何确定 URL 是本地文件路径还是远程文件路径

### 感谢贡献一个堆栈溢出的答案！请务必回答问题。提供详细信息并分享…

stackoverflow.com](https://stackoverflow.com/questions/41176531/how-to-decide-if-the-url-is-local-file-path-or-remote-file-path) 

# 返回👨‍💻