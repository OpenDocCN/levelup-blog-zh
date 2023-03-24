# * devo PS:{ UN }复杂？！

> 原文：<https://levelup.gitconnected.com/devops-un-complicated-cc035429b53b>

![](img/c125df0b96cdaccf9d9c0938da9ef1c3.png)

马克·明特尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## DevOps 指南周期、工具、免费认证等……:)

> 根据 AWS 的说法，我们可以将 DevOps 理解为“文化哲学、实践和工具的结合，提高了公司高速分发应用和服务的能力:以比使用传统流程的公司更快的速度优化和改进产品。软件开发和基础设施管理。这种速度使公司能够更好地为客户服务，并能够在市场上更有效地竞争。”

**#基础设施为代码**

这是一种实践，在这种实践中，我们可以使用来自软件工程师的技术，例如版本控制和持续集成，以编程方式提供和配置基础设施。

通过自动化解决方案，我们能够在不同的场景中促进和增加许多解决方案，以获得更有效和高效的交付，我们可以将基础架构实践用作代码的一些场景包括:

1 .虚拟机管理:流浪者

软件开发过程中的一个大问题是其关于环境及其依赖项的配置，除了开发环境将与服务器非常不同的场景之外，大多数时候，开发人员会浪费一些时间来安装本地工作所需的一切，在服务器上将安装和运行相同的项目(“部署将在此完成”)。

为了尽量减少这个问题，我们必须创建一个虚拟机，它可以在程序员的开发环境中和托管代码的服务器上复制。

要使用编程来配置虚拟机，我们可以使用由 HashiCorp 开发的解决方案 vagger 来简化虚拟机的创建和配置。使用 vagger 和配置虚拟机，我们可以更改网络设置、SSH、端口等。

