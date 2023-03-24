# 通过持续交付和 GitHub 操作简化部署

> 原文：<https://levelup.gitconnected.com/make-deployment-easy-with-continuous-delivery-and-github-action-f5dde92468a1>

了解 CD 如何为您节省数百个小时

![](img/4e131222896c0849262937e559df357b.png)

由[杰西·拉米雷斯](https://unsplash.com/@jesseramirezla?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

部署总是一项复杂而乏味的任务。随着应用程序的增长，您可能需要一周多次交付生产，快速交付通常是至关重要的。幸运的是，由于持续的交付和 GitHub 操作，部署到生产环境变成了一项简单的任务！

# 什么是 CD？

连续交付或 CD 是一种敏捷的实践，即连续地将比特大小的代码片断部署到生产中，而不是以大块的形式。这样，不仅您的应用程序可以更稳定地增长，而且在小提交中比在大量代码中更容易识别错误。当与持续集成或 CI 结合时，CD 处于最佳状态。

CI 从管理应用程序变更的相同原则出发，分成更小的、更易管理的部分，但与 CD 相反，CI 负责验证代码库的新变更是否按预期工作。这样，只有当新的变更按预期工作时，CD 才会介入部署新的变更！

CI 和 CD 的美妙之处在于，由于 GitHub 的动作，它们可以完全自动化！每次对主分支进行提交时，我们可以触发一个工作流，对我们的代码进行测试，如果测试通过，我们的代码就会被部署到生产环境中。你所要担心的就是把你的代码推到你的库，并修复任何可能发生的错误。

要了解更多关于 GitHub 动作和如何创建工作流的信息，请查看我之前关于 CI 的文章。

[](/why-you-should-use-ci-df1adc0c5145) [## 为什么您应该使用 CI

### 以及如何用 GitHub 动作做到这一点

levelup.gitconnected.com](/why-you-should-use-ci-df1adc0c5145) 

在这篇文章中，我将主要侧重于适当的裁谈会工作流程的内容。

# 启用 CD

在一天结束时，部署包括登录到服务器并运行某些命令。

## 生成 SSH 密钥

为了登录我们的生产服务器，我们需要一对 ssh 密钥:一个秘密密钥或 SK 和一个公开密钥或 PK。要生成它们，请在本地计算机上运行

```
**$ ssh-keygen -m PEM -t rsa -b 4096** Generating public/private rsa key pair.
Enter file in which to save the key (/home/paburo/.ssh/id_rsa): **name**
```

其中`name`代表密钥对的名称。出于本教程的目的，让我们将`name`设置为 **cd** 。当命令完成时，我们现在应该在 **~/上有两个新文件。ssh** 目录。

```
**$ ls ~/.ssh** cd cd.pub 
```

我们现在需要复制密钥的内容，并将其作为存储库秘密保存在 GitHub 上。

```
**$ cat ~/.ssh/cd** -----BEGIN RSA PRIVATE KEY-----
MIIJJwIBAAKCAgEAlmBMQtfsLjOADd3mAuq5qqifiynSJzGN3dy3qJed8MCDZv7S
**[...]**
ty9wnmRiqhzl2wzuA7TVyq6X40IOOdPp910CFQrRMPaJPV1/y2KjMwMLF0/cdVNG
RHhuExEbTzMa+zYjDtt0nBERRiR0tiy/3fBfFOylWtyzkH4Hvz9gBYGOwQ==
-----END RSA PRIVATE KEY-----
```

前往你的 GitHub 库，点击**设置>秘密>新建库秘密**，用你的私钥内容创建一个 GitHub 秘密，名为 **CD_SK** 。

![](img/537a552501a046c33a7f4c557a23c551.png)

一旦你保存了这个秘密，从你的电脑中删除你的私钥是一个很好的做法，但是要小心，因为你将无法从 GitHub secret 中看到你的密钥。

```
**$** rm ~/.ssh/cd
```

现在我们需要将公钥的内容添加到我们的服务器文件中。首先，让我们获取公钥的内容

```
**$ cat ~/.ssh/cd.pub**
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCWYExC1+wuM4AN3eYC6rmqqJ+LKdInMY3d3Leol53wwINm/tK1uHRKU9UilpYIvadV9UxSyIDueHgpcME6Yqjjt9KxFIxSGtrvN6RWxLGGjl0HMYoe5bETwOyrF7MT69whe9AFOlX2WYzqXbbeqaHlvoFYp/M5woFQQo2LTZ/OUHpUT/4ODsmlNgSNA0L70GRonEXj8QRwWyhEMSWk/h5S6iCfQg2TGLW+c6F6lj0+izo6DahdKPiW6j4IsSvV52gyXdfX28Mo3XeEeIXaNXSW5VANSKbJ5aZ4pfgnmfTDmPI9p73SeTsCzisAxTpVZN0OilL+I/MvVkAKtCQLdzQANVM6gFolz8xjfjTGtu1xFLo/V1dlGe+IvkSvVJIeecrMtPudmOTR1lre/JcDGb3ntbFjQuNfDaqc6jEgsFDmub+J7rvORzJumfLl+1fe4BdMA4Rl7oLPHb1I9pFhO3vYuAd+2+FpXOygIC+pDlznjpTwfdXoJkm9M4Q5SzoCmgfkkbKaLhmRhyP7QuEv6FALfIF98FIvLiRcMpjK6+SHiq684dxce8yMbKdmdfRuwR+7t9/K5OLF3KB67uZQGaRZYZsCxMsk76Z7joANCm+5YoPEyxck8CQ1ArYC+TlHfZU9O1uKvjlqdiffbJbcVSDAy24OiXkvpNWzV+bFX15ULw== paburo@paburo-ThinkPad-XXXX
```

现在 ssh 进入您的服务器，这可以通过

```
**$** ssh **user**@**ip**
```

其中`user`是你服务器上的用户，`ip`。从你的服务器上，用你喜欢的文本编辑器打开`authorized_keys`文件，并在底部粘贴你的公钥内容。

```
**$** nano ~/.ssh/authorized_keys
```

## CD 工作流程

既然我们已经生成并配置了用于访问服务器的 SSH 密钥对，那么是时候创建一个工作流来登录服务器并为我们运行命令了。

让我们分解这个工作流程。此工作流可以手动触发，也可以在每次向主分支发出推或拉请求时触发。一旦被触发，它将执行`CD`工作。这项工作将使用一个由[苹果男孩](https://github.com/appleboy)ssh 到我们的服务器的市场行动。它需要四个参数:`host`，你的服务器的域或者 IP；`username`，你的服务器的用户名；`key`，我们之前创建的密钥；`port`，对于 SSH 连接默认为 22 和`script`，这是我们希望在工作流通过 SSH 登录后在我们的服务器上运行的命令。

根据您的应用程序，`script`参数可能会有很大的不同。在这个例子中，您可以看到我是如何选择部署一个 Django 应用程序的:我`cd`到项目目录中，从 git 存储库中提取，安装所有的需求，并重启负责我的应用程序的服务。

# 最后的想法

就是这样！现在您可以放松了，因为每次您将代码推送到存储库时，部署过程都会自动执行！持续交付本身是一个很好的工具，但是一定要把它和持续集成一起使用来释放它的全部潜力。

一如既往，如果需要，请随时联系我。本教程中的所有代码都可以在 GitHub 上免费获得。

[](https://github.com/PaburoTC/JoboBackend) [## PaburoTC/JoboBackend

### 通过在 GitHub 上创建一个帐户，为 PaburoTC/JoboBackend 开发做出贡献。

github.com](https://github.com/PaburoTC/JoboBackend) 

连续部署愉快！

谢谢你