# 构建内部 Kubernetes 平台

> 原文：<https://levelup.gitconnected.com/building-an-internal-kubernetes-platform-f714a0b5ef72>

内部 Kubernetes 平台是让您的组织获得 Kubernetes 全部好处的下一步。

![](img/9954ec4ab532a491f9718320b40bdf83.png)

容器编排技术 Kubernetes 已经成为云基础设施的主导解决方案，因此，它正以无与伦比的速度走向成熟。许多公司已经采用或正在采用 Kubernetes。然而[仅仅采用 Kubernetes 是不够的](https://loft.sh/blog/why-adopting-kubernetes-is-not-the-solution/)，你还需要在你的组织中更广泛地传播 Kubernetes。

一些早期采用者展示了进一步采用 Kubernetes 所需的后续步骤:**一个内部 Kubernetes 平台。**

[Spotify](https://www.youtube.com/watch?v=vLrxOhZ6Wrg) 、 [Datadog](https://static.sched.com/hosted_files/kccnceu20/a8/Toolchains%20v3.pdf) (第 25 页)和 [Box](https://www.youtube.com/watch?v=cpIO5h7EuEE) 只是为他们的工程团队建立这种平台的公司的一些公开例子。

# 什么是内部 Kubernetes 平台

内部 Kubernetes 平台是一个允许工程师直接访问公司内部使用的 Kubernetes 按需环境的平台。

从工程师的角度来看，平台必须按需提供[自助式名称空间](https://loft.sh/blog/self-service-kubernetes-namespaces-are-a-game-changer/)或另一个 Kubernetes 环境。它还需要易于使用，即使是没有与 Kubernetes 工作经验的工程师。最后，内部 Kubernetes 平台必须为工程师提供真正的 Kubernetes 访问，这意味着他们可以与 Kubernetes 交互并使用 Kubernetes-原生组件和资源。为此，仅仅使用运行在 Kubernetes 之上的平台即服务系统或者在 CI/CD 管道之后提供 Kubernetes 测试环境是不够的。

从管理员的角度来看，内部 Kubernetes 平台需要集中控制，以便管理员可以监督整个系统，因为在公司范围内推广时，它的使用率将远远高于一些首次采用实验。因此，平台允许有效地管理和限制用户也是必要的，这样运行内部 Kubernetes 平台的总成本才不会失控。

一个好的内部 Kubernetes 平台将所有这些特性结合在一个单一的解决方案中，支持所有利益相关者进一步采用 Kubernetes。

# 为什么你应该有一个内部的 Kubernetes 平台

随着越来越多的应用程序运行在 Kubernetes 之上，Kubernetes 的采用也有必要在组织内部传播，以便开发人员可以直接与他们应用程序的底层技术进行交互。只有满足了这一要求，Kubernetes 的优势即更快的开发周期和增强的应用程序稳定性才能得以实现。

正如在 [Stack Overflow 开发者调查 2020](https://insights.stackoverflow.com/survey/2020#technology-most-loved-dreaded-and-wanted-platforms-wanted5) 中可以看到的，开发者已经为下一步做好了准备，因为 Kubernetes 是一项非常“受欢迎”和“受欢迎”的技术，也就是说，开发者希望使用它，已经使用它的人也喜欢它。

使工程师能够使用 Kubernetes 的第一步是为他们提供使用 Kubernetes 的个人工作环境。这正是内部 Kubernetes 平台所做的，这也是它在成功进一步采用中如此重要的原因。

# Kubernetes 存在什么样的环境来构建内部平台

理论上，工程师可以通过几种方式直接访问 Kubernetes:本地 Kubernetes 集群、单个集群或共享集群。

**1。本地集群:**本地集群是专门为在工程师的计算机上运行而制作的 Kubernetes 版本，如 [minikube](https://github.com/kubernetes/minikube) 或 [kind](https://github.com/kubernetes-sigs/kind) 。因此，它们必然受限于本地可用的计算资源，并且不具备在云环境中运行的“真正的”Kubernetes 中存在的所有 Kubernetes 特性。本地运行时环境进一步使得无法简化设置过程，因此需要由工程师自己来完成，这需要一些 k8s 专业知识。

**对于这一点，本地 Kubernetes 集群非常适合于初始采用和试验阶段，并且非常受欢迎，但它们不是进一步采用或构建内部平台的正确解决方案。**

**2。单个集群:**单个集群是只由一个工程师使用的集群。可以构建一个内部平台，为您的工程师提供完整的独立集群。原则上，EKS、AKS 和 GKE 已经是“外部”的 Kubernetes 平台，如果您让每个工程师都可以访问这些公共云提供商解决方案，您将拥有类似于“内部”Kubernetes 平台的东西。然而，这种解决方案非常昂贵，因为计算资源的使用效率极低(没有计算资源的集群管理费已经是每个工程师每月 70 美元)。对于管理员来说，监督一个拥有大量集群的系统，找出哪些还在使用，哪些可以删除，也要困难得多。最后，大多数公司不希望让每个工程师都能直接访问他们的云提供商账户。

**因此，为工程师建立一个具有独立集群的内部 Kubernetes 平台在理论上是可能的，但这将非常昂贵且低效，这就是为什么它在现实中几乎不会被实现。**

**3。共享集群:**共享集群是由几个工程师或团队使用和共享的多租户集群。共享集群是真正的基于云的 Kubernetes 集群，因此基本上拥有无限的计算资源，并且可以按照与生产系统相同的方式进行配置。同时，整个工程团队只需要一个集群，这使得管理员可以轻松管理和控制，并保持较高的资源利用率。

共享集群是构建内部 Kubernetes 平台的首选(也是唯一可行的)方式。

> *要获得不同类型 Kubernetes 环境的更详细的比较，请查看本地和远程集群的* [*比较*](https://loft.sh/blog/local-cluster-vs-remote-cluster-for-kubernetes-based-development/) *和单独和共享集群的* [*比较*](https://loft.sh/blog/individual_kubernetes_clusters_vs-_shared_kubernetes_clusters_for_development/) *。*

## 命名空间与 vClusters

如果您的内部平台使用共享集群环境，工程师通常会使用名称空间，这是在 Kubernetes 中建立多租户的基础。对于许多用例来说，通过名称空间分离用户就足够了，但是还有[虚拟 Kubernetes 集群(vClusters)](https://loft.sh/blog/introduction-into-virtual-clusters-in-kubernetes/) 提供了一种更困难的多租户形式，因此为您的平台提供了更大的稳定性。vClusters 还有一个额外的优势，那就是它给工程师们一种使用单个集群的感觉，这样工程师们就可以根据需要自由地配置一切，例如，他们甚至可以独立地选择他们想要使用的 Kubernetes 版本。

## 手动与自助服务

现在，问题仍然是如何让工程师访问共享集群上的命名空间或 v cluster。一个简单的解决方案是，集群管理员为工程师手动创建和分发命名空间和 v cluster。然而，这将产生一个非常有问题的瓶颈，这对工程师来说将是一个巨大的生产力杀手，正如在 [VMware 调查](https://tanzu.s3.us-east-2.amazonaws.com/campaigns/pdfs/VMware_State_Of_Kubernetes_2020_eBook.pdf)中可以看到的那样，该调查发现“等待中央 IT 提供对基础架构的访问”是开发人员生产力的头号障碍。

**为此，你需要一个易于使用的自助服务平台，这样开发者就可以从一开始就高效地使用它，而不必先了解很多关于 Kubernetes 的细节。**

# 你如何建立一个内部的 Kubernetes 平台

## 1.选择 Kubernetes 平台

如前所述，您希望用共享集群构建您的内部平台。不过，您需要决定您的内部平台将运行在哪个 Kubernetes 平台上。

在这里，您必须在公共云和私有云环境之间做出选择。最受欢迎的公有云环境有 [EKS(亚马逊 Web 服务)](https://aws.amazon.com/eks/)、 [AKS(微软 Azure)](https://azure.microsoft.com/services/kubernetes-service/) 和 [GKE(谷歌云)](https://cloud.google.com/kubernetes-engine)。

或者，也可以使用多云或混合云方法，这种方法结合了几个云提供商，甚至是公共云和私有云。运行这种类型的系统，像牧场主和 T2 这样的特殊工具会非常有用。

对于您的内部 Kubernetes 平台来说，一个好的起点是只使用一个最能反映您的生产系统环境的环境。例如，如果您在生产中使用 EKS 用于您的 Kubernetes 应用程序，那么从 EKS 开始用于您的内部开发、测试、CI/CD 平台也是有意义的……如果您已经在生产中有了一个多云系统，您需要评估重新创建该设置是否有意义，或者单个云系统是否足够。

## 2.定义平台架构

下一步，您需要确定您的平台应该如何工作，以及架构应该是什么样子。

这方面的一个问题是平台用户的身份验证将如何工作，因为他们需要帐户来访问它。有几个选项可以处理用户管理的这一部分。最简单的方法是让管理员集中创建用户帐户。如果你有一个相对较小的团队，并且没有太大的波动，这是一个很好的选择。在较大的团队中，让用户自己登录是有意义的，例如，允许具有特定电子邮件地址的用户创建新帐户，或者实现单点登录(SSO)系统，该系统使用您已经拥有的另一个服务的用户管理，例如 GitHub。对于这样一个系统的实现，你可以看看 [dex](https://github.com/dexidp/dex) ，它是一个 OpenID Connect 提供者，连接到许多不同的服务，比如 LDAP、GitHub、GitLab、Google、微软和许多其他服务。

您需要回答的另一个问题是，内部平台的用户将如何与之交互。在这里，您可以选择图形用户界面(对初学者来说可能是最容易使用的，但也是最难构建的), CLI(可以很好地集成到工程师的工作流中),或者 Kubernetes 自定义资源定义(CRDs ),这是最接近基本 Kubernetes 的实现。当然，也可以组合不同的选项，例如通过提供在后台创建 CRDs 的 GUI。

## 3.搭建你的平台

构建 Kubernetes 平台的第三步是实际的设置。在这里，你面临一个制造或购买的决定。

从头开始构建自己的平台的主要好处是，这样的平台是完全可定制的，因为您可以确定它的每个部分。同时，这意味着设置需要大量的工作和时间。你还应该预料到，随着用户需求和 Kubernetes 自身的发展，你必须不断地改进你的平台。为此，选择构建自己的平台最适合有非常特殊需求的组织，例如，他们在受监管的行业中工作，或者对于规模如此之大以至于与巨大的努力相比，一些定制好处是值得的公司。以 Spotify 为例，它决定建立一个自己的平台，仅供内部使用。如果你决定走这条路，你仍然可以构建现有的解决方案，比如前面提到的 dex 或 [kiosk](https://github.com/kiosk-sh/kiosk) ，它是 Kubernetes 的多租户扩展。

然而，对于大多数公司来说，只购买现有的解决方案可能是更好的选择，因为设置更容易、更快，您不必投入永久的开发资源来改进平台，并且您将自动获得一些最佳实践。同时，这种解决方案在定制方面只有有限的选择，这就是为什么它可能不适用于非常专业化的公司。一个先进的现成内部 Kubernetes 平台解决方案是 [loft](https://loft.sh/) 。loft 可以与任何 Kubernetes 集群一起工作，它提供了一个 GUI、一个 CLI，并且完全基于 Kubernetes CRDs。它还负责用户管理，允许 SSO，并具有一些附加功能，如通过关闭未使用的命名空间和 vClusters 来节省成本的睡眠模式。

## 4.搭载您的工程师并提供开发工具

在您建立了您的内部 Kubernetes 平台之后，您需要准备在您的组织中推广。在这里，你应该记住，对于许多以前没有使用过 Kubernetes 的工程师来说，不仅平台是新的，工作流程也是新的。为此，您应该为平台和新的开发/测试/部署工作流准备大量的文档。为了让工程师尽可能轻松地完成过渡，有必要尽可能多地进行预配置和实施智能默认设置。

在这个阶段，为工程师提供合适的开发工具非常有帮助，尤其是因为这些工具中的大多数都可以针对您的典型用例进行预配置。专门为开发人员制作的工具的例子有 [DevSpace](https://github.com/devspace-cloud/devspace) ，如果这是您选择的平台，它还具有可选的 [loft integration](https://github.com/loft-sh/loft-devspace-plugin) 、 [Skaffold](https://github.com/GoogleContainerTools/skaffold) 和 [Tilt](https://github.com/tilt-dev/tilt) 。

作为一种推广策略，从单个团队开始并逐步推进通常是一个好主意。这使您可以单独培训团队，并了解他们面临的主要挑战。如果您额外测量实际采用和使用情况，您可以进一步发现意想不到的挑战和阻力。有了这些信息，您就可以一步一步地改进您的系统和文档，这样随着时间的推移，团队中的采用过程会变得更加容易。

## 5.控制成本

随着 Kubernetes 在您的组织中的日益普及，成本将成为一个更重要的因素，因为现在，每个工程师都在永久使用基于云的 Kubernetes 环境。因为您使用的是共享环境，所以云资源的使用通常应该相对高效。尽管如此，仍有一些可以显著降低云成本的改进领域。

为了找到这些方面，并更好地了解您的成本结构，例如，哪个团队导致了哪个成本，您应该监控成本。为此，像 Kubecost 或 Replex 这样的工具会很有帮助。

您还应该尽早开始成本控制措施:最重要的措施是限制工程师对计算资源的使用。这将防止因工程师的错误而导致的额外成本。您还应该为您的集群激活[水平自动缩放](https://kubernetes.io/de/docs/tasks/run-application/horizontal-pod-autoscale/)，因为这将使您的计算能力适应当前需求。

这也是实现“[睡眠模式](https://loft.sh/docs/sleep-mode/basics)”的一个要求，这是一个自动系统，它关闭未使用的名称空间或 vClusters，从而防止资源闲置。如果您想象工程师在晚上、周末或会议期间不需要计算资源，但如果他们继续运行，您仍然需要支付费用，那么这种睡眠模式结合水平自动扩展可以节省大量资源(约 70%)。

# 结论

如果您已经成功采用了 Kubernetes，并且现在想要采取下一步措施，那么为您组织中的其他工程师提供 Kubernetes 访问是不可避免的。最好的解决方案是一个内部 Kubernetes 平台，允许工程师按需创建 Kubernetes 工作环境。这样的平台允许在您的组织内标准化 Kubernetes 的使用，因此即使非 Kubernetes 专家也可以使用它，而管理员保持集中控制和监督。

无论您是决定从头开始构建这样一个平台，还是仅仅购买一个现有的平台解决方案，投资都是值得的，因为只有内部 Kubernetes 平台和每个工程师都可以访问，您才能获得 Kubernetes 的全部好处，例如更快的开发周期、更高的稳定性和最终更好的软件。

由[克林特·王茂林](https://unsplash.com/@clintadair?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*最初发布于*[*https://loft . sh*](https://loft.sh/blog/building-an-internal-kubernetes-platform/)*。*