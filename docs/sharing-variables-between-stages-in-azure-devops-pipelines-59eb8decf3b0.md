# 在 Azure DevOps 管道中的阶段之间共享输出变量

> 原文：<https://levelup.gitconnected.com/sharing-variables-between-stages-in-azure-devops-pipelines-59eb8decf3b0>

![](img/7d22319e4bd422cec71bbd9d2eafcab6.png)

约翰·巴克利普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我最近参与了一个 azure，Terraform 项目，其中 terraform **状态文件**依赖项，如 Azure 存储 blob、容器、访问密钥等，是从 PowerShell 任务创建的，后来为 terraform 传递访问密钥以初始化其远程状态。这是在创建初始资源、SOP 等和完整的 IaC 时消除手动依赖的一种方式！！

在这个 azure [版本](https://docs.microsoft.com/en-us/azure/devops/release-notes/2020/sprint-168-update#jobs-can-access-output-variables-from-previous-stages)之后，在管道阶段之间共享变量被启用，下面是我的 PowerShell 代码中的一个片段，其中“存储帐户访问密钥”被存储到变量`**$STORAGE_ACCESS_KEY**`中。

```
# Fetching the keys to access the storage account
Write-Host 'Fetching storage key from '$tf.STORAGE_ACCOUNT_NAME
$STORAGE_ACCESS_KEY=$(az storage account keys list --subscription $subscription_id --resource-group $tf.RESOURCE_GROUP_NAME --account-name $tf.STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)
Write-Host "##vso[task.setvariable variable=accesskey;isOutput=true]$STORAGE_ACCESS_KEY"
```

您可以看到变量`**accesskey**`是用`**STORAGE_ACCESS_KEY**`设置的，其中包含存储帐户访问密钥和名为[日志命令](https://docs.microsoft.com/en-us/azure/devops/pipelines/scripts/logging-commands)的概念，您可以在脚本中定义输出变量。

> VSO 代表 Visual Studio Online，微软将其重命名为 Visual Studio Team Services (VSTS ),后来又重命名为 Azure DevOps Services。

```
Write-Host "##vso[task.setvariable variable=accesskey;isOutput=true]$STORAGE_ACCESS_KEY"
```

上面的脚本在名为**设置**的阶段、作业**测试**中以及名为**先决条件**的步骤中执行。所以要将变量`**accesskey**`传递到下一个叫做 Validate 的阶段，你必须使用`**stageDependencies**`。下面的代码片段显示了给变量`**$storage_access_key**`赋值`**accesskey**`。

```
** azure-pipelines.yml, wrote by bare hand and not tested :) **
stages:
- stage: Setup
  displayName: "Setup Environments"
  jobs:
  - job : Test
    steps:
    - pwsh: |
        Write-Host "##vso[task.setvariable                                                                        variable=accesskey;isOutput=true]$STORAGE_ACCESS_KEY"
      name: pre_requisites
- stage: Validate
  displayName: Validations, TFLint, Scan
  variables:
  - name: env
    value: test
  - name: storage_access_key
    value: $[ stageDependencies.Setup.Test.outputs['pre_requisites.accesskey'] ]
```

在一个阶段上，要引用另一个阶段的输出变量，应使用以下表达式格式:

*   在阶段级别，从另一个阶段引用输出变量的格式是`**dependencies.STAGE.outputs['JOB.TASK.VARIABLE']**`。
*   在作业级别，从另一个阶段引用输出变量的格式是`**stageDependencies.STAGE.JOB.outputs['TASK.VARIABLE']**`。

有关这方面的更多细节，您可以查看关于“ [**在不同阶段使用输出**](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-outputs-in-a-different-stage) ”的文档。

*注意:奇怪的是，如果您需要将值* `*accesskey*` *传递给第三个阶段，比如说“验证 3”，并且您在阶段“验证”中重复了相同的代码，那么您将得到一个空字符串作为* `*accesskey.*` 和一个限制，您不能在多个阶段中访问该变量！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   💰免费编码面试课程[查看课程](https://skilled.dev/?utm_source=luc&utm_medium=article)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)