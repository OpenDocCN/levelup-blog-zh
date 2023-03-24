# 亚马逊苏美尔主机:如何降低你的亚马逊波利成本

> 原文：<https://levelup.gitconnected.com/amazon-sumerian-hosts-how-to-reduce-your-amazon-polly-cost-eb13db90a47d>

![](img/2f7178590342646f87fe730bb6c9ab60.png)

克里斯·福吉在[艺术站](https://www.artstation.com/artwork/vvv9A)拍摄的照片

> 本文的目标读者是那些已经熟悉 Amazon Sumerian 主机并且正在寻找一种方法来减少 AWS 账单(特别是 Amazon Polly)同时保持他们的交互式和沉浸式体验的用户。

# 介绍

[亚马逊苏美尔主机](https://github.com/aws-samples/amazon-sumerian-hosts)是由 AWS 发布的开源 GitHub 知识库，允许开发者将 3D 虚拟角色集成到他们基于网络的(ThreeJS 或 BabylonJS)交互体验中。虚拟人物被称为亚马逊苏美尔人的主人。

# 技术

亚马逊苏美尔主机大多由亚马逊 Polly(亚马逊的文本到语音转换服务)驱动。有了 Amazon Polly，你可以通过 [SSML](https://docs.aws.amazon.com/polly/latest/dg/ssml.html) 让主机说 [29 种不同的语言](https://docs.aws.amazon.com/polly/latest/dg/voicelist.html)(在编写的时候)有各种各样的控制，允许开发人员精确地配置他们想要一个句子怎么说。要了解更多关于亚马逊苏美尔主机的信息，请访问 [GitHub 知识库](https://github.com/aws-samples/amazon-sumerian-hosts)。

由于大多数交互式 web 应用程序都是基于客户端的，这意味着浏览器呈现的体验除了托管和交付成本之外，与 Amazon Sumerian 主机相关的唯一成本就是对 Amazon Polly 的 API 调用。

Amazon Polly 是一种无服务器的现收现付服务，这意味着每月会根据你处理的文本字符数来计费。

> 亚马逊 Polly 的标准语音价格为每 100 万个字符 4.00 美元，用于语音或语音标记请求(当在免费层之外时)。亚马逊 Polly 的神经语音的价格为每 100 万个字符 16.00 美元，用于请求语音或语音标记(当在免费层之外时)。引用自[亚马逊 Polly 的定价页面](https://aws.amazon.com/polly/pricing/)。

每当开发人员请求亚马逊苏美尔人主机说出一条消息时，它就执行 2 次 API 调用。

1.  第一个 API 调用是合成文本(将文本转换为音频文件)并检索要播放的 MP3 音频文件。
2.  第二个 API 调用是检索文本的*语音标记*。

> *语音标记*是描述您合成的语音的元数据，例如一个句子或单词在音频流中的开始和结束位置。当您为文本请求语音标记时，Amazon Polly 会返回这些元数据，而不是合成语音。通过将语音标记与合成语音音频流结合使用，您可以为您的应用程序提供增强的视觉体验。引用自[亚马逊 Polly 的文档](https://docs.aws.amazon.com/polly/latest/dg/speechmarks.html)。

亚马逊苏美尔人主机利用语音标记为虚拟角色提供口型同步能力。

因此，在估算你的 AWS 成本时(特别是围绕 Amazon Polly)，你必须考虑到这样一个事实，即每条语音信息都要花费你两次成本。

# 成本公式

以下是使用亚马逊苏美尔主机估算亚马逊 Polly 成本时使用的公式:

> A mazon Polly 月成本= **【用户数】** X 口语字符数 X 每个字符的价格 X API 调用数

上述公式计算如下:

1.  应用程序的估计用户总数乘以
2.  说出的字符总数(请记住，如果您支持多种语言并允许您的用户在语言之间切换，您可能希望通过将所有语言的所有字符相加来计算最坏情况的估计值，但只计算唯一消息的字符数，因为亚马逊苏美尔主机会在内存中缓存响应，以防主机多次说出相同的消息)，乘以
3.  每个角色的价格(如果使用标准声音，每个角色 0.000004；如果使用神经声音，每个角色 0.000016)，乘以
4.  API 调用的数量是 2(因为每条消息需要对 Amazon Polly 进行两次 API 调用，一次用于音频文件，另一次用于语音标记)

## 场景 1

*   *用户数量:10*
*   *字符数:10000*

每月标准总额:10 x 10，000 x 0.000004 x 2 = 0.8 美元

神经每月总数:10 x 10，000 x 0.000016 x 2 = 3.2 美元

## 场景 2

*   *用户数量:100 人*
*   *字符数:25000*

每月标准总额:100 x 25，000 x 0.000004 x 2 = 20 美元

每月神经总数:100 x 25，000 x 0.000016 x 2 = 80 美元

## 场景 3

*   *用户数量:1000 人*
*   *字符数:25000*

每月标准总额:1000 x 25000 x 0.000004 x 2 = 200 美元

神经系统每月总数:1000 x 25000 x 0.000016 x 2 = 800 美元

从上面的场景中你可以清楚地看到，Amazon Polly 的成本会随着用户和角色的增加而增加。如果我们对结果进行预处理和缓存，我们的成本将会显著降低一个用户数量的系数，这样就更容易管理，如下图所示。

## 场景 3(带预处理)

*   用户数量:1
*   字符数:25，000

每月标准总额:1 x 25，000 x 0.000004 x 2 = 0.2 美元

神经系统每月总数:1 x 25，000 x 0.000016 x 2 = 0.8 美元

> 注:上述每月总额不包括微不足道的 S3/CloudFront 存储和转让费。

# 介绍亚马逊苏美尔波利优化

我已经派生了现有的亚马逊苏美尔主机包，并对其进行了一点修改，以通过允许开发人员向 **TextToSpeechFeature.play** 函数提供两个可选参数来直接使用提供的音频文件和语音标记来完成上述任务。

GitHub 包可以在[这里](https://github.com/ranilian/amazon-sumerian-hosts-polly-optimized)找到。

## 我如何安装它？

> npm 安装亚马逊-苏美尔-主机-波利-优化

## 它是如何工作的？

将两个可选标志传入函数调用，如下所示:

```
/**
* ... initialize host here ...
*/

/** 
 * Make sure to replace text with a unique string per audioURL and speechMarksJSON, you can keep this as the text you used to play but note that it actually won't be played as the preprocessed audio and speechMarks will be played instead.
 * Make sure to replace speechMarksJSON with the preprocessed SpeechMarks JSON Array.
 * Make sure to replace audioURL with a Blob URL of the preprocessed Audio file
*/
host.TextToSpeechFeature.play(text, {
  SpeechMarksJSON: speechMarksJSON,   
  AudioURL: audioURL
});
```

举个例子，让主持人说出下面的 SSML:

```
<speak>Hello, I am a Sumerian Host powered using a preprocessed MP3 file and SpeechMarks.</speak>
```

我已经预处理了必要的音频文件和演讲标记，并将它们放在 GitHub 存储库中的 examples/assets/preprocessed/文件夹下，因此您应该能够在您的代码中使用它们。

```
// Specify local paths
// Make sure to update them to where you copy them into under your public/root folder
const speechMarksPath = './examples/assets/preprocessed/exampleSpeechMark.json';
const audioPath = "./examples/assets/preprocessed/exampleAudio.mp3";

// Fetch resources
const speechMarksJSON = await(await fetch(speechMarksPath)).json();
const audioBlob = await(await fetch(audioPath)).blob();

// Create Audio Blob URL
const audioURL = URL.createObjectURL(audioBlob);

// Play speech with local assets
host.TextToSpeechFeature.play('Hello, I am a Sumerian Host powered using a preprocessed MP3 file and SpeechMarks', {
  SpeechMarksJSON: speechMarksJSON,
  AudioURL: audioURL
});
```

如果没有指定两个属性`SpeechMarksJSON`和`AudioURL`，它将像以前一样工作(与 Polly 交互)，但如果你经常使用静态文本，我强烈建议预处理语音标记和音频文件，并将其缓存在 S3/CloudFront 上，以优化成本。

## 我如何预处理必要的文件？

我强烈建议对文件进行预处理，并存储在亚马逊 S3 上(并通过 CloudFront 提供)，或者作为 web 应用程序的一部分提供。

为此，有两个主要文件需要预处理。

1.  音频文件:音频文件将由主机播放，可以使用 AWS SDK 进行预处理，也可以直接从 Amazon Polly AWS 控制台下载 MP3 文件。
2.  SpeechMarks:定义 viseme SpeechMarks 的 JSON 数组，这些 vise me speech marks 使主持人的嘴形与发出的声音相匹配。您可以通过在 AWS Lambda NodeJS 函数上运行代码或在本地运行 NodeJS 函数，使用 Amazon Sumerian Hosts repo 中已经存在的代码对它们进行预处理。代码本身粘贴在下面:

```
const synthesizeSpeechmarks = (text, voiceId) => {
  console.log(`Synthesizing speechmarks for ${text} with voice: ${voiceId}`);
  const params = {
    OutputFormat: "json",
    SpeechMarkTypes: ["sentence", "ssml", "viseme", "word"],
    SampleRate: "22050",
    Text: text,
    TextType: "ssml",
    VoiceId: voiceId,
  };
  return polly
    .synthesizeSpeech(params)
    .promise()
    .then((result) => {
      // Convert charcodes to string
      const jsonString = JSON.stringify(result.AudioStream);
      const json = JSON.parse(jsonString);
      const dataStr = json.data.map((c) => String.fromCharCode(c)).join("");

      const markTypes = {
        sentence: [],
        word: [],
        viseme: [],
        ssml: [],
      };
      const endMarkTypes = {
        sentence: null,
        word: null,
        viseme: null,
        ssml: null,
      };

      // Split by enclosing {} to create speechmark objects
      const speechMarks = [...dataStr.matchAll(/\{.*?\}(?=\n|$)/gm)].map(
        (match) => {
          const mark = JSON.parse(match[0]);

          // Set the duration of the last speechmark stored matching this one's type
          const numMarks = markTypes[mark.type].length;
          if (numMarks > 0) {
            const lastMark = markTypes[mark.type][numMarks - 1];
            lastMark.duration = mark.time - lastMark.time;
          }

          markTypes[mark.type].push(mark);
          endMarkTypes[mark.type] = mark;
          return mark;
        }
      );

      // Find the time of the latest speechmark
      const endTimes = [];
      if (endMarkTypes.sentence) {
        endTimes.push(endMarkTypes.sentence.time);
      }
      if (endMarkTypes.word) {
        endTimes.push(endMarkTypes.word.time);
      }
      if (endMarkTypes.viseme) {
        endTimes.push(endMarkTypes.viseme.time);
      }
      if (endMarkTypes.ssml) {
        endTimes.push(endMarkTypes.ssml.time);
      }
      const endTime = Math.max(...endTimes);

      // Calculate duration for the ending speechMarks of each type
      if (endMarkTypes.sentence) {
        endMarkTypes.sentence.duration = Math.max(
          0.05,
          endTime - endMarkTypes.sentence.time
        );  
      }
      if (endMarkTypes.word) {
        endMarkTypes.word.duration = Math.max(
          0.05,
          endTime - endMarkTypes.word.time
        );
      }
      if (endMarkTypes.viseme) {
        endMarkTypes.viseme.duration = Math.max(
          0.05,
          endTime - endMarkTypes.viseme.time
        );
      }
      if (endMarkTypes.ssml) {
        endMarkTypes.ssml.duration = Math.max(
          0.05,
          endTime - endMarkTypes.ssml.time
        );
      }

      return speechMarks;
    });
};
```

# 结论

如果 Amazon Polly 正在处理大量静态文本，或者有大量用户与 AWS 应用程序交互，请尝试寻找缓存尽可能多的数据的方法，以节省时间和金钱！

谢谢！

# 你可能喜欢的其他文章

[](/how-to-publish-your-unity3d-html5-application-or-game-to-aws-3bb053b59d21) [## 如何将您的 Unity3D HTML5 应用程序或游戏发布到 AWS

### 本文的目标读者是已经熟悉 Unity3D WebGL 并且正在寻找一个…

levelup.gitconnected.com](/how-to-publish-your-unity3d-html5-application-or-game-to-aws-3bb053b59d21) [](/aws-cdk-for-beginners-e6c05ad91895) [## AWS 初学者 CDK

### 本文的目标读者是不熟悉亚马逊 Web 服务(AWS)或 AWS 云的人…

levelup.gitconnected.com](/aws-cdk-for-beginners-e6c05ad91895)