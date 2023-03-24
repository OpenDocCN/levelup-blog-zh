# 让您的 Python 程序说话！

> 原文：<https://levelup.gitconnected.com/make-your-python-program-speak-310766534fbf>

## 如何使用谷歌的 gTTS 库

![](img/18d9a88ef8db4719e363623702c32d82.png)

[亚历山大·奈特](https://unsplash.com/@agk42?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你有没有想过如何创造一个会说话的程序？乍一看，这似乎是一项非常困难的任务。然而，谷歌已经为我们做了大部分工作，现在我们可以使用他们的库非常容易地从文本创建音频文件。

# 安装库

首先，我们需要安装 gTTS 库。

我建议你为这个项目创建一个新的虚拟环境(如果你不知道怎么做，可以查看 [*这篇文章*](https://python.plainenglish.io/how-to-create-and-manage-virtual-environments-in-python-706bf22fc1b) )。然后，我们可以使用 pip 安装该库:

```
pip install gtts
```

# 创建 mp3 文件

使用这个库最简单的方法是创建一个 mp3 文件，然后执行它。

首先要做的是创建一个`gTTS`对象。这有许多参数，但现在让我们只关注最重要的一个:我们想要阅读的文本。

```
from gtts import gTTStts = gTTS("Hello world")
```

既然我们已经指定了想要阅读的单词，我们可以将音频保存到一个`mp3`文件中:

```
tts.save('hello.mp3')
```

这是你开始创建 mp3 文件所需要的一切。很简单，对吧？

但是我们还可以做更多的事情:改变语言，添加特殊的口音，甚至直接用 Python 播放声音！

# 一些可选参数

现在我们知道了基本知识，让我们看看`gTTS`对象的更多参数。

## 语言和口音

首先，你可能想使用一种不同于英语的语言，或者甚至是一种特殊的口音(例如，澳大利亚口音、美国口音、英国口音)。为此，我们使用两个可选参数:

*   `tId`指定用于创建音频的翻译 API 的域。这是谷歌翻译网站的域名:*https://translate . Google .<tId>。*默认值是`com`，但是你可以指定任何有效的域，这将使用(在大多数情况下)本地口音。比如`co.uk`是英国口音，`com.au`是澳洲口音。
*   `lang`用于指定文本的语言，使用两个字母的代码:`en`(默认值)表示英语，`fr`表示法语，`es`表示西班牙语，以此类推。如果您想要检索所有可用语言的列表，请使用以下代码:

```
**from** gtts **import** langprint(lang.tts_langs())
```

这里`lang.tts_langs()`返回一个字典，其中键是语言的名称，值是相应的代码。

## 慢慢说

还有一个让程序说话更慢的参数:`slow`。这是默认的`False`，但是如果你需要，你可以改变它。

```
tts = gTTS("Hello world", slow=True)
```

## 预处理

如果您想在文本转换成音频之前对其进行一些修改，您可以使用`pre_processor_funcs`参数。这需要一个函数列表，取自函数库的`gtts.tokenixer.pre_processors`子模块。一些比较常见的是:

*   `abbreviations`用于将已知的缩写替换为完整的单词。
*   `end_of_line`修改在一行结束和下一行开始之间分开的单词。

***注:*** *库的官方文档中有一些更复杂的预处理程序。*

下面是一个使用预处理器的示例:

```
**from** gtts.tokenizer.pre_processors **import** abbreviations, end_of_linetts = gTTS("Hello world", pre_processor_funcs = [abbreiations, end_of_line])
```

# 直接播放音频

现在我们知道了如何创建 mp3 音频，但是我们仍然想在 Python 程序中播放它。

为此，我们需要安装一个库来播放`mp3`文件。在本教程中，我们将使用`pygame`，但你可以选择任何你想要的。

首先，我们来安装 Pygame:

```
pip install pygame
```

现在，要播放我们创建的 mp3 文件，您可以使用以下代码:

```
**from** pygame **import** mixer
**import** timemixer.init()
mixer.music.load("hello.mp3")
mixer.music.play()
time.sleep(2)
```

这里我们从 Pygame 初始化混音器，加载音频，然后播放。

我们添加了`time.sleep`命令，这一点很重要:这可以确保程序不会在音频播放之前终止。

# 完整的代码

在这里，您可以找到我们在本文中使用的完整代码:

```
**from** gtts **import** gTTS
**from** gtts.tokenizer.pre_processors **import** abbreviations, end_of_line
**from** pygame **import** mixer
**import** time*# Create the text* text = "Hello World!"
tts = gTTS(text, slow=False, pre_processor_funcs = [abbreviations, end_of_line]) *# Save the audio in a mp3 file* tts.save('hello.mp3')*# Play the audio* mixer.init()
mixer.music.load("hello.mp3")
mixer.music.play()*# Wait for the audio to be played* time.sleep(2)
```

# 结论

感谢您阅读本文！如果您需要更多信息，请查看以下资源:

*   [gTTS 文档](https://gtts.readthedocs.io/en/latest/)
*   [如何使用 pygame 混音器](https://coderslegacy.com/python/pygame-mixer/)