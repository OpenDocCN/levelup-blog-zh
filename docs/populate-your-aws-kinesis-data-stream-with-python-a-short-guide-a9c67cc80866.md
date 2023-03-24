# 用 Python 填充 AWS Kinesis 数据流:一个简短的指南

> 原文：<https://levelup.gitconnected.com/populate-your-aws-kinesis-data-stream-with-python-a-short-guide-a9c67cc80866>

![](img/ea978a8dcaa9e0214903aab912376011.png)

由 [Pablo Molina](https://unsplash.com/@pmpablo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你正在使用 AWS(就像我经常使用的那样)，你可能对 AWS 的 Kinesis 队列服务很熟悉。[据亚马逊网站](https://aws.amazon.com/kinesis/)报道，“亚马逊 Kinesis 数据流是一种可扩展且持久的实时数据流服务，可以每秒钟从数十万个来源中持续捕获数千兆字节的数据。”更重要的是，Kinesis 可以与亚马逊的其他数据驱动服务、Lambda 等无服务器应用程序、数据仓库等无缝协作。

在本文中，我将提供一个简短的代码示例，说明如何将消息推入 Kinesis 数据流，并解释如何做到这一点，尽管我不认为有太多需要解释的，因为它真的非常简单！

# 但是首先，让我们来看看你需要做些什么！

您将需要以下各项(显然，除了连接到互联网之外！)连接到 Kinesis:

1.  你的 AWS 证书。自然地，当你连接到你的 Kinesis 流时，你会想要一些安全，所以随着安全而来的是凭证。对于我们的例子，我们将使用一个 API 密钥和秘密。您应该能够通过 AWS IAM 服务生成这些。
2.  以下软件包安装在您的虚拟环境或 Docker 容器中(如果您使用 Docker):

```
json
uuid
boto3
```

1.  `json`是处理 web 开发的主要工具，如果你正在阅读这篇文章，你很可能已经遇到过这个包。
2.  在许多处理数据库的设置中，T1 是一个非常有用和常用的包。如果你已经这样做了，你可能已经遇到了这个。
3.  `boto3`是亚马逊网络服务的助手包，让连接到它的各种服务变得轻而易举。如果你和 Kinesis 一起工作或者想和 Kinesis 一起工作，不要重新发明轮子；已经拿到`boto3`了！

就这样…

# 我们来看看代码！

让我们快速看一下助手类的代码:

这个助手主要帮助连接到 Kinesis，并添加一个记录。还有什么好说的？

使用这个助手将记录上传到 Kinesis 时，有几件事你需要注意:

1.  由于`boto3`助手不会自动将 python 字典编码成字符串形式，助手将运行`json.dumps`将其转换成 fit。不要自己编码，因为这样会导致不可思议。
2.  在这一点上，如果你正在使用一个服务或运行某种处理来读取 Kinesis 数据，不要忘记解码数据，因为它是 base64 编码的。这相当简单，但超出了本文的范围，所以我们在这里跳过它。

你如何使用这个助手将记录上传到 Kinesis？简单！

```
from kinesis_helper import KinesisStreamdata = {'my': 'data'}
stream = KinesisStream('stream-name')
stream.send_stream(data=data)
```

就这样了！感谢您通读这篇文章。

如果你也必须使用 Elasticsearch，别忘了看看我写的关于如何使用它的文章！ [我还发表了一个关于优化 Django 的迷你系列文章，所以如果你想提高你的 Django 服务器的性能，就去看看吧！](https://angysmark.medium.com/)