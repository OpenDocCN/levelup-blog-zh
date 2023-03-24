# 语音中的脏话过滤

> 原文：<https://levelup.gitconnected.com/profanity-filtering-in-speech-41ae4fd6cccf>

用星号替换冒犯性词语

![](img/c08cd81e8d367dff3fc1bbe3aa9fbbac.png)

照片由[纳吉布·卡利尔](https://unsplash.com/@nkalil?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/filter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

之前我已经覆盖过一个[言论内容安全检测](https://betterprogramming.pub/build-a-speech-content-safety-detection-tool-using-python-e4419f6c593)的教程，对言论中的色情、恐怖、仇恨言论等敏感内容进行识别。这个特性对于平台上的内容调节非常有用，但是如果你的用例只是过滤掉言语中的攻击性词语，那么这个特性就太过分了。

例如，给定一个具有以下转录的演讲:

```
It blows my fucking mind every time I hear the story.
```

假设您期望的最终转录如下:

```
It blows my f****** mind every time I hear the story.
```

您需要执行以下步骤来过滤脏话:

*   语音识别提取转录
*   对转录进行自然语言处理，以识别攻击性词语并用星号替换它们

在本教程中，您将学习通过 AssemblyAI 提供的外部[语音转文本 API](https://www.assemblyai.com/) 轻松执行脏话过滤。

让我们继续下一部分，开始安装必要的模块。

# 设置

本教程基于 Python，但是您也可以通过其他编程语言复制相同的输出，因为核心实现是基于 API 调用的。在此之前，您应该创建一个新的虚拟环境，将其与您的其他项目隔离开来。

## 要求

对于进行 HTTP API 调用，`requests`包是首选。运行以下命令进行安装:

```
pip install requests
```

## API 密钥

之后，你需要在 [AssemblyAI 的注册页面](https://app.assemblyai.com/signup)注册一个新账户，每个月有 3 个小时的转录时间。完成后，您应该会看到以下主页:

![](img/e5ad03dd2cbbe367f2a6961d6a457031.png)

作者图片

记住 API 键，因为稍后调用转录 API 时它是必不可少的。

# 履行

供您参考，整个转录过程如下:

*   将您的本地音频文件上传到任何形式的在线存储，以获得一个可访问的 URL。或者，您可以使用 AssemblyAI 提供的那个。
*   用包含可访问 URL 的`audio_url`对转录 API 进行 POST 调用，并将`filter_profanity`参数设置为`true`。
*   再次调用转录 API，但改为通过 GET。它将根据转录过程的当前状态返回不同的状态。一旦完成，您应该会得到 JSON 中的转录文本。

## 上传本地音频文件(可选)

这一步是可选的，如果您已经有一个可访问的 URL，您可以跳过这一步。否则，继续在当前工作目录下创建一个名为`upload_file.py`的新文件。

在文件中添加以下代码，并相应地替换`api_key`和`filename`变量:

为了上传本地音频文件，只需保存文件并运行以下命令:

```
python upload_file.py
```

上传过程完成后，您应该会得到以下输出:

```
{"upload_url": "https://cdn.assemblyai.com/upload/ccbbbfaf-f319-4455-9556-272d48faaf7f"}
```

稍后调用转录 API 时，可以使用新创建的可访问 URL 作为输入参数。

## 对脚本 API 进行 POST 调用

通过 POST 调用 [AssemblyAI 的转录 API](https://docs.assemblyai.com/api-ref/v2-transcript) 进行转录。只需创建一个名为`transcribe.py.`的新 Python 文件，并在其中添加以下代码:

记住相应地更换`api_key`和`audio_url`变量。完成后，保存文件并在终端上运行以下命令:

```
python transcribe.py
```

您应该得到一个 JSON 格式的输出，它包含以下键值对:

```
{ "id": "xxxxxx"
  "language_model": "assemblyai_default",
  "acoustic_model": "assemblyai_default",
  "language_code": "en_us",
  "status": "queued",
  "audio_url": "https://bit.ly/3yxKEIY",
  "filter_profanity": true, ...
}
```

您只需关注以下事项:

*   `id` —您的流程的唯一标识符。稍后您将使用它来调用 GET API 以获得最终的转录。
*   `status` —表示您的转录进度

该状态可能返回一个`error`,指示转录失败。这种失败有几个原因:

*   不支持的音频文件格式
*   音频文件不包含音频数据
*   音频文件太短(< 200 毫秒)
*   无法访问音频文件的 URL
*   API 端的错误

## 对脚本 API 进行 GET 调用

在上一节中，您已经通过 POST API 调用开始了转录过程。此外，根据音频文件的长度，转录过程可能需要 10 分钟。

现在，简单地让另一个 GET HTTP 到同一个脚本 API。创建一个名为`transcribe_file.py`的新文件，并添加以下代码:

同样，记得相应地更换`api_key`和`id`变量。在您的终端上运行以下命令:

```
python transcribe_file.py
```

它将返回一个 JSON 输出，指示转录的当前状态。一旦转录完成，您将获得一个包含相关转录文本的 JSON 输出:

```
{"status": "completed",
 "text": "Hello there ...",
 ....
 "words": [
        {
            "confidence": 1.0,
            "end": 440,
            "start": 0,
            "text": "Hello"
        },
        {
            "confidence": 0.96,
            "end": 10060,
            "start": 9600,
            "text": " there"
        },
        ...
    ]
}
```

与正常的抄本不同，一旦你将`filter_profanity`设置为`true`，除了第一个字符之外，任何冒犯性的单词都将被星号掩盖。

这是非常有用的，尤其是当你有大量的录音，并希望有一个快速和简单的方法来转换成转录没有任何攻击性的话。

# 结论

让我们回顾一下你今天所学的内容。

本文在引言中强调了过滤脏话的标准工作流程。

然后，它进入设置和安装过程。这包括在 AssemblyAI 的平台上注册一个新的试用账户。

还讲述了上传本地音频文件以获得可访问 API 所需的步骤。接下来，它继续通过 API 调用触发转录过程的实现过程。

最后，展示了最终的转录输出和示例 JSON 输出。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [AssemblyAI —文档(脏话过滤)](https://docs.assemblyai.com/core-transcription#profanity-filtering)