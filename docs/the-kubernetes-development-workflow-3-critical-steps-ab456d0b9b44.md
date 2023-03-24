# Kubernetes 开发工作流程——3 个关键步骤

> 原文：<https://levelup.gitconnected.com/the-kubernetes-development-workflow-3-critical-steps-ab456d0b9b44>

建立一个高效的 Kubernetes 开发工作流程对于成功和接受您的 Kubernetes 应用至关重要。

![](img/0d046b63779abea6bfe0cbafbb725b5d.png)

越来越多的开发人员开始使用 Kubernetes。这意味着他们的工作流程也必须改变，以适应这种最初不是为开发人员设计的技术。然而，将 Kubernetes 集成到高效的开发工作流中并不容易，它包含了我将在本文中讨论的几个方面。

> 并不是所有的公司都以同样的方式在同样的程度上使用 Kubernetes。要了解 Kubernetes 采用和云原生成熟度的不同阶段，请阅读我关于 [*采用云原生开发之旅*](https://loft.sh/blog/the-journey-of-adopting-cloud-native-development/) 的文章

# 1.设置 Kubernetes 工作环境

建立一个高效的 Kubernetes 开发工作流的第一步是决定应该使用哪种工作环境。这里，问题不仅在于使用哪种云环境或托管的 Kubernetes 服务，还在于是否应该使用云环境。与生产系统相比，只使用本地 Kubernetes 环境进行开发也是可能的。

## 本地 Kubernetes 或基于云的 Kubernetes

当然，本地和基于云的工作环境各有利弊:虽然本地环境如 [Minikube](https://github.com/kubernetes/minikube) 可以免费使用，但是云环境要花钱。本地环境也可以离线使用，并且完全独立于其他开发人员和其他基础设施。

云环境的优势在于，它们提供更多的计算资源，运行“标准”Kubernetes(不仅仅是在本地计算机上运行的版本)，并且更容易启动。这种环境的供应甚至可以通过[内部 Kubernetes 平台](https://loft.sh/blog/building-an-internal-kubernetes-platform/)实现自动化，因此它们不需要开发人员方面的任何努力或知识。

> *有关本地集群和远程集群优缺点的更多信息，请看本文:* [*本地集群与基于 Kubernetes 开发的远程集群*](https://loft.sh/blog/local-cluster-vs-remote-cluster-for-kubernetes-based-development/)

## Kubernetes 工作环境的设置流程

从工作流的角度来看，为开发人员建立一个标准化的工作环境是很重要的。这当然很大程度上取决于你工作环境的类型。本地环境需要由每个开发人员单独设置，因为它们只在本地计算机上运行，这妨碍了集中设置。这就是为什么您应该提供如何启动本地环境的详细说明。确定使用哪个本地 Kubernetes 解决方案也是有意义的。

如果您使用远程 Kubernetes 环境，您将面临一个完全不同的挑战:虽然创建基于云的工作环境很容易，但是您需要确定开发人员如何访问它们。一些公司可能会决定让管理员集中创建环境，并单独授予开发人员访问这些环境的权限。然而，这将创建过程变成了一个瓶颈，可能会降低整个开发工作流的速度。因此，让开发人员按需创建这些环境会更有效率。像 [Spotify](https://www.youtube.com/watch?v=vLrxOhZ6Wrg) 这样的公司已经为这个用例建立了内部[自助式命名空间](https://loft.sh/blog/self-service-kubernetes-namespaces-are-a-game-changer/)平台。然而，也有现成的软件解决方案，如 [loft](https://loft.sh/) 为任何 Kubernetes 集群提供此功能。

# 2.发展

在开发人员访问了 Kubernetes 工作环境之后，需要确定实际的开发阶段。对于开发阶段，我指的是有时被称为[的软件工程](https://mitchdenny.com/the-inner-loop/)的“内循环”,即编码、构建和结果的观察/测试。

## 如何与 Kubernetes 互动

虽然大多数工程师没有建立 Kubernetes 环境的经验([步骤 1](https://loft.sh/blog/kubernetes-development-workflow-3-critical-steps/#1-setting-up-kubernetes-work-environments) ，但是他们非常熟悉软件开发阶段。尽管如此，与引入 Kubernetes 时他们所习惯的相比，他们的工作流程可能会发生显著变化。

开发人员通常很了解他们使用的编程语言、框架和工具的特性，但 Kubernetes 带来了一些新的挑战，这些挑战大多与实际软件无关:如何将软件容器化？如何在 Kubernetes 中构建和启动容器？如何将代码变更部署到容器中，以便开发人员看到他们的变更？软件怎么调试？

所有这些问题都需要解决，所有的解决方案都应该成为标准化的工作流程。对于这项任务，并非每个开发人员都参与解决最初的一次性问题(例如最初的容器化)，只有一个工程师或一个小团队建立新的工作流，这通常是有意义的。(在另一篇博文中，我更详细地描述了[开发人员体验负责人(DXO)](https://loft.sh/blog/why-every-software-team-should-have-a-developer-experience-owner-dxo/) 的角色。)

## Kubernetes 开发工具

由于许多公司在开发阶段引入 Kubernetes 时面临相同的问题，因此开发了一些开源工具来解决这一领域的问题。这方面的例子有 [DevSpace](https://github.com/devspace-cloud/devspace) 、 [Skaffold](https://github.com/GoogleContainerTools/skaffold) 、 [Tilt](https://github.com/tilt-dev/tilt) 和 [Telepresence](https://github.com/telepresenceio/telepresence) 。

它们都解决了类似的问题，但在概念和技术方法上略有不同:DevSpace 提供了双向、实时的代码同步，允许容器的热重新加载，因此无需重启容器就可以看到代码的变化。(其他工具现在也部分提供了这种功能。)它还关注基于云的 Kubernetes 环境中的开发场景。与此相反，Tilt 更专注于本地 Kubernetes 环境的开发，而 Telepresence 支持本地运行的应用程序的开发，该应用程序可以与远程运行的其他部分进行交互。最后，Skaffold 类似于 DevSpace 和 Tilt，但更侧重于快速部署工作流。

总的来说，所有这些工具都有类似的用途，并且相对通用(例如，Tilt 也可以用于远程环境，DevSpace 也可以用于本地环境或 CI/CD 管道)。要决定哪种解决方案最适合您的情况，您应该亲自查看所有解决方案，因为决定实际上取决于您的偏好和需求。

在任何情况下，您都应该为您想要在您的团队中使用的工具提供一个通用的配置，以便开发人员非常容易使用。例如，这意味着开发人员只需使用很少的命令，如`devspace dev`或`skaffold debug`，就可以直接开始高效地使用 Kubernetes。当然，这需要一些初始的配置和文档工作，但是这些工作会很快得到回报。

# 3.部署

## 如何部署到 Kubernetes 系统

与 Kubernetes 开发工作流密切相关的最后一步是部署。这意味着开发人员需要有一个简单的机会将他们的代码推到一个阶段或测试环境，并最终推向生产。

Kubernetes 工作流中的这一挑战应该相对容易解决，因为大多数开发人员和公司已经习惯了这一点，并且已经有了解决方案。尽管如此，这个阶段的过程对开发人员来说应该是简单而快速的，以便鼓励他们在适当的时候部署他们的应用程序。

## Kubernetes 部署工具

部署到 Kubernetes 的可能解决方案是前面提到的开发工具，最著名的是 Skaffold 和 DevSpace，它们也可以集成到更复杂的 CI/CD 管道中。例如，开发人员只需使用命令`devspace deploy`就可以配置 DevSpace，他们的代码将被部署到一个预先指定的 Kubernetes 集群中执行。这对于快速部署非常实用，例如运行测试。

为了将应用程序部署到生产环境中，存在更复杂的持续集成和部署解决方案。由于 Kubernetes 现在非常普遍，几乎所有的 CI/CD 工具都支持它，所以这些解决方案是否专门针对 Kubernetes 并不重要。你应该再次比较不同的解决方案，看看哪个最适合你的需要。这些工具是一个很好的起点: [Jenkins](https://www.jenkins.io/) 、 [Codefresh](https://codefresh.io/) 、 [Travis CI](https://travis-ci.org/) 和 [Circle CI](https://circleci.com/) 。

# 结论

为了建立一个高效的 Kubernetes 开发工作流，需要定义和简化几个工作流步骤。首先是为开发人员提供一个 Kubernetes 工作环境，它可以在本地或云中运行。然后，他们需要易于使用的 Kubernetes 开发工具来支持开发的“内部循环”,即编码、快速部署和调试。最后，开发人员必须有一种简单的方法将他们开发的代码部署到生产环境中。

所有这些步骤都有一个共同点，那就是它们应该是标准化的，并且对开发人员来说很容易，这样 Kubernetes 的采用就会变得尽可能顺利。在这样做的时候，如果你以前从未使用过 Kubernetes，你永远不要低估它的复杂程度。因此，文档和支持在您组织的整个过程中至关重要。

*最初发布于*[*https://loft . sh*](https://loft.sh/blog/kubernetes-development-workflow-3-critical-steps/)*。*