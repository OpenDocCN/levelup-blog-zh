# 如何用 Githooks 增强您的 Python 编码游戏

> 原文：<https://levelup.gitconnected.com/how-to-step-up-your-python-coding-game-with-githooks-3c054409dcb4>

关注那些修复 python 语法、hooks 文件、cicd 管道模板和凭证的 git 挂钩

![](img/d95cb1e5bbd00a106042f5b464df48ea.png)

罗曼·辛克维奇·🇺🇦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我最近写了一篇关于在 git 提交过程中使用预提交的文章。

[](/how-to-standardize-python-code-with-githooks-d81a73b481b4) [## 如何用 Githooks 标准化 Python 代码

### 实现预提交钩子来转换我自己的网站代码

levelup.gitconnected.com](/how-to-standardize-python-code-with-githooks-d81a73b481b4) 

在写那篇文章的时候，我对有这么多钩子感到惊讶，并想详细说明预提交真正出色的地方。

## 句法

工程师在一个项目中作为一个团队工作。当他们建立新的专业领域和新思想的原型时，他们的代码中经常有不一致的地方。引入 Python 语法标准简化了代码库，标准化了不同项目之间的最佳实践，帮助开发人员更快地找到瓶颈，并减少了调试代码问题所花费的时间。

我们可以添加这些挂钩来更改特定的语法，比如将双引号改为单引号，或者将旧的 python 代码改为新的 python 代码:

*   `black_nbconvert`将黑色应用于 ipynb 文件。
*   `double-quote-string-fixer`会用单引号替换双引号。
*   `end-of-file-fixer`如果文件中有代码或者验证文件为空，则添加额外的一行。
*   `name-tests-test`将验证包含测试的文件名称是否正确。
*   `pydocstyle`检查是否符合 Python docstring 约定。
*   `pyupgrade`将为新版本升级旧的 python 语法。
*   `trailing-whitespace`将删除尾部空白。

## **Docker 文件**

容器通过在任何服务器、云或客户机上运行一个软件包来简化应用程序的开发和交付，而不需要虚拟机。

容器是轻量级的，易于配置，并且通过只包含足够支持单一功能的软件，允许更有效的空间利用。

Dockerfile 允许创建这些容器。为了确保 order 文件是正确的，我们可以使用这些钩子:

*   `docker-compose-check`验证 docker 编写的文件。
*   `dockerfilelint`是一种 Dockerfile 棉绒。

## CI/CD 模板

就像 Dockerfiles 一样，持续集成和部署模板也需要验证。你能想象在你做了所有的改变之后，你的代码发现在配置 CI/CD 管道的实际 yaml 文件中有一个错误吗？这个应该有助于解决那个特殊的问题。

*   `check-azure-pipelines`将验证 Azure 管道配置。
*   `check-github-actions`将验证 GitHub 动作。
*   `check-github-workflows`将验证 GitHub 工作流程。
*   `check-gitlab-ci`将验证 GitLab CI 配置。
*   `check-travis`将验证特拉维斯的配置。

## 凭证检查

我们可以通过添加这两个挂钩来识别提交凭证:

*   `detect-aws-credentials`将根据您的 AWS CLI 凭据文件确定您的 AWS 凭据是否存在。
*   `detect-private-key`检测私钥的存在。

我没有在这里列出所有的钩子，所以如果你想找到更多的钩子，可以在[https://pre-commit.com/hooks.html](https://pre-commit.com/hooks.html)找到。它们非常有用，肯定会升级我的代码和你的代码。