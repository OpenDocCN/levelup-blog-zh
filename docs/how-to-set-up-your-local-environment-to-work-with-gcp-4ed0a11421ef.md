# 如何设置您的本地环境以便与 GCP 一起工作

> 原文：<https://levelup.gitconnected.com/how-to-set-up-your-local-environment-to-work-with-gcp-4ed0a11421ef>

## 学会在几分钟内处理 GCP 本地认证

![](img/1f514f53ceef5306903e5a8192f91b60.png)

图片由[凯蒂尔怀特 91](https://pixabay.com/illustrations/cloud-computer-backup-technology-3998880/) 在皮克斯拜拍摄

如果你在谷歌云平台(GCP)上工作，适当地设置你的本地环境可以让你的生活更容易。处理所有本质的认证问题可能会很头疼，尤其是当你刚刚开始与 GCP 合作的时候。不要担心，在这篇文章中，我们将介绍 gcloud CLI、Google 客户端库和 Docker 的常用设置，这些设置对于您的日常开发工作至关重要。我们还将介绍如何在本地使用服务帐户。

## 安装谷歌云 SDK

请按照[这个官方链接](https://cloud.google.com/sdk/docs/install-sdk#deb)安装 Google Cloud SDK，它提供了不同操作系统的分步说明。

## 验证 Google Cloud SDK

如果是第一次使用 Google Cloud SDK，需要初始化 gcloud CLI:

```
$ gcloud init
```

按照控制台上的说明创建新配置。如果您已经为某个帐户创建了配置，但需要以另一个帐户的身份登录，您可以运行以下命令以另一个帐户的身份登录:

```
$ gcloud auth login
```

浏览器将为这两个命令打开，你将被要求登录到与 GCP 项目相关的谷歌帐户。

登录后，您可以使用以下命令检查您的身份验证:

```
$ gcloud auth list
```

现在，您应该能够查看关于 GCP 的资源了。例如，您可以使用`[gsutil](https://lynn-kwong.medium.com/how-to-use-gsutil-and-python-to-deal-with-files-in-google-cloud-storage-fc4f430b3b28)`命令检查 Google 存储桶:

```
$ gsutil ls
```

## 验证 Google 客户端库

以上认证方式只认证 gcloud CLI，不认证 Google 客户端库。我们还需要验证需要与 Google APIs 交互的应用程序。为此，我们需要运行[这个命令](https://cloud.google.com/sdk/gcloud/reference/auth/application-default/login):

```
$ gcloud auth application-default login
```

浏览器将再次打开，您需要选择 Google 帐户进行身份验证。注意，该命令独立于上面的`gcloud auth`或`gcloud auth login`命令。在对 gcloud CLI 进行身份验证后，您始终需要显式运行此命令。

在使用上面的命令对应用程序进行身份验证之后，您应该能够使用 Google 客户端库访问您的资源。例如，让我们用 Python 列出所有的 Google 存储桶:

## 鉴定码头工人

到目前为止，如果你试图从 Google Artifact/Container 注册表中拉出一些图像，你会有权限问题，因为你需要显式地认证 Docker，默认认证和默认应用程序认证都不会起作用。

在我们开始之前，知道在 Linux 上安装了 [Snap](https://en.wikipedia.org/wiki/Snap_(software)) 的 Docker 可能无法使用下面显示的命令正常工作是非常重要的。最好从[官方网站](https://docs.docker.com/engine/install/ubuntu/)安装 Docker，而不是用 Snap 甚至“handy”`[get_docker](https://get.docker.com/)`[脚本](https://get.docker.com/)。

验证 Docker 的命令是:

```
$ gcloud auth configure-docker eu.gcr.io
```

注意最好也指定区域注册中心，否则，`docker build`会很慢，因为您需要处理所有可用的注册中心，即使它们没有被使用。

现在，如果您被授予了这样做的权限，您将能够从工件/容器注册表中拉出/推入。

## 使用服务帐户

如果您需要手动检查或更改一些资源，您应该使用您的个人谷歌帐户，该帐户已添加到您自己或您的小组的 GCP 项目中。然而，如果您需要在代码中以编程方式做一些事情，这通常意味着使用 Google APIs，如[存储](https://lynn-kwong.medium.com/how-to-use-gsutil-and-python-to-deal-with-files-in-google-cloud-storage-fc4f430b3b28)、[发布/订阅](/how-to-use-google-pub-sub-to-build-an-asynchronous-messaging-system-in-python-3b43094627dc)、[日志](/how-to-write-logs-to-google-cloud-logging-in-python-46e7b514c60b)等，最好使用通常具有有限和专用权限/角色的服务帐户。

如果您的应用程序托管在计算引擎、应用引擎、云运行等 GCP 资源上。您可以将具有特定角色的服务帐户附加到您的目标服务，而不需要担心其他任何事情。

然而，当您在本地开发代码时，使用服务帐户就不那么简单了。如果你的个人 Google 账户有足够的权限来执行编程动作，你可以运行如上所示的`gcloud auth application-default login`来验证你的应用程序，然后可以直接在你的代码中使用 Google APIs。

另一方面，如果你的个人谷歌帐户权限有限，你必须使用一个[服务帐户](https://console.cloud.google.com/iam-admin/serviceaccounts)，你可以下载(或要求…)服务帐户的 JSON 密钥，并使用它在你的应用程序中认证谷歌客户端库。尽管如果您的应用程序没有托管在 GCP 基础设施上，建议使用"[Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation?_ga=2.115992518.-1545168142.1635322660)"来验证您的应用程序，但使用 JSON 密钥仍然很常见，因为它们更方便。但是，您必须非常小心您的 JSON 键，不要意外地暴露它们。最重要的是，永远不要将它们添加到您的公共存储库中。这是因为拥有这些 JSON 密钥的任何人都可以直接访问和操纵您的 GCP 资源！

服务帐户的 JSON 键的一个实际用例是用于一些第三方 CI/CD 管道，如 GitLab，其中您需要构建一个 Docker 映像并将其推送到 GCP 工件/容器注册表，这将在后面的帖子中介绍。

要使用服务帐户对您的应用程序进行身份验证，您需要运行以下命令，将 JSON 键的路径设置为预定义的环境变量`GOOGLE_APPLICATION_CREDENTIALS`:

```
$ export **GOOGLE_APPLICATION_CREDENTIALS**=path/to/JSON_key.json
```

请注意，环境变量的名称必须与此处显示的名称完全一致。它将被谷歌客户端库自动使用。此外，需要注意的是，该环境变量的优先级高于由`gcloud auth application-default login`设置的凭证，这意味着您可以使用自己的 Google 帐户进行默认应用程序认证，并在需要时为不同的应用程序使用不同的服务帐户。

有了这个环境变量集，您不再需要在代码中显式地处理服务帐户。

在本帖中，我们介绍了一些常见的设置，这些设置对于设置您的本地环境以使用 GCP 非常重要。如果你的开发者的 Google 帐户有足够的权限，通常就是这样，那么设置应该相当简单，你不需要为服务帐户费心。尽管如此，当真正需要一个服务帐户时，您只需下载它的 JSON 密钥，并将本地路径设置为环境变量`GOOGLE_APPLICATION_CREDENTIALS`，然后一切都会如预期的那样工作。

相关文章:

*   [如何使用 gsutil 和 Python 处理 Google 云存储中的文件](https://lynn-kwong.medium.com/how-to-use-gsutil-and-python-to-deal-with-files-in-google-cloud-storage-fc4f430b3b28)
*   [如何用 Python 写日志到 Google 云日志](/how-to-write-logs-to-google-cloud-logging-in-python-46e7b514c60b)

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)