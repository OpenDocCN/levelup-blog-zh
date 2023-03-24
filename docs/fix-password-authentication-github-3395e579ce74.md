# 如何修复 GitHub 上移除的密码认证支持

> 原文：<https://levelup.gitconnected.com/fix-password-authentication-github-3395e579ce74>

## 了解如何在 GitHub 中配置和使用访问令牌

![](img/8c59e2662751775401123f6027aff72b.png)

照片由[推特@ethmessages](https://unsplash.com/@moneyphotos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/password?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上发布

# 介绍

2020 年 7 月， [GitHub 宣布了他们的意图](https://github.blog/2020-07-30-token-authentication-requirements-for-api-and-git-operations/)，要求用户使用**基于令牌的认证**，以便执行某些(已认证的)Git 操作。从 2021 年 8 月 13 日起，**使用 REST API 进行身份验证时，不再接受帐户密码。**

例如，如果您尝试使用密码验证在远程服务器上进行`push`，操作将会失败，并显示以下消息:

```
**Support for password authentication was removed on August 13, 2021\. Please use a personal access token instead**
```

最近的变化影响了对 Git 的命令行访问，以及任何使用密码直接访问 GitHub 库的服务。另一方面，如果您已经启用了双因素身份验证，则需要使用基于令牌的身份验证(或基于 SSH 的身份验证)，因此您应该不会看到上面提到的错误。

在今天的文章中，我们将讨论一个快速的分步指南，它将帮助您在 GitHub 上配置访问令牌，我们将允许您在执行需要您这样做的 Git 操作时执行基于令牌的身份验证。

# 重现错误

现在让我们假设您已经初始化了一个 Git 存储库(`git init`)，您已经做了一些工作并创建了一个 commit，最后您想要将所做的更改推送到远程主机。

```
$ **git push -U origin main**
Username for '[https://github.com'](https://github.com'): <username>
Password for '[https://your-username@github.com'](https://gmyrianthous@github.com'): 
remote: Support for password authentication was removed on August 13, 2021\. Please use a personal access token instead.
remote: Please see [https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) for more information.
fatal: Authentication failed for '[https://github.com/<username>/repo-name.git/'](https://github.com/gmyrianthous/CrackingTheCodingInterviewSolutions.git/')
```

假设您正在尝试执行基于密码的身份验证，push 命令将会失败，并显示上面显示的身份验证致命错误。

# 修复错误:逐步指南

在接下来的几节中，我们将一步步地引导您在 GitHub 上配置访问令牌，这样您就可以在执行需要认证的 Git 操作时执行基于令牌的认证。

## 步骤 1:在 GitHub 上创建访问令牌

首先，你必须在 GitHub 上创建一个个人访问令牌。为此，请遵循以下步骤:

*   点击右上角的 **GitHub 个人资料图标**
*   点击**设置**
*   从左侧显示的菜单中，点击**开发者设置**
*   点击**个人访问令牌**
*   点击**生成新令牌**
*   添加一条注释，帮助您确定要生成的访问令牌的范围
*   **从下拉菜单中选择有效期**(最好避免选择无有效期选项)
*   最后，**选择您想要授予对生成的访问令牌**的相应访问权限的作用域。确保选择所需的最小范围，否则在执行某些 Git 操作时仍然会有问题。
*   最后点击**生成令牌**

现在，您应该已经成功地生成了个人访问令牌，并且您的屏幕上应该会显示以下消息。

![](img/6c40702a19e805c0e161c99decce35cc.png)

来源:[作者](https://gmyrianthous.medium.com/)

在该消息下面，您还应该能够看到您的个人访问令牌。**确保复制它**，因为我们将在以下步骤中需要它。

## 步骤 2:更改远程 URL

既然我们已经创建了个人访问令牌，我们需要运行以下命令:

```
git remote set-url origin https://**<githubtoken>**@github.com/**<username>**/**<repositoryname>**.git
```

在上面的命令中确保替换:

*   `<githubtoken>`使用您在上一步中复制的个人访问令牌
*   `<username>`使用您的 GitHub 用户名
*   `<repositoryname>`用你的 GitHub 库的名字

## 第三步:测试它是否有效

现在，我们已经成功地为 GitHub 上的特定存储库配置了基于令牌的认证。要测试一切是否按计划进行，只需尝试将您在本地所做的更改推送到远程主机。举个例子，

```
$ git push -u origin main
```

Git 操作应该可以顺利执行。

# 最后的想法

在今天的简短指南中，我们讨论了 GitHub 最近的变化，现在要求用户执行基于令牌的身份验证，因为基于密码的身份验证不再被接受。

此外，我们还浏览了一个分步指南，该指南将帮助您配置 GitHub 访问令牌，这将允许您正确地认证 Git 操作。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

# **你可能也会喜欢**

[](https://towardsdatascience.com/undo-last-local-commit-git-5410a18f527) [## 如何撤销 Git 中的最后一次本地提交

### 了解如何在 Git 中撤销最近的本地提交

towardsdatascience.com](https://towardsdatascience.com/undo-last-local-commit-git-5410a18f527) [](https://betterprogramming.pub/how-to-merge-other-git-branches-into-your-own-2fe69f70a2b4) [## 如何将其他 Git 分支合并到您自己的分支中

### 使用命令行或 GitHub 桌面快速合并分支

better 编程. pub](https://betterprogramming.pub/how-to-merge-other-git-branches-into-your-own-2fe69f70a2b4) 

# 分级编码

感谢您成为我们社区的一员！升级正在改变技术招聘。 [**在最好的公司**找到你最理想的工作](https://jobs.levelup.dev/talent) **。**

[](https://jobs.levelup.dev/talent) [## 提升——改变招聘流程

### 🔥让软件工程师找到他们热爱的完美角色🧠寻找人才是最痛苦的部分…

作业. levelup.dev](https://jobs.levelup.dev/talent)