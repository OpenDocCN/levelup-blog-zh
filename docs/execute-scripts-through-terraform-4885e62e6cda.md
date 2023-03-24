# 通过 Terraform 执行脚本

> 原文：<https://levelup.gitconnected.com/execute-scripts-through-terraform-4885e62e6cda>

如何在 terraform 中执行 power-shell 或 cli 脚本

![](img/378efb3356a892749811b3b866eaa3b5.png)

[米切尔·罗](https://unsplash.com/@mitchel3uo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Terraform 是供应云基础设施的一个很好的工具，也是编写代码的一种声明式方式。但有时它会遗漏一些您的云提供商支持的功能。例如，当我写这篇博客时，terraform 在创建 Azure AD 应用程序时无法设置默认值`identifier_uris`以及这里提到的一个未解决的问题 [azuread_application:默认 identifier_uris 字段为“api://application_id ”,因为门户发布了#428](https://github.com/hashicorp/terraform-provider-azuread/issues/428)

## 地形上的脚本

那么我们能做什么呢？是的，有一个解决办法！！是的，这是你开发生涯中最喜欢的词。因此，要解决这个问题，有很多工作要做，我已经在这里写了一篇博客[在 Azure DevOps 管道中使用 PowerShell 文件](https://rollendxavier.medium.com/how-to-use-powershell-files-in-azure-devops-pipelines-88e0ec801d1d)。但是对于这个场景，将尝试使用 terraform 资源本身使用`null_resource` & `provisioner`的组合来执行外部脚本。

为了运行脚本，我们将使用 Terraform 中的`null_resource` ，并在这里找到更多关于它的信息[。使用 null_resource，我们将调用`local_exec` provisioner，它指定脚本将在运行 Terraform 配置的代理上运行。更多信息请点击](https://www.terraform.io/docs/provisioners/null_resource.html)[这里](https://www.terraform.io/docs/provisioners/local-exec.html)。

## **例子**

因此，我们上面提到的限制，让我们尝试通过 terraform 本身来解决

```
resource "azuread_application" "app1" {
  display_name = "test-app1"
}locals {
  identifier_uri          = "api://${azuread_application.app1.application_id}"
}resource "null_resource" "app_identifier_uri" {
  provisioner "local-exec" {
    command = "az ad app update --id $APP_OBJECT_ID --identifier-uris $IDENTIFIER_URI"
    environment = {
      APP_OBJECT_ID           = azuread_application.app1.object_id
      IDENTIFIER_URI          = local.identifier_uri
    }
  }depends_on = [
    azuread_application.app1
  ]
}
```

让我们深入了解上面的代码发生了什么，第一部分是创建一个名为“test-app1”的示例广告应用程序。下一步是创建一个局部变量，然后通过一个`null_resource`更新`identifier_uri`。

## **地形补给者**

terraform 中有两种供应器，`local-exec` & `remote-exec`

> **本地执行供应器在创建资源后调用本地可执行文件**。这将在运行 Terraform 的机器上调用一个进程，而不是在资源上

是的，没错，local exec 不对资源做任何事情，但是对于我们的场景，要执行外部脚本，这是可行的。我们需要一个适当的 Azure 连接已启用，terraform 服务主体已定义所需的角色和访问。

## 结论

大多数时候，我都是在 DevOps 管道中编写外部脚本，而 terraform 创建脚本的能力很方便，我觉得这很容易参考。还有其他需要考虑的事情，比如空资源状态、脚本执行的生命周期等等，这些都留给你了:)。

![](img/802615e8f0b0af65ea502e62a59c49f8.png)