# 如何检测语音中的话题

> 原文：<https://levelup.gitconnected.com/how-to-detect-topics-in-speech-f60552e7ce36>

根据 IAB 分类法确定相关主题

![](img/6f41cb6e112bf9151c3c6003203d0dc9.png)

由[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/variety?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

主题检测是一种发现文档集合背后的抽象主题的技术。将文本分类到特定的主题/领域主要是自然语言处理技术的一部分。

虽然没有关于主题应该如何分类的规则和规定，但是有一些你可以遵守的内容分类法。例如， [IAB 分类法](https://www.iab.com/guidelines/content-taxonomy/)提供了一个标准化的主题列表。目的是

> …提供额外的实用程序，旨在最大限度地降低内容分类信号可能被用来生成有关种族、政治、宗教或其他个人特征等可能导致歧视的敏感数据点的风险

请看下面的例子，它说明了 IAB 分类法中可用的几个类别:

```
MedicalHealth>DiseasesAndConditions>Injuries
MedicalHealth>DiseasesAndConditions>Injuries>FirstAid
MedicalHealth>DiseasesAndConditions>LungAndRespiratoryHealth
MedicalHealth>DiseasesAndConditions>MentalHealth
...
Style&Fashion>PersonalCare>Shaving
Style&Fashion>StreetStyle
Technology&Computing
Technology&Computing>ArtificialIntelligence
```

`>`之后的主题是指前一主题的副主题:

```
Topic1>Subtopic of topic 1>Subtopic of subtopic of topic 1
```

当涉及到音频数据时，在实际的主题检测过程之前，基础语音被转换成文本。因此，整个过程由两个阶段管道组成:

*   对语音进行语音识别以获得转录文本
*   转录上的主题检测以获得相关的标签/类别

让我们探索如何通过 AssemblyAI 的[语音转文本 API](https://www.assemblyai.com/) 轻松执行话题检测。

# 设置

在本教程中，实现基于 API 调用，并使用 Python 作为编程语言。但是，由于输入输出是 JSON，所以也可以很容易地用其他编程语言替换它。

## API 密钥

首先，前往 [AssemblyAI 的注册页面](https://app.assemblyai.com/signup)注册一个新的试用账户。它附带每月 3 小时的转录。登录平台，您应该会看到以下主页:

![](img/e5ad03dd2cbbe367f2a6961d6a457031.png)

作者图片

记下 API 键，因为您稍后将使用它来调用转录 API。

## 要求

因为需要进行 HTTP API 调用，所以我们需要一个 Python 包，而`requests`包是一个很好的选择。按照以下方式安装:

```
pip install requests
```

# 履行

假设您已经有一个链接到音频的可访问 URL，主题检测的步骤如下:

*   通过 POST HTTP 调用转录 API。这个调用需要`audio_url`，它指向你的音频文件的可访问 URL，还需要将`iab_categories`参数设置为`true`。
*   在第一次 API 调用之后，您必须对同一个转录 API 进行第二次调用，但这次是通过 GET。返回的响应将包含一个指示转录过程状态的`status`键。这个过程完成后，您应该会得到带有相应标签的转录文本。

## 对脚本 API 进行 POST 调用

我们将调用 [AssemblyAI 的脚本 API](https://docs.assemblyai.com/api-ref/v2-transcript) 。它接受 JSON 输入并返回 JSON 作为输出。让我们用下面的代码片段创建一个名为`transcribe.py.`的新 Python 文件:

需要注意的一点是，您必须将`iab_categories`参数设置为`true` ，否则返回的响应将不会包含关于主题检测的信息。

另外，请根据您现有的变量替换`api_key`和`audio_url`变量。运行以下命令启动转录过程:

```
python transcribe.py
```

它将返回一个 JSON 格式的输出，其中包含相当多的键值对:

```
{ "id": "xxxxxx"
  "language_model": "assemblyai_default",
  "acoustic_model": "assemblyai_default",
  "language_code": "en_us",
  "status": "queued",
  "audio_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c",
  "iab_categories": true, ...
}
```

不要被键值对的数量吓到，因为重要的键值对如下所示:

*   `id`—代表您的流程的唯一标识符。请记下这个 id，因为稍后将使用它来检索转录结果。
*   `status` —表示您的转录进度

如果返回的`status`是一个`error`，这意味着转录不成功。这可能是由于以下原因之一:

*   不支持的音频文件格式
*   音频文件不包含音频数据
*   音频文件太短(< 200 毫秒)
*   无法访问音频文件的 URL
*   API 端的错误

继续下一部分以检索转录结果。请注意，根据音频文件的长度，转录过程可能需要 10 分钟。

## 对脚本 API 进行 GET 调用

在同一个工作目录中创建一个名为`transcribe_file.py`的新文件，并添加以下代码:

它将调用相同的转录 API，但是通过 GET HTTP。像往常一样，替换`api_key`并使用从之前的 POST HTTP 调用返回的同一个`id`。

在您的终端上运行以下命令来检查转录的状态:

```
python transcribe_file.py
```

如果转录完成，它将返回 JSON 作为输出。输出包含`iab_categories_result`键，表示从语音中检测到的主题:

在撰写本文时，API 支持大约 698 种不同的标签。该密钥包含以下数据:

*   `status`:可以是`success`也可以是`unavailable`(表示故障)
*   `summary`:字典列表(文本、标签、时间戳)。`text`表示转录文本，而`timestamp`表示语音中转录的开始和结束。另一方面，`labels`包含一个字典列表(相关性，标签)。`label`表示基于 IAB 分类法检测到的主题，而`relevance`提供 0 到 1 之间的分数，表示标签与文本的相关程度。
*   `summary`:对于检测到的每个主题/标签，它将具有 0 到 1 之间的关于标签在整个音频文件中的相关程度的汇总分数。

在本例中，它返回以下与谈论校园枪击事件的音频剪辑相关的重要主题:

```
"NewsAndPolitics>Disasters": 1,                                   "MedicalHealth>DiseasesAndConditions>Injuries": 0.38230085372924805,                                   "Sports>CollegeSports": 0.3760283589363098,
```

## 上传本地音频文件(可选)

如果你没有任何可访问的 URL，你可以使用 AssemblyAI 提供的[上传 API](https://docs.assemblyai.com/reference#upload) 。

让我们用下面的代码创建一个名为`upload_file.py`的新文件来测试一下(相应地替换`filename`和`api_key`变量):

运行以下命令，开始将音频文件分块上传到服务器:

```
python upload_file.py
```

它将返回一个包含可访问 URL 的 JSON 输出:

```
{"upload_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c"}
```

调用转录 API 时只需使用`upload_url`的值，就可以了。

# 结论

让我们回顾一下你今天所学的内容。

本文简要介绍了语音中的话题检测和 IAB 分类法。

然后，介绍了如何在 AssemblyAI 平台上安装 requests 模块和注册新帐户。

随后，它解释了使 POST 和 GET API 调用转录可通过 URL 访问的音频文件的实现过程。除此之外，它还展示了上传本地音频文件以通过 URL 访问它所需的步骤。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [AssemblyAI —文档(主题检测)](https://docs.assemblyai.com/audio-intelligence#topic-detection-iab-classification)