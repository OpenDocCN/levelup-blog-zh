# 如何用 Python 制作 PDF 文本到语音阅读器

> 原文：<https://levelup.gitconnected.com/how-to-make-a-pdf-reader-in-python-579f0e7330b2>

## 您可以使用 Python 将任何 PDF 转换成有声读物。以下是如何使用各种音量、声音和速度调制进行文本到语音转换。

![](img/dc55d675383187b80f30d0fe009a8bfe.png)

安德烈亚[皮亚卡夸迪奥](https://www.pexels.com/photo/man-in-red-polo-shirt-sitting-near-chalkboard-3779448/)从[像素](https://www.pexels.com/)拍摄的照片

PDF 非常适合分发，因为我们可以在任何 PDF 阅读器设备上阅读它们。然而，许多读者更喜欢听他们的内容，而不是读它。幸运的是，您可以使用 Python 将任何 PDF 转换成音频文件，我们可以在几种流行的设备上播放。

使用 Python 创建 PDF 阅读器的第一步是下载并安装正确的库。在本教程中，我们将使用 [PyPDF2](https://pypi.org/project/PyPDF2/) 和 [pyttsx3](https://pypi.org/project/pyttsx3/) 。PyPDF2 是一个读取和操作 PDF 文件的库，而 pyttsx3 会将它们转换成音频文件。

**关联:** [***如何用 Python 下载 YouTube 视频？***](https://www.the-analytics.club/download-youtube-videos-in-python)

这些库不是默认安装的，所以我们需要使用 pip 来安装它们。为此，我们可以打开命令提示符或终端，输入以下命令:

```
pip install PyPDF2 pyttsx3
```

# 1.使用 Python 读取 PDF 文件

用 Python 读取文件很简单。我们需要使用 [open()](https://www.w3schools.com/python/ref_func_open.asp) 函数打开文件。

然而，要阅读 PDF 的内容，我们需要安装 PyPDF2 库。我们可以通过创建一个 PdfFileReader 对象并将 PDF 文件的文件路径传递给它来做到这一点。

```
from PyPDF2 import PdfFileReader

# create a PdfFileReader object
reader = PdfFileReader("/path/to/file.pdf")
```

# 2.获取要阅读的页面

一个 PDF 文件可能包含多个页面。所以我们需要指定我们想读哪一页。

PyPDF2 模块提供了两种处理页面的方法。我们可以使用`numPages`属性找到 PDF 的页数。属性返回 PDF 中所有页面的列表。

```
number_of_pages = reader.numPages
page = reader.pages[0]
```

如果您要将一个大的 PDF 文件转换成有声读物，您可以使用它来循环浏览页面。

```
for page in reader.pages:
    # do the rest

#or
for i in range(reader.numPages):
    page = reader.pages[i]

    #do the rest
```

# 3.使用 Python 将 PDF 转换为文本

一旦我们决定阅读哪一页，我们就需要从那一页中提取文本内容。在 PyPDF2 中，我们可以使用`extractText`属性。

```
text = page.extractText()
```

# 4.配置 pyttsx3 引擎

现在我们有了 PDF 的文本内容，我们需要将它转换成音频。为此，我们将使用 pyttsx3 库。

Pyttsx3 是 Python 中的文本到语音转换库。它可以离线工作，兼容多种平台，包括 Windows、Linux 和 MacOSX。

第一步是创建 pyttsx3 引擎类的实例。

```
import pyttsx3
engine = pyttsx3.init()
```

一旦引擎准备好，我们就可以设置声音(男，女)，音量和语速。但这些都是可选的。

## 设置声音—男声/女声

我们需要得到引擎中可用的东西来设置声音。

```
voices = engine.getProperty('voices')
```

现在，我们可以在引擎上设置语音属性。使用 0 设置男声，使用 1 设置女声。

```
#Male
engine.setProperty('voice', voices[0].id)  

#Female
engine.setProperty('voice', voices[1].id)
```

## 设置阅读速度

我们可以通过将 rate 属性设置为您想要的每分钟字数来改变阅读速度。

```
engine.setProperty('rate', 150)
```

如果你只想加快(或减慢)阅读速度，而不是设定一个特定的速度，你可以参考当前的速度并进行调整。

```
# refer to the current value
rate = engine.getProperty('rate') 

engine.setProperty('rate', rate+50)
```

## 改变音量

在 pyttsx3 中，可以在 0 和 1 之间设置音量。0 表示静音，1 表示将音量设置为最大。

```
engine.setProperty('volume',.75)
```

# 5.阅读或保存音频

我们已经做了所有的背景工作。现在让我们开始行动吧。在 pyttsx3 中，您必须调用`say`方法，然后调用`runAndWait`方法来进行实际的语音。

```
engine.say(text)
engine.runAndWait()
```

最后，我们可以使用 pyttsx3 的 Engine 类的 save_to_file()方法将音频保存为 MP3 格式。

```
engine.save_to_file(text, 'test.mp3')
```

# 把所有的放在一起

为了更好地理解代码，我们一行一行地浏览了代码。现在，转换 pdf 并将其保存到文件的完整版本如下所示。

```
import pyttsx3
from PyPDF2 import PdfFileReader

# create a PdfFileReader object
reader = PdfFileReader("/path/to/file.pdf")

# extract text from page 1 (index 0)
page = reader.pages[0]
text = page.extractText()

# Create a pyttsx3 engine
engine = pyttsx3.init()

# set the voice
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)  #Female

# set the speed/rate of speech
engine.setProperty('rate', 150)

# set the volume
engine.setProperty('volume',.75)

engine.save_to_file(text, 'test.mp3')
```

# 最后的想法

pdf 到处都是。但是读者的偏好也会发生巨大的变化。

想听的人比想读的人多。因为在听的时候有可能做别的事情。我们在阅读时没有这种灵活性。还有，听力对于视力障碍的人来说更方便。

但是我们并没有为我们在互联网上看到的每一个 PDF 资源找到音频版本。但是 Python 程序员可以通过几行代码轻松地将它们转换成音频文件。

在本文中，我们讨论了如何将 pdf 转换成音频文件。我们也在寻找用不同的音量、声音和速度来修改演讲的方法。

> *感谢阅读，向我问好*[***LinkedIn***](https://www.linkedin.com/in/thuwarakesh/)*，*[***Twitter***](https://twitter.com/Thuwarakesh)*，*[***Medium***](https://thuwarakesh.medium.com/)*。*
> 
> *还不是中等会员？请使用此链接到* [***成为会员***](https://thuwarakesh.medium.com/membership) *因为，在没有额外费用的情况下，我为你引荐赚取一小笔佣金。*