[](https://www.vagrantup.com/) [## 哈希公司的流浪者

### 无论您是开发人员、操作人员还是设计人员，hashi corp vagger 都能提供同样简单的工作流程。它…

www.vagrantup.com](https://www.vagrantup.com/) 

2.配置管理:傀儡
当我们开始拥有一个有几个虚拟机的环境时，经常配置每一个虚拟机是非常复杂的；配置冲突或机器没有任何配置是很常见的。

//想象一下，您在星期五的中间，必须建立一个有 400 台机器的环境，一次一台…:)

这个问题的解决方案是使用 Puppet(一种通过代码的配置管理器),实际上，它具有一些配置文件，这些文件将在提供与 manager 或 Terraform 结合使用的机器或容器的过程中应用。

[](https://puppet.com/) [## 强大的基础设施自动化和交付|木偶

### 我们希望在扩大客户群的同时确保高效地扩大规模——有了 Puppet，我们可以做到这一点。我们可以关注…

puppet.com](https://puppet.com/) 

//您可能认为可以为此使用 shell 脚本；祝你好运…

3.自动配置:可行

另一个解决方案是 Ansible，它也可以用于自动供应、统一配置管理，并且是 Shell 脚本的理想替代方案。

有了 Ansible，从几个文件中，我们可以在机器或容器上进行最多样化的安装和配置，除了有一个非常活跃的社区来维护这个解决方案之外，只需要在应用配置的目的地安装 Python 和配置 SSH。使用 Ansible，我们可以为最多样化的环境在最不同的场景中创建可重用的自动化。

//它会在你手里建立一个 git lab……:)

[](https://docs.ansible.com/) [## 翻译文件

### 翻译文件

ansi ble Documentationdocs.ansible.com](https://docs.ansible.com/) 

额外:Packer
你是否想象过创建一个映像，在 AWS、Azure 或 Google cloud 等云中提供该映像，使用 Ansible 或 Puppet 配置它，只需一个 JSON 文件，这是 Packer 的想法。

[](https://www.packer.io/) [## 哈希公司打包机

### packer build template . pkr . HCl = = > virtualbox:virtualbox 输出会是这个颜色。==> vmware: vmware 输出将是…

www.packer.io](https://www.packer.io/) 

4.容器

随着时间的推移，人们发现维护多个虚拟机可能会成为一个问题，因为除了在每个虚拟机上安装操作系统及其驱动程序之外，这还是一个消耗大量计算资源的解决方案。

为了解决和虚拟机一样的问题，容器出现了；尽管两者都是 DevOps 文化中广泛使用的解决方案，并且具有隔离应用程序的使命，但是容器消耗更少的计算资源，并且具有更容易的环境管理。以及使用 Kubernetes 和 Rancher 配置。

*   Docker
    这是市场上最著名和最受欢迎的创建定制容器图像的解决方案，可以在 Docker Hub 上公开或私下共享该图像。

[](https://www.docker.com/) [## 增强开发者的应用开发能力| Docker

### 面向 M1 MAC 电脑的 Docker 台式机现在有哪些新功能宣布面向…的 Docker 台式机正式上市

www.docker.com](https://www.docker.com/) 

*   Kubernetes
    这是一个创建和管理用于部署容器的“集群”的解决方案，除了拥有大量的文档和一个大型社区来维护它之外，还有可能通过 API 来处理。

[](https://kubernetes.io/) [## 生产级容器编排

### Kubernetes，也称为 K8s，是一个开源系统，用于自动部署、扩展和管理…

kubernetes.io](https://kubernetes.io/) 

*   Helm
    这是一个强大的 Kubernetes 包管理器，允许通过 CLI 和一个 YMAL 定义文件(图表)使用几个命令来定义、安装和更新 Kubernetes 中最复杂的应用程序。

[](https://helm.sh/) [## 舵

### Helm 是查找、共享和使用为 Kubernetes 构建的软件的最佳方式。Helm 帮助您管理 Kubernetes…

helm.sh](https://helm.sh/) 

//我们已经有了在 Kubernetes 中以自动化方式部署 Apache Spark 和其他大数据解决方案的图表。[图表 _ 火花](https://github.com/helm/charts/tree/master/stable/spark)

*   Rancher
    这是一个 Kubernetes 服务交付平台；也就是说，它将促进 Kubernetes 环境的管理、配置和使用至少 300%。:)

[](https://rancher.com/) [## 处处创新

### Rancher 是一个开源的多集群编排平台，让运营团队能够部署、管理和保护…

rancher.com](https://rancher.com/) 

[//免费认证这里！< <](https://academy.rancher.com/courses/course-v1:RANCHER+K101+2019/about)

**#质量和代码安全性:sonarqude**
通过 sonar qude，我们以自动化的方式检查代码质量及其安全性，从开发阶段到其集成和在生产环境中的持续部署。

[](https://www.sonarqube.org/) [## 代码质量和代码安全性| SonarQube

### 数以千计的自动化静态代码分析规则，在多个方面保护您的应用程序，并指导您的团队。接住…

www.sonarqube.org](https://www.sonarqube.org/) 

**# Registry:Jfrog Artifactory**
注册表是保存容器图像的地方，创建这些图像是为了在适当的时候重用。目前有几种解决方案，其中最常用的是 Jfrog Artifactory，因为它功能多样，易于管理存储库。

//另一种选择是 [Sonatype Nexus](https://www.sonatype.com/) …

**#持续集成(CI):**
在一个开发团队中工作，由于时间的原因，可以注意到许多开发人员将他们的代码放在一个公共的存储库中，只是在交付的最后期限非常接近或几乎接近的时候。我们有很多情况，因为功能停止工作或没有时间做所有的测试和开发。
为了解决这一问题，在文化开发运动中，跑步机将开始出现，并集成和自动化开发的一部分。代码的开发、测试和验收在规定的期限内正确进行。
在代码开发过程中，应用程序的组织通常采用 Mono-Repo，其中只有一个集中的存储库是整个解决方案，或者 Multi-Repo，其中解决方案被划分为不同存储库的独立模块。
在这种意义上，最受欢迎的提议之一是 GitOps，通过 git、SVN 或 GitHub 或 Gitlab 等代码库中的另一个版本控制解决方案进行代码版本控制，Jenkins 的自动跑步机被激活，这将:

*   通过使用 sonarqube 应用自动化测试和创建定制报告，降低风险和风险，提高开发过程中的质量和安全性。
*   通过 Jfrog Artifactory 或 Nexus 等注册表保存编译后的代码、库、二进制文件和创建的映像，以备将来使用。
*   以连续和自动化的方式在开发、认证和生产环境中进行部署，或者使用 Argo 项目。

Jenkins 是一个开源解决方案，用于创建被市场广泛采用的自动化管道，具有大量集成插件，为大多数不同的策略提供了最多样化的解决方案。

[](https://www.jenkins.io/) [## 詹金斯

### Jenkins——一个开源自动化服务器，它使全世界的开发人员能够可靠地构建、测试和…

www.jenkins.io](https://www.jenkins.io/) 

//除了 Jenkins，我们还可以用 [GoCD](https://www.gocd.org/) 、[竹子](https://www.atlassian.com/br/software/bamboo)、 [Travis CI](https://github.com/marketplace/travis-ci) 、[团队城市](https://www.jetbrains.com/teamcity/)、[圈子 CI](https://circleci.com/) 、 [Gitlab](https://about.gitlab.com/product/continuous-integration/) 、 [AWS 代码管道](https://aws.amazon.com/codepipeline/)、 [Azure](https://azure.microsoft.com/pt-br/services/devops/server/) …

**#连续交付/部署 Continuo (CD):**

持续部署通常是持续集成的最后或倒数第二个阶段。整个系统已经过广泛的质量和代码安全性测试，可以在环境中进行安装(部署)。

持续部署和持续交付之间的最大区别是，当团队看到应用程序已经足够成熟，可以在新版本中投入生产时，在批准代码时需要人工交互，为了实现这一点，必须获得相关团队甚至业务领域的批准。

Argo 是许多现有解决方案之一。最大的区别在于它的易用性、丰富的图形界面和高级控制，使得在某些情况下甚至可以使用不同的恢复策略。

[](https://argoproj.github.io/argo-cd/) [## Argo CD -用于 Kubernetes 的声明性 GitOps CD

### Argo CD 是一个用于 Kubernetes 的声明式 GitOps 连续交付工具。应用程序定义、配置和…

argoproj.github.io](https://argoproj.github.io/argo-cd/) 

Argo 的替代方案是 [urban{code}](https://www.urbancode.com/product/deploy/) …

最常用的 CD 策略有:

*   蓝绿色
    部署发生了，生成一个新版本(绿色)并在环境中保留旧版本(蓝色)；在两个版本之间有一个路由器，在某个预先定义的时刻，它会逐渐将访问流导向新版本，同时保持旧版本不可预见的状态。
*   金丝雀
    我们可以认为金丝雀是蓝绿色的进化，只有一小部分用户使用新版本进行测试，不久之后，版本的变化就发生了。
*   特性切换
    这种策略是在部署之前通过代码进行一些配置，将一个给定的用户描述为新版本的可能测试者。

**#微服务/功能即服务(FaaS) /无服务器**

随着 DevOps 文化和大量解决方案和工具的出现，以改善和自动化开发过程，大型应用程序将开始被分离到一组 API 中，随着时间的推移和 Kubernetes 环境的采用，如果已经成为功能的话。现在，前端请求托管在使用无服务器架构的云中的大量功能是很普遍的。

[](https://jlgjosue.medium.com/introduction-to-serverless-and-functions-as-a-service-faas-for-dummies-d764863f842b) [## 介绍无服务器和功能作为一种服务(FaaS)的傻瓜！

### 无服务器？作为服务的功能(FaaS)？目的是甚麽？AWS Lambda？谁使用它？谷歌功能？案例？天蓝色…

jlgjosue.medium.com](https://jlgjosue.medium.com/introduction-to-serverless-and-functions-as-a-service-faas-for-dummies-d764863f842b) 

*   服务 Mash: Istio

通过大量采用 Kubernetes 和以自动化方式创建 API 的便利性，我们开始有了某个功能被极度消耗的场景，每秒有数千个请求，所以像 Istio 这样的服务 Mash 出现了。

Istio 旨在智能控制流量，部署或排除给定服务，通过管理服务的认证、授权和加密实现自动安全，控制使用服务的不同 API 的访问策略，以及监控和创建服务日志。

[](https://istio.io/) [## 伊斯迪奥

### 连接、保护、控制和观察服务。

istio.io](https://istio.io/) 

*   信使/溪流:阿帕奇卡夫卡

处理大量请求的另一个策略是使用事件流的解决方案，比如 Apache Kafka。请求将被记录在一个队列或一组队列中，并在另一个队列中得到应答。

[](http://kafka.apache.org/) [## 阿帕奇卡夫卡

### 超过 80%的财富 100 强公司信任并使用卡夫卡。阿帕奇卡夫卡是一个开源的分布式事件…

kafka.apache.org](http://kafka.apache.org/) 

注意:如果应用在正确的场景中，这两个策略会非常有效！

[](https://javier-ramos.medium.com/service-mesh-vs-kafka-f60c00044f20) [## 服务网格与卡夫卡

### 服务网格和 Kafka 事件驱动架构都非常复杂。这是我试图弄清楚这些的文章…

javier-ramos.medium.com](https://javier-ramos.medium.com/service-mesh-vs-kafka-f60c00044f20) 

**#反馈/监控**

随着分散体系结构在日益多样化的环境中的出现，根据解决方案的不同，监控应用程序变得几乎是不可能的活动，而且这通常只在出现重大问题时才会出现。

//这种情况很普遍，只是当项目已经处于生产环境中时才考虑监视它……:(

除了监控环境之外，一些解决方案还可以检查应用程序或微服务的健康状况；这里有一些。

1.  stack ELK(Elasticsearch+Logstash+Kibana)
    Elastic 公司创建了一套解决方案，以监控日志为目的，随着时间的推移，新的功能已经添加进来，今天我们有可能通过 Beats 以不同的方式监控环境并生成监控日志，使用 Logstash 来处理这些日志中的数据质量，几乎就像 ETL 解决方案、定制搜索引擎 Elastic search 和可视化解决方案 Kibana 一样。

*   节拍:

Beats 是安装在环境中的代理，用于监控和生成可通过 API 请求的日志，监控内存和 CPU 等计算资源的使用情况、应用程序网络、数据文件的完整性，以便在某些云中部署功能即服务(FaaS)。

//除了那些由 Elastic 维护的，主要在 Github 上的社区看到每天都有不同目的的 beats 创建…

[](https://www.elastic.co/beats/) [## beats:Elastic search | Elastic 的数据运输商

### Beats 非常适合收集数据。它们位于您的服务器上，与您的容器在一起，或者作为功能部署-然后…

www.elastic.co](https://www.elastic.co/beats/) 

*   Logstash

这是一个开源解决方案，用于集中日志、转换数据、在目的地插入或索引它们，以及配置和集成 Beats 和其他解决方案，如 Apache Kafka。

[](https://www.elastic.co/logstash) [## Logstash:收集、解析、转换日志

### 刚接触 Logstash？立即启动并运行。观看视频了解如何解析 CSV 文件并将其导入 Elasticsearch…

www.elastic.co](https://www.elastic.co/logstash) 

*   弹性搜索

Elasticsearch 是一个分布式 RESTful 搜索和分析引擎，能够处理大量数据。作为 Elastic Stack 的核心，它集中存储您的数据，以便进行快速搜索、调整相关性和可以轻松扩展的强大分析。

[](https://www.elastic.co/elasticsearch/) [## Elasticsearch:官方分布式搜索和分析引擎| Elastic

### 不熟悉 Elasticsearch？立即启动并运行。观看视频为使用 Elasticsearch 打下坚实的基础…

www.elastic.co](https://www.elastic.co/elasticsearch/) 

*   基巴纳

这只是一个开源的“界面”,用于查看 Elasticsearch 中最多样化的数据形式。

[](https://www.elastic.co/kibana) [## Kibana:探索、可视化、发现数据|弹性

### 刚到基巴纳？这是你开始工作所需要的一切。观看视频，了解使用 Kibana 进行数据分析的核心概念…

www.elastic.co](https://www.elastic.co/kibana) 

//免费和开源产品对于他们的付费版本是有一些区别的。

2.普罗米修斯+格拉法纳

另一种监控策略是让应用程序生成自己的指标，让 Prometheus 对它们进行索引，并使用 Grafana 查看和翻转这些被监控的指标。

*   普罗米修斯

这是一组以监控和生成警报为目标的开源工具，由服务器、库、导出器和警报管理器组成，服务器中的数据以时间序列格式存储，库可在应用程序中用于生成指标，适当的推送网关用于捕获这些指标，导出器用于共享指标数据，警报管理器用于创建警报并通过电子邮件或电报等最多样化的渠道发送警报。

[](https://prometheus.io/) [## 普罗米修斯监测系统和时间序列数据库

### 利用领先的开源监控解决方案增强您的指标和警报能力。普罗米修斯作者 2014-2021 |…

普罗米修斯](https://prometheus.io/) 

*   格拉夫纳

Grafana 是一个开源项目，允许您咨询、可视化、提醒和理解您的指标，无论它们存储在哪里。与您的团队一起创建、探索和共享仪表盘，并通过创建最多样化的可视化动态仪表盘来促进基于数据的文化，这些仪表盘可以探索指标和日志，创建 fin 并集成不同的数据源，无论是 MySQL、MongoDB 等数据库还是 Elasticsearch 等索引搜索解决方案。

[](https://grafana.com/) [## Grafana:开放观察平台

### Grafana 是适用于所有数据库的开源分析和监控解决方案。

grafana.com](https://grafana.com/) 

//可以将 Grafana 与聊天机器人集成在一起，在 Telegram 上进行提醒。

//最近对人工智能(AI)的过度使用(炒作)，我们可以监控环境、容器、功能，但模型、它的质量和效率是另一回事。社区中正在形成的一个策略是模型漂移…

[](https://towardsdatascience.com/model-drift-in-machine-learning-models-8f7e7413b563) [## 机器学习模型中的模型漂移

### 机器学习模型应该如何以及何时被重新训练

towardsdatascience.com](https://towardsdatascience.com/model-drift-in-machine-learning-models-8f7e7413b563) [](https://towardsdatascience.com/why-machine-learning-models-hate-change-f891d0d086d8) [## 为什么机器学习模型讨厌“改变”

### 模型漂移，一种机器学习概念的介绍

towardsdatascience.com](https://towardsdatascience.com/why-machine-learning-models-hate-change-f891d0d086d8) 

**#云基础设施自动化代码:Terraform**

我们目前有几个云选项可以用来维护我们的基础设施，比如 Google Cloud、AWS 和 Azure。我们有不同的配置和不同的管理方式；为了抽象这个困难，我们可以使用 Terraform。

Terraform 是一个基础设施解决方案代码，由 HashiCorp 创建和维护，以简单和自动化的方式提供和管理独立于所选云的基础设施。

在实践中，我们将 Terrafrom 与 Ansible 等其他解决方案结合使用，以便更轻松地应用配置，并以可定制的方式管理环境。

[](https://www.terraform.io/) [## 哈希公司的 Terraform

### Terraform 是一款开源基础设施代码软件工具，它提供一致的 CLI 工作流来管理…

www.terraform.io](https://www.terraform.io/) 

#想象一下，必须在不同的云中提供多个 Kubernetes 节点，同时管理 AWS 和 Azure 上的应用程序…

**#DataOps —面向数据科学的 DevOps】**

随着 DevOps 文化的使用越来越多，数据工程师和架构师开始在这种介质中使用最多样化的解决方案来自动化环境的配置管理和供应，例如，使用 vagger 来创建虚拟机，Ansible 来应用大规模配置，以及使用 Terraform 来在主要的可用云中供应最不同的环境。

[](https://medium.com/data-hackers/o-que-%C3%A9-dataops-organizando-o-futuro-do-data-science-para-neg%C3%B3cios-dc46af338e12) [## o que Data ops:商业数据科学的组织和未来

### 作为一家企业的执行机构，我们将为您提供服务

medium.com](https://medium.com/data-hackers/o-que-%C3%A9-dataops-organizando-o-futuro-do-data-science-para-neg%C3%B3cios-dc46af338e12) [](https://medium.com/data-hackers/dataops-a6d008549aa6) [## 数据操作:摩西大学 trabalho em 数据科学？—数据黑客播客 16

### 这是一种数据操作方法，它影响着我们的业务

medium.com](https://medium.com/data-hackers/dataops-a6d008549aa6) 

许多大数据解决方案已经开始获得针对容器环境的版本，如下所示:

用 Docker 实现 Hive 的一些例子:

[](https://blog.newnius.com/setup-apache-hive-in-docker.html) [## 在 Docker 中设置 Apache 配置单元

### 自从 Hadoop 出现以来，Hadoop 生态系统越来越大。有如此多的软件被…

blog.newnius.com](https://blog.newnius.com/setup-apache-hive-in-docker.html) [](https://github.com/big-data-europe/docker-hive) [## 大数据-欧洲/码头-蜂巢

### 这是 Apache Hive 2.3.2 的 docker 容器。它是基于 https://github.com/big-data-europe/docker-hadoop，所以…

github.com](https://github.com/big-data-europe/docker-hive) 

Kubernetes 的 Apache Spark:

[](https://spark.apache.org/docs/latest/running-on-kubernetes.html) [## 在 Kubernetes 上运行火花

### Spark 可以在 Kubernetes 管理的集群上运行。这个特性利用了原生的 Kubernetes 调度程序

spark.apache.org](https://spark.apache.org/docs/latest/running-on-kubernetes.html) [](https://datenworks.github.io/quickstart-apache-spark-no-kubernetes/) [## 快速入门:你喜欢阿帕奇的火花吗

### Kubernetes？

datenworks.github.io](https://datenworks.github.io/quickstart-apache-spark-no-kubernetes/) [](https://towardsdatascience.com/performance-of-apache-spark-on-kubernetes-has-caught-up-with-yarn-73730878a792) [## Apache Spark 在 Kubernetes 上的表现已经赶上了 YARN

### 了解我们的性能指标评测设置、结果以及关键技巧，以便在运行时将洗牌速度提高 10 倍…

towardsdatascience.com](https://towardsdatascience.com/performance-of-apache-spark-on-kubernetes-has-caught-up-with-yarn-73730878a792) [](https://databricks.com/session_na20/running-apache-spark-on-kubernetes-best-practices-and-pitfalls) [## 在 Kubernetes 上运行 Apache Spark:最佳实践和陷阱

### 自从 Apache Spark 2.3 增加了最初的支持，在 Kubernetes 上运行 Spark 越来越受欢迎…

databricks.com](https://databricks.com/session_na20/running-apache-spark-on-kubernetes-best-practices-and-pitfalls) 

HDFS:

[](https://hasura.io/blog/getting-started-with-hdfs-on-kubernetes-a75325d4178c/) [## Kubernetes 上的 HDFS 入门

### Kubernetes 上 HDFS HDFS 架构的基本架构将 namenode 包装在服务中通过…

hasura.io](https://hasura.io/blog/getting-started-with-hdfs-on-kubernetes-a75325d4178c/) [](https://github.com/apache-spark-on-k8s/kubernetes-HDFS/blob/master/charts/README.md) [## 阿帕奇-k8s 上的火花/库伯内特斯-HDFS

### 在 K8s 集群中启动 HDFS 守护进程的舵图。主要的切入点图表是 hdfs-k8s，这是一个超级图表…

github.com](https://github.com/apache-spark-on-k8s/kubernetes-HDFS/blob/master/charts/README.md) 

HBASE:

[](https://github.com/harryge00/hbase-kubernetes) [## harryge00/hbase-kubernetes

### 基于 kubernetes 之上的 hdfs 的分布式 hbase，具有 2 个主服务器和 2 个区域服务器。hadoop: hadoop 集群运行在…

github.com](https://github.com/harryge00/hbase-kubernetes) 

阿帕奇麒麟:

[](https://medium.com/analytics-vidhya/building-and-managing-apache-kylin-cubes-on-docker-like-a-pro-22fc236c335e) [## 像专家一样在 docker 上构建和管理 Apache Kylin 多维数据集！

### 整体方法。

medium.com](https://medium.com/analytics-vidhya/building-and-managing-apache-kylin-cubes-on-docker-like-a-pro-22fc236c335e) 

阿帕奇气流:

[](https://medium.com/ninjavan-tech/setting-up-a-complete-local-development-environment-for-airflow-docker-pycharm-and-tests-3577ddb4ca94) [## 为 Airflow 设置本地开发环境的完整指南(包含 Docker 和…

### 使用包括 docker-compose、PyCharm 和 DAG 在内的完整设置，轻松协作处理气流工作流

medium.com](https://medium.com/ninjavan-tech/setting-up-a-complete-local-development-environment-for-airflow-docker-pycharm-and-tests-3577ddb4ca94) 

卡夫卡:

[](https://www.confluent.io/blog/getting-started-apache-kafka-kubernetes/) [## Apache Kafka 和 Kubernetes 入门|汇合

### 让每个人都能在 Kubernetes 上运行 Apache Kafka 是我们将流媒体平台放在…

www.confluent.io](https://www.confluent.io/blog/getting-started-apache-kafka-kubernetes/) [](https://www.confluent.io/blog/kafka-devops-with-confluent-kubernetes-and-gitops/) [## 阿帕奇卡夫卡的 DevOps

### 在生产中运行关键的 Apache Kafka 事件流应用程序需要声音自动化和工程设计…

www.confluent.io](https://www.confluent.io/blog/kafka-devops-with-confluent-kubernetes-and-gitops/) [](https://medium.com/swlh/apache-kafka-with-kubernetes-provision-and-performance-81c61d26211c) [## 带有 Kubernetes 的 Apache Kafka 供应和性能

### 我从事 Apache Hadoop 和 Apache Kafka 这样的分布式系统已经很多年了。从来都不容易…

medium.com](https://medium.com/swlh/apache-kafka-with-kubernetes-provision-and-performance-81c61d26211c) 

//许多其他大数据解决方案正在获得适合这一新现实的实施，您准备好了吗？

**#MLOps — ML + DevOps 及更多…**

随着人工智能的普及及其在 2018 年底在最不同媒体中的使用，世界各地的大型社区看到了自动化学习、培训、测试、部署和监控新模型的过程的需求，在某些情况下，需要几个月才能部署到生产环境。

通过许多数据科学家和机器学习工程师对 DevOps 文化的使用，有可能创建定制的跑步机，甚至是开源解决方案，这些解决方案将优化与大多数不同场景和环境兼容的模型的创建和维护。

AI 型号的自动跑步机示例:

[](https://medium.com/analytics-vidhya/polyaxon-argo-and-seldon-for-model-training-package-and-deployment-in-kubernetes-fa089ba7d60b) [## Polyaxon、Argo 和 Seldon，用于在 Kubernetes 进行模型训练、打包和部署

### Kubernetes 中模型管理开源框架的终极组合？

medium.com](https://medium.com/analytics-vidhya/polyaxon-argo-and-seldon-for-model-training-package-and-deployment-in-kubernetes-fa089ba7d60b) 

**#结论**

这份出版物只是对 DevOps 文化、一些不同的现有解决方案、它们的用途以及它们寻求解决的问题(无论是在开发、基础设施、大数据还是人工智能方面)的介绍。

更多参考:

[](https://aws.amazon.com/pt/devops/what-is-devops/) [## AWS DevOps - O que é DevOps？-亚马逊网络服务

### 文化、生产和消费相结合的发展，为企业提供了一个良好的发展空间。

aws.amazon.com](https://aws.amazon.com/pt/devops/what-is-devops/) [](https://www.redhat.com/pt-br/topics/containers/containers-vs-vms) [## 集装箱 x máquinas virtuais

### 容器和混合了 TI 和 e 组件的虚拟 Linux 环境

www.redhat.com](https://www.redhat.com/pt-br/topics/containers/containers-vs-vms) [](https://www.valuehost.com.br/blog/o-que-sao-containers/) [## 集装箱和集装箱码头

### 这是一个非常有趣的故事。数字化和大众化的转变…

www.valuehost.com.br](https://www.valuehost.com.br/blog/o-que-sao-containers/) [](https://buoyant.io/2017/04/25/whats-a-service-mesh-and-why-do-i-need-one/) [## What's a service mesh?And Why Do I Need One?

### We've wrapped up 在这个事后所有的建议和一切,我们已经学会了,因为开始定制的评估过程...

buoyant.io](https://buoyant.io/2017/04/25/whats-a-service-mesh-and-why-do-i-need-one/) 

推荐课程:

[](https://www.alura.com.br/cursos-online-infraestrutura/) [## DevOps 课程 | Alura

### 在基础设施领域内,有几个选项可以遵循。您可以了解 DevOps 世界和交付...

www.alura.com.br](https://www.alura.com.br/cursos-online-infraestrutura/) [](https://www.schoolofnet.com/cursos/infraestrutura/) [## DevOps 和基础设施课程在线 | School of Net

### 了解有关 DevOps 和基础架构的各种技术。访问有关 DevOps,Infra 和...的 700 多个在线课程。

www.schoolofnet.com](https://www.schoolofnet.com/cursos/infraestrutura/) 

谢谢你的阅读!:)