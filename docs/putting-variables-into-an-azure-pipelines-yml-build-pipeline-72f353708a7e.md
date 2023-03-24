# 将变量放入 Azure Pipelines yml 构建管道

> 原文：<https://levelup.gitconnected.com/putting-variables-into-an-azure-pipelines-yml-build-pipeline-72f353708a7e>

![](img/a62b4a104a28727fe2d68895c6265447.png)

[田宽](https://unsplash.com/@realaxer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我似乎一直在努力尝试将变量放入 Azure Pipelines `build.yml`文件。一些变量[是预定义的和全局的](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml)，而其他的[是本地设置的](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch)。我将编制一个代码示例列表供自己参考，并随着我学习新的做事方法而增加这个列表。

## 将预定义的变量放入 bash 脚本中

在这种情况下，我想在 Mac 代理上运行 bash 脚本来重命名构建的应用程序文件，以包含版本号。要使用预定义的变量，您需要全部使用大写字母，并在有句号的地方添加下划线。您还需要在开头添加一个美元符号。例如`Build.BuildNumber`变成了`$BUILD_BUILDNUMBER`。

## 将模板参数放入 bash 脚本中

在这种情况下，我们从另一个`.yml`文件中包含这个模板，并在包含它时传递参数。

## 将作业变量放入 bash 脚本中

在这种情况下，我们只是在作业的顶部局部设置一个变量。