# angular——必需角色指令

> 原文：<https://levelup.gitconnected.com/angular-required-roles-directive-48f477f69d23>

## 通过指令使用基于角色的访问控制(RBAC)动态禁用元素

![](img/5e471ef0b4e3b9f0e03d983c0616a01c.png)

[贾罗德·埃尔贝](https://unsplash.com/@erbephoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的照片

在许多应用程序中，需要用户的认证机制。此外，用户可以具有不同的角色，以实现不同的功能或用于决定用户是否被授权查看或访问某个功能。在本文中，我将向您展示如何使用角度指令为没有特定角色的用户动态禁用元素，例如按钮。

## 先决条件

对于这个例子，我不会讨论一般的用户认证，而是假设应用程序已经实现了一个认证机制。因此你应该

1.  熟悉 OIDC、oAuth2 和 JWT(集成认证的通用库是 [angular-oauth2-oidc](https://www.npmjs.com/package/angular-oauth2-oidc) )
2.  熟悉基于角色的访问控制(RBAC)

## 场景

基于角色的访问控制(RBAC)是一种基于用户角色(例如在组织或公司内)来限制对应用程序的访问的方法。考虑下面的例子:我们有一个管理员工数据的应用程序。员工应该只能读取应用程序中的数据。另一方面，经理可以编辑数据。因此，我们现在有两个不同的角色，并希望停用一个角色的特定功能。

## 让我们完成它

如上所述，我们假设应用程序已经验证了用户，因此我们也收到了一个令牌(JWT)。现在让我们创建我们的指令。

我们将 Angular CLI 与以下命令配合使用:

```
ng generate directive required-roles
```

这将生成一个新的指令:

首先，我们需要向我们的指令添加一个属性，我们将使用它作为所需角色的输入。如果要检查多个角色，该属性可以是字符串或数组。

接下来，我们需要修改指令附加到的 HTML 元素。为此，我们可以使用`ElementRef`，它作为我们视图中元素的包装器。与`Renderer2`结合使用，我们可以操纵 DOM。

最后，我们需要一种方法来检查角色。为此，我们可以使用例如检查用户令牌是否包含相应角色的服务。

最终指令可能如下所示:

我们有针对`requiredRole`的输入，我们通过`RolesService`来验证它，如果用户没有所需的角色，主机元素将被禁用。如您所见，`Renderer2`可以在运行时操作 DOM，设置元素的属性或改变它们的样式。这是 Angular 核心包的一个非常强大的内置 API(可以找到所有可用的方法来检查官方的[文档](https://angular.io/api/core/Renderer2#description))。

要在按钮上使用该指令，只需将它添加到 HTML 元素中，并为所需的角色添加一个值。在下面的例子中，对于没有角色`admin`的用户，该按钮将被禁用:

最后，让我们看看 RolesService 的目的。这里我们注入一个 AuthService(例如，来自前面提到的[认证库](https://github.com/manfredsteyer/angular-oauth2-oidc)的那个)来访问用户令牌。如果令牌声明中的角色与所需角色匹配，则用户被授权。

验证角色的最小示例

我还在这个 StackBlitz 中添加了一个小原型(不包括身份验证)。

# 下一步是什么

附加功能的一些想法:

*   调整指令以验证多个角色
*   改进角色验证以支持不同的运算符，如 OR，即用户拥有角色 X 或角色 y。

此外，请查看后续报道，了解一些改进:

[](/angular-improved-required-roles-directive-e2e90ed32b67) [## Angular —改进的必需角色指令

### 通过指令进行细粒度的基于角色的访问控制(RBAC)

levelup.gitconnected.com](/angular-improved-required-roles-directive-e2e90ed32b67) 

在 [Medium](https://saackef.com/) 或 [Twitter](https://twitter.com/sw3eks) 上关注我，阅读更多关于 Angular 的内容！

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)