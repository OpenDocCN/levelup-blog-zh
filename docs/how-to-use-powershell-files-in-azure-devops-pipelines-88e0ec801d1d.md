# 在 Azure DevOps 管道中使用 PowerShell 文件

> 原文：<https://levelup.gitconnected.com/how-to-use-powershell-files-in-azure-devops-pipelines-88e0ec801d1d>

在 Azure DevOps YAML 管道中使用 Powershell 脚本。

![](img/aa23e3f5a8f4d7ef0d73e992353a1ab7.png)

何塞·勒布朗在 [Unsplash](https://unsplash.com/s/photos/jet-fuel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

脚本充当您的 DevOps 管道的燃料，而 PowerShell 在自动化您的任务中扮演着重要角色。我最近参与了一个 Azure DevOps 项目，其中很多自动化都是使用 PowerShell 编写的。在您的 YAML 管道中有许多运行 PowerShell 任务的方法，我将尝试解释其中的大部分

> PowerShell 是微软的一个任务自动化和配置管理程序，由命令行 Shell 和相关的脚本语言组成

# 内嵌脚本

您可以在 YAML 文件中将 PowerShell 脚本作为内联脚本运行，可以是单行脚本，也可以是多行脚本

`- Powershell: "PowerShell single line"`

```
- Powershell: |
      This is script line 1
        This is script line 2
```

下面是运行内联 PowerShell 脚本的各种方法

## **方法一:使用 powershell.exe**

`powershell.exe`是传统的 *Windows PowerShell* 版本(v5.1-)的可执行名称，仅构建在 Windows 上。NET Framework (v4.8-)它不支持跨平台执行，只在 windows 代理上执行。

```
stages:
  - stage: Initialize
    displayName: 'Initialize Stage'
    pool: "windows-server-pool"
    jobs:
    - job: Install
      displayName: 'Install Dependencies (PowerShell)'  
      steps:
      - script: |
         @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "Install-PackageProvider NuGet -Scope CurrentUser -Force"
        displayName: 'Powershell Install NuGet'
      - script: |
            @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "Install-Module -Name SqlServer -AllowClobber -Scope CurrentUser -Force"
        displayName: 'Powershell Install SqlServer Module'
```

## **方法二:使用 pwsh.exe**

`pwsh[.exe]`是*PowerShell【Core】*(V6+)的*可执行文件名称*，PowerShell 的跨平台版本建立在其上。NET Core /。网络 5+

```
- pwsh: './SmokeTest.ps1 -url $(WebAppDeploy.AppServiceApplicationUrl)'
  displayName: 'Run Smoke Test'
  workingDirectory: '$(Pipeline.Workspace)/DeployScripts'
  failOnStderr: true
```

`pwsh`运行 PowerShell Core，它必须安装在 Linux 特定的代理或容器上，要在您的 Linux 代理中安装 **pwsh** ，运行

```
sudo apt-get install -y PowerShell
pwsh --version
```

方法三: **— PowerShell 任务**

```
- powershell: |
     Install-Module -Name Az -AllowClobber -Scope CurrentUser -Force
     displayName: 'Install Azure Powershell'
```

## 方法 4: PowerShell@2 任务

这是“- powershell:”和“- pwsh”的快捷方式，根据所选的选项，管道代理将位于 Windows 或 Linux 上。

```
- task: PowerShell@2
  displayName: 'Run Smoke Test'
  inputs:
    filePath: '$(Pipeline.Workspace)/DeployScripts/SmokeTest.ps1'
    arguments: '-url $(WebAppDeploy.AppServiceApplicationUrl)'
    failOnStderr: true
    pwsh: true
```

> 使用内联代码的优点是将所有功能都放在一个地方，更容易看到正在发生的一切。但是，如果您有一个大型管道，这种方法可能会变得复杂。

# 剧本

您也可以在 YAML 管道中运行. ps1 脚本。将. ps1 文件保存在$(System 下的文件夹中。DefaultWorkingDirectory)并将`targetType`设置为`filePath`用于执行。然后通过`filePath`属性指定脚本运行的路径。

```
- task: PowerShell@2
  retryCountOnTaskFailure: 2
  inputs:
    targetType: 'filePath'
    filePath: $(System.DefaultWorkingDirectory)/Scripts/health.ps1
    arguments: > # Use this to avoid newline characters in a  multiline string
      -uri "[https://](/${{)myapp.azurewebsites.net/health"
  displayName: 'Health check'
```

> 默认情况下，Azure DevOps 将签出源 repo 中的所有代码。签出代码会将脚本文件从 repo 下载到管道代理上，使它们可供执行。这里的例外是` **-部署:**`作业，您必须使用-**check out:self**强制签出该作业

# 结论

PowerShell 对于 DevOps 工程师来说非常方便，可以在 Windows 和 Linux 代理上运行。Azure DevOps 支持 PowerShell 内联和管道中的脚本，可以根据代码的复杂性进行选择。快乐脚本:)