# 如何从音频和视频中删除个人身份信息(PII)

> 原文：<https://levelup.gitconnected.com/how-to-remove-personally-identifiable-information-pii-from-audio-and-videos-7e61cb987976>

从转录文本中删除个人身份信息(PII)

![](img/72a3c2ab8ee2dd5c11d7ea942697ba94.png)

照片由[尤利娅·米哈洛夫](https://unsplash.com/@iulia_m?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/identity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在我之前的文章中，我已经介绍了如何将音频文件转录成文本。在本教程中，让我们进一步探讨如何从转录中删除个人身份信息(PII)。根据美国隐私和公开政府办公室的数据， [PII](http://www.osec.doc.gov/opog/privacy/PII_BII.html) 指的是

> …可用于区分或追踪个人身份的信息，如姓名、社会保险号、生物特征记录等。单独使用，或与其他个人信息或与特定个人相关或可相关的识别信息结合使用，如出生日期和地点、母亲的婚前姓氏等。"

自欧盟(EU)和欧洲经济区(EEA)通过《一般数据保护条例》( GDPR)以来，许多国家一直在采用类似的模式来管理可追踪到特定个人的个人数据的传输。

因此，如果您打算在应用程序中存储或传输这类个人数据，就必须从转录文本中删除它们。例如，给定以下转录文本:

```
John Doe is a well-known singer.
```

PII 的修订通常是通过用符号/哈希替换无名氏来完成的

```
#### ### is a well-known singer.
```

或者用表示相应实体名称的某种形式的字符串:

```
[PERSON_NAME] is a well-known singer.
```

通常，PII 修订按如下方式完成:

*   语音识别来获得转录
*   对转录文本进行自然语言处理以识别相关实体。这通常是通过命名实体识别来完成的
*   用散列/实体名称替换已识别实体的后处理

让我们来探索如何通过 AssemblyAI 提供的外部 API [语音转文本 API](https://www.assemblyai.com/) 来完成这项工作。

# 设置

本教程使用 Python 作为编程语言。但是，您可以根据需要随意修改它，因为核心实现是基于 API 调用的。同样，输入和返回的输出是基于 JSON 的。

强烈建议您在继续安装之前创建一个新的虚拟环境。

## 要求

package 是 Python 编程语言的一个用户友好的 HTTP 库。您可以按如下方式安装它:

```
pip install requests
```

## API 密钥

接下来，前往 [AssemblyAI 的注册页面](https://app.assemblyai.com/signup)并注册一个新的试用账户。对于新创建的帐户，它附带每月 3 小时的转录。访问主页时，您应该有以下用户界面:

![](img/e5ad03dd2cbbe367f2a6961d6a457031.png)

作者图片

记下 API 键，因为稍后调用转录 API 时会用到它。

# 履行

整个转录过程如下:

*   设置包含视频或音频文件的可访问 URL 的`audio_url`,并对转录 API 进行 POST 调用。此外，您需要将`redact_pii`参数设置为`true.`，它将通过 JSON 返回转录 id。
*   之后，对同一个转录 API 进行第二次 API 调用，但是改为通过 GET。您应该得到 JSON 输出，指示转录进度的状态。一旦完成，您将看到转录输出，其中的文本被替换为散列，如果其中存在任何 PII。

## 对脚本 API 进行 POST 调用

我们将为您拥有的每个音频文件对 [AssemblyAI 的脚本 API](https://docs.assemblyai.com/api-ref/v2-transcript) 进行一次 POST 调用。确保可以通过 URL 访问您的音频文件。使用以下代码在工作目录中创建一个名为`transcribe.py.`的新 Python 文件:

根据您所拥有的，简单地替换`api_key`和`audio_url`变量。接下来，运行以下命令开始转录:

```
python transcribe.py
```

它将返回 JSON 格式的输出，包含以下内容:

```
{ "id": "xxxxxx"
  "language_model": "assemblyai_default",
  "acoustic_model": "assemblyai_default",
  "language_code": "en_us",
  "status": "queued",
  "audio_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c",
  "filter_profanity": true, ...
}
```

最重要的键值对如下所示:

*   `id` —您的流程的唯一标识符。这是稍后调用 GET API 所必需的。
*   `status` —表示您的转录进度

如果您收到了一个`error` as 状态。说明转录不成功。这可能是由于以下原因之一:

*   不支持的音频文件格式
*   音频文件不包含音频数据
*   音频文件太短(< 200 毫秒)
*   无法访问音频文件的 URL
*   API 端的错误

您可以通过如下的`redact_pii_policies` 参数设置来预定义您想要的编校策略:

```
data = {
  "audio_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c",
  "redact_pii": True,
  "redact_pii_policies": ["drug", "injury", "person_name"]
}
```

转录 API 目前支持以下个人数据:

*   `medical_process`
*   `medical_condition`
*   `blood_type`
*   `drug`
*   `injury`
*   `number_sequence`
*   `email_address`
*   `date_of_birth`
*   `phone_number`
*   `us_social_security_number`
*   `credit_card_number`
*   `credit_card_expiration`
*   `credit_card_cvv`
*   `date`
*   `nationality`
*   `event`
*   `language`
*   `location`
*   `money_amount`
*   `person_name`
*   `person_age`
*   `organization`
*   `political_affiliation`
*   `occupation`
*   `religion`

有关这方面的更多信息，请查看[官方文档](https://docs.assemblyai.com/audio-intelligence#pii-redaction)。

## 自定义 PII 密文

此外，您可以定制和控制如何 PII 应该被取代。您所需要做的就是在调用 API 之前将`redact_pii_sub`参数包含在您的`data`字典中。

```
data = {
  "audio_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c",
  "redact_pii": True,
  "redact_pii_sub": "entity_name"
}
```

`redact_pii_sub:`有两种选择

*   `hash` —用哈希替换 PII(例如`1111–2222–3333–4444`将被替换为`####-####-####-####`)。这是默认值。
*   `entity_name` —用相关实体名称替换 PII(如`1111–2222–3333–4444`将替换为`credit_card_number`)

## 对脚本 API 进行 GET 调用

转录过程可能需要 10 分钟，具体取决于音频文件的长度。为了获得转录文本，您需要再次调用同一个转录 API，但是使用 GET HTTP。

创建一个名为`transcribe_file.py`的新文件，并用以下代码片段填充该文件:

记住相应地更换`id`和`api_key`参数。完成后，运行以下命令检查转录结果:

```
python transcribe_file.py
```

根据转录过程，您应该会看到不同的`status`键。完成后，您应该会得到 PII 的转录文本，并使用哈希/实体名称进行编辑。

## 拿到 PII 编辑过的音频文件

除此之外，AssemblyAI 确实提供了一个额外的 API，允许你获得一个版本的原始音频文件，其中 PII 被过滤掉了`beep`声音。

您需要在您的`data`字典中包含`redact_pii_audio`参数，并将其设置为`true`:

```
data = {
  "audio_url": "https://cdn.assemblyai.com/upload/d04b8505-3cf2-422a-a6e9-b9c6af7c8c6c",
  "redact_pii": True,
  "redact_pii_audio": True
}
```

接下来，对以下 URL 进行 GET HTTP 调用:

```
https://api.assemblyai.com/v2/transcript/<id>/redacted-audio
```

用您拥有的转录 id 替换`id`。它将返回以下输出，指示编辑的音频 url 和当前进度:

```
{
  "redacted_audio_url": "https://link-to-redacted-audio",
  "status": "redacted_audio_ready"
}
```

请注意，音频链接最多只能使用 30 分钟。之后，它将从服务器中删除，您将获得`400`响应代码。

# 结论

让我们回顾一下你今天所学的内容。

在简介中，本教程简要介绍了个人身份信息(PII)和一般数据保护法规(GDPR)。它还展示了一个关于 PII 修订如何影响最终转录文本的例子。

接下来，它继续在 AssemblyAI 平台上安装 requests 包和试用帐户注册。

在实施部分，它强调了用 PII 编校转录音频文件的工作流程。这包括某种形式的定制，您可以选择用散列或实体名称替换与 PII 相关的文本。

此外，它还解释了如何获得 PII 编辑的音频文件，过滤掉音频文件中的 PII 与`beep`的声音。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [汇编 AI —文档(PII 修订版)](https://docs.assemblyai.com/audio-intelligence#pii-redaction)