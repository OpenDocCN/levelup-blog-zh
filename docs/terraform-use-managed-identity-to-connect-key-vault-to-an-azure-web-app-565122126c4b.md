# Terraform —使用托管身份将密钥库连接到 Azure web 应用程序

> 原文：<https://levelup.gitconnected.com/terraform-use-managed-identity-to-connect-key-vault-to-an-azure-web-app-565122126c4b>

如何认证应用服务以使用托管身份从密钥库中读取机密

![](img/6f49880b72c5a9a0a7bb8a29ab66dbc4.png)

[达维苏科](https://unsplash.com/@davisuko?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/identity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

> 托管身份在 Azure Active Directory 中为应用程序提供了一个自动托管的身份，以便在连接到支持 Azure Active Directory (Azure AD)身份验证的资源时使用。应用程序可以使用托管身份**来获得 Azure AD 令牌，而无需管理任何凭证**。

## 必需的权限

terraform 服务主体需要一个[应用管理员](https://docs.microsoft.com/en-us/azure/active-directory/roles/permissions-reference#application-administrator)角色来创建 AD 应用。

*注意:你的应用子网应该有一个“* ***微软。KeyVault*** *”添加服务点，将其添加到允许的网络。更多内容请点击此处* [*虚拟网络服务端点为 Azure Key Vault*](https://learn.microsoft.com/en-us/azure/key-vault/general/overview-vnet-service-endpoints)

## 将（行星）地球化（以适合人类居住）

第一步是为应用服务启用托管身份，这样应用将拥有 azure 托管服务主体。您可以拥有“系统分配的”(*，将在应用的整个生命周期*中进行管理)或“用户分配的”(*用户管理的生命周期*)管理身份，更多信息请点击[查看](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)。

上述 Terraform 代码中的`identity` 部分将为我们的应用程序提供前面提到的“系统分配的”托管身份。

## 对密钥库的应用服务访问

因此，我们启用了托管身份，接下来是读取作为托管身份的一部分创建的底层服务主体详细信息(记得我之前提到过)

```
# Get the app's managed identity principal id.
data "azuread_service_principal" "app_sp" {
  display_name = azurerm_app_service.app.name
  depends_on   = [
    azurerm_app_service.app
  ]
}
```

现在，将服务主体访问权限分配给密钥库

*如果你在一个* ***的私有网络*** *的世界里记得把 app 子网也添加到访问“允许”的虚拟网络子网标识*

## 结论

托管身份将使您的应用程序能够访问密钥库，而无需管理代码中的任何机密。读取应用程序内部的秘密会因你使用的技术而异，但在 Azure 世界中，你的应用程序已经有了它需要的东西！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   💰免费编码面试课程[查看课程](https://skilled.dev/?utm_source=luc&utm_medium=article)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)