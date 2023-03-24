# 为 Azure 自动创建 Terraform 状态基础架构

> 原文：<https://levelup.gitconnected.com/automate-terraform-state-for-azure-920e9005968e>

![](img/f5f72e514cb92d1e71b75cc08e21847f.png)

图片来自 azorobotics

Terraform 需要保存一个状态文件来维护它的基础设施和它所创建的资源的配置。通常，我们从为任何 terraform 项目手动创建初始存储帐户、容器等开始，但当更多的环境到来时，可能必须重复这些步骤，例如:测试、uat、生产等。我将解释使用 power-shell 和 Azure DevOps 自动化上述过程的步骤。

# 自动创建远程状态基础架构

## 本地和远程状态

当你在本地开发环境中工作时，这种状态默认存储在名为“terraform.tfstate”的本地文件中，但在 Azure 中，它也可以远程存储，这在共享环境中效果更好。

**将远程状态文件保存在安全的地方**

在共享环境中工作&作为最佳实践，将 Terraform state 状态文件保存到共享存储中，这对于任何 IaC 项目都是极其重要的。

您必须采取预防措施，不要丢失状态文件来跟踪现有基础设施的状态。如果那个丢失了，Terraform 会试图破坏不该破坏的基础设施！。

在 Azure 上工作时，存储帐户和存储容器用于维护您的状态文件。

## 超级外壳脚本和 Azure 管道(yml)

我们可以在 Azure DevOps 管道上运行 powershell 脚本，下面的 powershell 脚本将使用“ [az cli](https://docs.microsoft.com/en-us/cli/azure/) ”创建 terraform 管理其状态所需的资源，并通过服务主体完成身份验证。创建的资源有

*   资源组
*   存储帐户
*   贮藏容器

下面是您需要包含在您的管道 yml 中来调用上面的 power-shell 脚本的脚本。根据需要修改参数“订阅”。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   💰免费编码面试课程[查看课程](https://skilled.dev/?utm_source=luc&utm_medium=article)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)