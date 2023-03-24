# 在环境变量中使用私钥

> 原文：<https://levelup.gitconnected.com/using-private-keys-in-environment-variables-460b8832b568>

![](img/2154dab79f8543bb98430f9f628d2323.png)

我们许多较新的 API 使用 jwt(JSON Web 令牌)进行认证，这很好。然而，由于我们的 jwt 是用私钥签名的，并且这些 jwt 包含换行符，这有时会使我们处理凭证的一些常用方法出错！

这篇文章将展示如何在环境变量中使用私钥，并展示一个使用 Vonage Voice API 和 Netlify 函数的例子。

# Vonage API 帐户

要完成这个教程，你需要一个 [Vonage API 账户](http://developer.nexmo.com/ed?c=blog_text&ct=2020-07-29-using-private-keys-in-environment-variables)。如果你还没有，你可以今天[注册](http://developer.nexmo.com/ed?c=blog_text&ct=2020-07-29-using-private-keys-in-environment-variables)并开始用免费信用点数进行构建。一旦你有了一个帐户，你可以在 [Vonage API 仪表板](http://developer.nexmo.com/ed?c=blog_text&ct=2020-07-29-using-private-keys-in-environment-variables)的顶部找到你的 API 密匙和 API 秘密。

然后创建一个具有语音功能的应用程序；下一步您将需要应用程序 ID 和私钥文件。

您可以使用您的[帐户控制面板](https://dashboard.nexmo.com)来完成此部分，也可以像这样使用 CLI:

该命令将打印应用程序 ID，并将私钥写入虚构的名称为`private.key`的文件。这两个项目将在下一步中使用。

# 为什么不上传文件呢？

数据在`private.key`里吧？为什么我们不能用磁盘上的这个文件呢？

对于本地应用程序，我们绝对可以，您将看到我们的许多示例应用程序都是这样做的。

然而对于一个“真正的”应用程序来说，`private.key`文件不是应用程序的一部分，不能像其他文件一样被处理。

永远不要将`private.key`文件添加到源代码控制中；它就像你的账户密码一样是一个秘密凭证。也有可能在不同的平台上对这个应用程序使用不同的凭据集，比如您的本地开发平台，或者当应用程序被部署到一个临时或实时平台时。

考虑到这一点，我需要一种方法来安全地将这个私钥作为一个字符串来处理。

# 创建基本的语音通话应用程序

一个很好的方法是创建一个利用 Voice API 的应用程序。我不认为我会厌倦通过编程让我的电话响！

今天的例子使用 Node.js，通过简单的文本到语音转换通知打电话。

在我写代码之前，我将安装 [Nexmo 节点 SDK](https://github.com/nexmo/nexmo-node) 依赖项:

现在是代码的时候了！对于这样一个简单的应用程序，我通常只是将整个内容放入`index.js`中，就像这样:

看一下代码示例，您会发现有相当多的地方使用了`process.env.*`来引用环境变量。

使用环境变量是让代码在多个地方都能愉快运行的好方法，因为在每个场景中，它只是四处看看并使用所提供的值。

特别是，我更喜欢云平台的环境而不是配置文件，在那里我可以从源代码控制进行部署，但绝不会在那里包含凭证。

对于本地平台，我使用 [dotenv](https://github.com/motdotla/dotenv) 从配置文件中加载环境变量。在使用 dotenv 时，或者对于 Netlify 和 Glitch 等一些云平台，不可能对环境变量使用多行值。为了解决这个问题，我对环境变量使用 base64 编码的值，并让我的应用程序在使用它们之前解码这些值。

# 准备环境变量

对于本地使用的`dotenv`，或者对于不处理多行环境变量的平台，我准备了一个`.env`文件，如下所示:

> 如果您通过另一种方式设置环境变量，比如通过 web 界面，您也可以在那里重用这些值。

这些值应该是:

*   `TO_NUMBER`打电话的号码，我用的是我的手机号码
*   `NEXMO_NUMBER`我在 Vonage 平台上拥有的号码
*   我在第一部分创建的应用程序的 ID
*   `NEXMO_APPLICATION_PRIVATE_KEY64`我的`private.key`文件的内容，base64 编码

我用来获取 base64 值的命令是:

# 把它们放在一起

通过用换行符对环境变量进行编码，我们可以安全地将它们作为字符串进行传输。使用上面的配置和我们之前带来的`index.js`文件，我可以在本地运行我的代码(通过将`dotenv`添加到我的应用程序中)，或者在任何其他平台上运行。

这是一件小事，但我在处理私钥文件时会在意想不到的地方遇到它，所以我肯定自己会引用这个帖子。

*原载于*[*https://www . NEX mo . com/blog/2020/07/29/using-private-keys-in-environment-variables*](https://www.nexmo.com/blog/2020/07/29/using-private-keys-in-environment-variables)*。*