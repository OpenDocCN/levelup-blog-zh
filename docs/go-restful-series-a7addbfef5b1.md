# 去 RESTful 系列

> 原文：<https://levelup.gitconnected.com/go-restful-series-a7addbfef5b1>

![](img/f2869fe772af8be4a522bafd3330ac3c.png)

## 让我们用 Go (Golang)构建一个真实世界的生产级 RESTful Web 服务项目。

当我为自己的未来项目试验 Go (Golang)时，我正在构建一个真实世界的生产级 RESTful Web 服务项目作为概念证明，并将我的工作记录到本系列中。

**这个项目的源代码、文档、问题跟踪都发布更新到这个** [**Github 仓库**](https://github.com/the-evengers/go-restful) **。请参考那里的进一步细节和更新。**

![](img/0d92292b03584d887e2487f7bc014714.png)

# 目标

该项目包括:

*   一个优化的 Go 实现遵循[干净的架构](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)，提供声明实体、用例以及外部服务(例如数据访问)的机制。
*   一个优化的 Go 实现提供了将实体和用例公开为 RESTful Web 服务的机制。
*   基于令牌的[认证](https://en.wikipedia.org/wiki/Authentication)和[授权](https://en.wikipedia.org/wiki/Authorization)的优化 Go 实现。
*   一个优化的 Go 实现提供了访问关系数据库的抽象机制。
*   一个优化的 Go 开发环境，有 [Git](https://git-scm.com) 、 [Docker](https://www.docker.com) 、 [Go 模块](https://github.com/golang/go/wiki/Modules)、Go 调试器( [GDB](https://golang.org/doc/gdb) / [Delve](https://github.com/go-delve/delve) )和流行的代码编辑器([vs code](https://www.google.com/search?client=safari&rls=en&q=vscode&ie=UTF-8&oe=UTF-8)/[GoLand](https://www.jetbrains.com/go/)/[Vim](https://github.com/fatih/vim-go))。
*   具有 [Github 动作](https://github.com/features/actions)和 [AWS](https://aws.amazon.com) 的优化 CI/CD 解决方案。
*   一个优化的分发解决方案与 [Github 发布](https://help.github.com/en/enterprise/2.16/user/articles/about-releases)和 [Github 包注册表](https://help.github.com/en/articles/about-github-package-registry)。
*   使用 [Terraform](https://www.terraform.io) 的 [AWS](https://aws.amazon.com) 上的可扩展且高度可用的生产部署解决方案。
*   为测试目的复制生产环境的优化试运行环境。
*   带有 [Github 项目](https://github.com/features/project-management/)、[问题](https://help.github.com/en/articles/about-issues)和[拉请求](https://help.github.com/en/articles/about-pull-requests)的优化问题跟踪机制。
*   持续改进。

# 业务需求

一个“迷你”媒体，一个小型出版/博客平台，允许:

*   用户使用他们的电子邮件和基本信息创建帐户。
*   认证用户创建文章，发布，编辑和删除他们的文章。
*   每个用户都可以看到所有发表的文章。
*   经过认证的用户可以为他人的文章鼓掌，每个用户每篇文章最多可以鼓掌 50 次。
*   经过身份验证的用户可以跟踪其他用户。

# 内容

*   [#2。用 Docker](/setup-simple-go-development-environment-with-docker-b8b9c0d4e0a8) 设置一个简单的 Go 开发环境。
*   [#3。拥有 Docker 和 VSCode](/a-complete-go-development-environment-with-docker-and-vs-code-2355aafe2a96?sk=1becc5c88f48b1c80971ad08e2c6a5f3) 的完整 Go 开发环境。
*   [](/manage-go-dependencies-with-dep-within-docker-and-vscode-remote-containers-cc0fedd627f2#4。在 Docker 和 VSCode 远程容器中使用 Dep 管理 Go 依赖关系</a>。</li><li id=)[#5。一个 Go 全时本地质量 VSCode 驱动的容器化开发环境概要](/summary-of-a-go-full-time-local-quality-vscode-powered-containerized-development-environment-6aa20f50a338)。
*   [#6。将 Golang 内置的 HTTP 服务器实现与最流行的社区包 gorilla/mux](/experiment-golang-http-builtin-and-related-popular-packages-1d9a6dcb80d) 进行对比。
*   [#7。从 Go Dep](/switch-to-go-modules-from-go-dep-fcdd4aa41bd5) 切换到 Go 模块。
*   [#8。迭代#1 —需求和规范](/gorestful-1st-iteration-requirements-and-api-specification-f2b5e40e9571)。
*   [#9。迭代# 1——使用干净架构方法的架构设计简介](/a-quick-architecture-and-components-design-2baf0216021f)。
*   [#10。迭代# 1——实现基本用户实体](/implement-basic-user-entities-1f3d4aea98e2)。
*   *未完待续……*