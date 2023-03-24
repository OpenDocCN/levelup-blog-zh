# 带有情感分析的语音识别

> 原文：<https://levelup.gitconnected.com/speech-recognition-with-sentiment-analysis-894e6a321112>

将言语分为积极的、消极的或中性的

![](img/e628489c8674ce62fe86377c0ee69777.png)

[腾雅特](https://unsplash.com/@tengyart?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/emotion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

今天的主题是语音数据的情感分析。供您参考，情绪分析是一种识别、提取和量化积极、消极或中立数据的技术。它主要用于文本数据，以获得有用的见解，帮助企业监控客户反馈中的产品情绪。

语音数据情感分析的使用案例包括:

*   识别客户和代理对话的情绪，以促进更好的客户服务体验
*   在虚拟会议中获得更好的洞察力，用于产品开发和营销目的
*   在新闻报道或采访中定义言语的本质及其情感

尽管情感分析通常依赖于文本数据，但是您也可以利用两阶段管道对语音数据执行情感分析:

*   运行自动语音识别以获得相应的转录
*   从转录文本中获得情感

与其麻烦地设置管道，不如让我们探索一下如何通过调用 AssemblyAI 提供的外部[语音转文本 API](https://www.assemblyai.com/) 来轻松完成。最新版本带有情绪分析功能，帮助你一举两得。

让我们继续下一部分，开始安装必要的模块。

# 设置

## 要求

这个项目使用`requests`包进行 HTTP API 调用。默认情况下，它是随您的环境一起预安装的。如果不是这样，请按如下方式安装:

```
pip install requests
```

## API 密钥

除此之外，您还需要在此注册一个[新的免费试用账户。试用帐户附带每月 3 小时的转录，这对于本教程来说已经足够了。](https://app.assemblyai.com/signup)

![](img/38bbdb0f000ead04df1db8afd5ac03bd.png)

作者图片

请记住记下 API 键，因为稍后以编程方式调用 API 时会用到它。

# 履行

转录过程包括以下步骤:

*   上传一个本地音频文件到 AssemblyAI 的服务器。它将为您返回一个可访问的 URL
*   用`audio_url`和`sentiment_analysis`参数调用转录 API (POST call)
*   调用转录 API (GET call)来获得最终的转录结果

## 上传本地音频文件(可选)

这一步是可选的，主要用于上传本地音频文件以获得可访问的 URL。如果你已经有一个网址，请跳到下一节。

在工作目录中创建一个名为`upload_file.py`的新文件。在其中追加以下代码:

用你项目中的变量替换掉`api_key`和`filename`变量。

运行以下命令将音频文件上传到服务器:

```
python upload_file.py
```

它将返回一个 JSON 输出，表示音频文件的可访问 URL:

```
{“upload_url”: “https://cdn.assemblyai.com/upload/ccbbbfaf-f319-4455-9556-272d48faaf7f"}
```

## 对脚本 API 进行 POST 调用

完成后，创建一个名为`transcribe.py.`的文件，我们将用下面的代码调用[官方脚本 API](https://docs.assemblyai.com/api-ref/v2-transcript) :

确保在调用 API 之前已经将`sentiment_analysis`参数设置为`True`。此外，您可以为`audio_url`使用自己的可访问 URL，如上面的示例代码所示。

运行以下命令来触发转录过程:

```
python transcribe.py
```

输出是 JSON 格式的，包含相当多的键值对。最重要的关键字如下:

```
{"id": "oe9eg384ft-4f9e-4151-81f8-0697a9d128f0", ...
"status": "queued" ...}
```

*   `id` —代表流程的唯一标识符。您将在稍后调用 GET API 时使用它
*   `status` —表示您的转录进度

如果您收到的状态为`error`，可能是由于以下原因之一:

*   不支持的音频文件格式
*   音频文件不包含音频数据
*   音频文件太短(< 200 毫秒)
*   无法访问音频文件的 URL
*   API 端的错误

## 对脚本 API 进行 GET 调用

为了获得情绪分析附带的转录文本，您需要进行另一个 API 调用。请注意，根据文件的长度，转录可能需要 10 分钟。

使用以下代码创建一个名为`transcribe_file.py`的新 Python 文件:

像往常一样，相应地更换`api_key`和`id`变量。接下来，运行以下命令:

```
python transcribe_file.py
```

输出 JSON 包含一个额外的`sentiment_analysis_results`键，它表示对应于语音的情感分析输出。该键包含具有以下项目的字典列表:

*   `text` —正在分析的句子的转录文本
*   `start` —抄本中文本的开始时间戳(以毫秒为单位)
*   `end` —抄本中文本的结束时间戳(以毫秒为单位)
*   `sentiment` —检测到的情绪`POSITIVE`、`NEGATIVE`或`NEUTRAL`
*   `confidence` —检测到的情感的置信度得分

看看下面的输出示例:

```
...
{
      "text": "And and for people to expose themselves to being rejected on TV.",
      "start": 2372,
      "end": 6574,
      "sentiment": "NEGATIVE",
      "confidence": 0.7591296434402466
},
```

# 结论

让我们回顾一下你今天所学的内容。

本教程涵盖了情感分析及其用例背后的基本概念。

然后，它提供了安装必要模块的分步指南，以及在 AssemblyAI 平台中设置新的免费试用帐户以获取 api 密钥。

随后，它解释了上传音频文件以获得可访问的 URL。当调用转录后 API 时，URL 是参数的一部分。最后，调用转录 GET API 来获得最终结果，该结果伴随着演讲的情绪。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [AssemblyAI —引入情感分析](https://www.assemblyai.com/blog/introducing-sentiment-analysis/)
2.  [AssemblyAI —文档(情感分析)](https://docs.assemblyai.com/audio-intelligence#sentiment-analysis)