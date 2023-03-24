# 管理您的地形状态

> 原文：<https://levelup.gitconnected.com/manage-your-terraform-state-1be1eb8e16f4>

## 或者我如何学会不再担心 DevOps

![](img/61410242b7b1907007962b1a2703057a.png)

安德雷亚·波帕在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我过去常常避免任何类型的 DevOps 工作。我不知道也不想学习如何配置服务器、管理负载平衡器或扩展数据库。我只想为最终用户编码。

随着无服务器架构的兴起，我对 DevOps 的恐惧开始减少。我的许多痛苦都消失了。

然后我读到了[infra structure as code(IaC)](https://en.wikipedia.org/wiki/Infrastructure_as_code)，并迷上了 DevOps 可以更容易管理和更有趣的想法，因为所有这些都只是代码。

我开始学习把 Terraform 作为我事实上的 IaC 工具，因为我们在我工作的地方使用它。我可以通过运行一个命令来定义我的资源并进行部署，而不是在 AWS 控制台中四处点击。为了实现完全自动化，我可以在每次推送回购时通过 CI/CD 管道运行它。

**有了 Terraform，DevOps 对我来说变得更加容易和容易。**

我唯一的问题是所有的东西都在我的本地机器上，我不能和我的团队或者其他人分享。我考虑过使用源代码控制，但是由于 Terraform 管理状态的方式，我可能会遇到一些安全问题。

Terraform docs 提供了一个实现后端的解决方案，在那里我可以[存储](https://www.terraform.io/docs/state/index.html)和[管理](https://www.terraform.io/docs/state/locking.html)我的基础设施的状态。

# 我该如何开始？

*以下是我使用 Terraform 设置项目的首选方式。*

## 先决条件

请确保您已完成以下步骤:

*   安装 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 和[配置](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)它
*   安装 [Terraform 0.13 或以上](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started)

## 项目结构

*您可能已经习惯了其他的代码组织方式，并且可以根据您的偏好或需求进行调整。*

```
infrastructure
│   
└─── global
│   │  
│   └─── state_local
│   │   │ main.tf
│   │   │ output.tf
│   │   │ variables.tf 
│   │  
│   └─── state_remote
│       │ main.tf
│       │ output.tf
│       │ variables.tf
│
└─── dev
│
└─── modules
backend.hcl
global.tfvars
setup.sh
teardown.sh
```

## 一切是什么意思？

`infrastructure`中的`dev`文件夹是您项目中的所有环境。可能只有一个、五个或十个。

`modules`是你定义项目基础设施的地方:木桶、VPC、兰姆达斯等等。这也支持项目中的代码可重用性。

`global`文件夹将创建您的远程后端，在 S3 上存储您的状态，并管理 DynamoDB 上的锁，以防止与您的团队发生冲突或损坏。

## 代码材料

配置文件的要点:

backend.hcl 文件将用于设置您所在州的后端配置。您可以更改 DynamoDB 表的名称。确保名称以文件 global.tfvars 中的 service_name 开头。

global.tfvars 文件将用作命名资源的前缀。你也可以设置更多的变量。用您自己的名称更新 service_name。

setup.sh 文件是一个 bash 脚本，用于初始化您的 Terraform 远程状态。更新 STATE_BUCKET_NAME 变量。这将是用于存储状态的 S3 存储桶的名称。

teardown.sh 文件是一个 bash 脚本，用于删除您的 Terraform 远程状态。更新 STATE_BUCKET_NAME 变量。这将是用于存储状态的 S3 存储桶的名称。

下一步是开始在`global`文件夹里面写东西。

我们将在`state_local`文件夹中定义远程后端。

定义您的 S3 桶和 DynamoDB 表。

你用版本控制和加密来定义我们的 S3 存储桶，在那里你的地形状态文件将被存储。您还定义了一个 DynamoDB 表来管理您的状态中的锁定。将`hash_key`定义为`LockID`很重要；如果不这样做，将不会启用锁定。

现在您需要将本地后端同步到远程。您可以通过将其添加到`state_remote`文件夹中来实现。

定义您的 S3 桶和 DynamoDB 表。

如您所见，该文件与前一个文件几乎相同。主要区别在于您定义了一个后端配置。这将告诉 Terraform 在 S3 寻找州，而不是创建一个新的 S3 桶和 DynamoDB 表。

# 我如何让它工作？

> *现在我得到了这一堆文件，但是我的后端没有处理任何状态。*

为了真正开始一切，我们需要运行我们之前编写的`setup.sh` bash 脚本。

**确保有权限运行它。提示:** `**chmod +x ./setup.sh**`

运行该脚本后，将创建一个 S3 桶和一个 DynamoDB 表，并且您的 Terraform 状态有其远程后端。

现在，当你开始添加你的模块时，唯一要记住的是用存储状态的 S3 桶初始化它们的后端。作为一个例子，让我们假设我们有一个`dev`文件夹，其中有一个名为`storage`的模块。在`modules`文件夹中，我们定义了模块`storage`。里面有一个 S3 桶的定义。

好吧，这听起来可能有点复杂，但这会让你项目中的一切都井井有条。 [**为了方便起见，这里有一个 Github repo，它为这个项目提供了完全相同的设置。**](https://github.com/jagonzalr/terraform-remote-backend-example)

添加一个新文件`deploy.sh`到你的项目的根目录。

确保在 state bucket 上使用与 setup.sh 中定义的相同的名称。

这个文件的作用是为我们的`storage`模块设置后端，然后创建(或更新)我们的基础设施。

**Kataboom！**现在你已经准备好与你的团队合作，而不用担心冲突。

# **总结**

要记住的事情:

*   DevOps 现在比以往任何时候都更容易接近
*   Terraform 是一个不错的选择([但不是唯一的](https://dzone.com/articles/the-top-7-infrastructure-as-code-tools-for-automat))
*   您应该定义一个远程后端以便于协作
*   从上面的所有代码中检查这个[Github repo](https://github.com/jagonzalr/terraform-remote-backend-example)

有一个好的，并继续建设真棒的东西🎉

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)