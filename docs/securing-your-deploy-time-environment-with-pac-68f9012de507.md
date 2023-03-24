# 使用 PaC 保护您的部署时环境

> 原文：<https://levelup.gitconnected.com/securing-your-deploy-time-environment-with-pac-68f9012de507>

本文原载于 Magalix
[https://www . Magalix . com/blog/securing-your-deploy-time-environment-with-PAC](https://www.magalix.com/blog/securing-your-deploy-time-environment-with-pac)

您努力确保您开发和部署的代码没有与法规遵从性相关的问题。你和你的团队最不需要的就是另一套界限。更多的规则如何转化为新的和更具创新性的问题解决方案？乍一看，他们不会！然而，在当今的自动化世界中，开放策略代理(OPA)在促进策略即代码(PaC)的同时，让整个团队安心。

# 开放政策代理保持 Kubernetes 干净

为了控制我们的 Kubernetes 集群中发生的事情，使用了一个[准入控制器](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)来执行特定的策略。如果没有这样的控制，我们在 Kubernetes 集群中寻求的许多高级功能将会严重得不到利用。几乎可以控制容器的任何方面来修改集群的组成。

在某种意义上，你可以称之为安全缓冲器，不管速度如何，我们都用它来保持在轨道上。我们不需要记住每一个单独的新政策，我们可以依靠工具来帮助我们前进。除了在进入集群时强制执行映像结构规则之外，依靠 OPA 还允许变异传入的对象。以可控的方式有效地将大量脏活放到自动化上。

OPA 作为准入控制器的使用使事情保持在正轨上，而无需在您的工作流程中注入更多的步骤。大多数团队能够理解不添加更多步骤来将代码投入生产的重要性。如果容器和 Kubernetes 旨在帮助协调开发和基础设施，那么可以把 OPA 想象成指挥者。

# OPA 和网守协同工作

深入研究 OPA 的使用，我们看到它作为接纳控制器的主要功能是帮助奠定服务基础设施的基础。在许多情况下，这可能是有用的。如果实施正确，它可以轻松标准化环境

以相当少的努力来满足任何需要的约束。

列表顶部是准入控制器带来的配置和组织的便利性。要求是在控制器级别设置的，必须满足这些要求才能在环境中成功运行。这可以确保使用标准化的命名约定来命名项目，防止冲突，或者指定从哪里获得容器图像。

与 OPA 协同工作的一个新功能是看门人。当与 OPA 一起用作准入控制器时，网守承诺增加更大的集成层。它以用[减压阀](https://www.openpolicyagent.org/docs/latest/policy-language/)编写的模板中定义的约束的形式注入了一个额外的策略库层。

审核功能允许这些策略按照预定义的时间表扩展到基础架构的任何复制部分。这样做不仅有助于实施现有策略中的配置，也有助于实施新策略中的配置。记住这一点，您可以看到这种类型的实时策略管理在 Kubernetes 集群中的用处。

扩展 OPA Gatekeeper 很快发现自己被注入到开发周期中。像 [Magalix](https://www.magalix.com/) 这样的前瞻性公司正在采用这种准入控制器的基础，并对其进行简化，以提供有用的界面、预配置的策略和整体的安全感。

在我们的示例中，我们只想展示一个使用所需基本组件的可能示例。对于那些想用一个更健壮的解决方案立即投入使用的人，看看 [KubeGuard](https://www.magalix.com/product/kubeguard) 。如果 OPA Gatekeeper 是售票员，KubeGuard 通过安装护栏使其变得更加容易。

# OPA 看门人在行动

在我们的场景中，在上一次 DevSecOps 会议中已经知道，不应该通过标准 HTTP 访问生产集群中的资源。所有流量都应使用 TLS 进行保护，以确保符合公司支付系统的最新要求。

除了开发中的代码，还签入了表示网关守护设备约束信息的其他文件。通过将其存储在外部存储库中，DevOps 工程师能够将其作为 CI/CD 流程的一部分。与管理应用程序代码的方式非常相似，这种策略即代码的方法确保了对变更的历史视图，促进了同行评审，并允许任务转化为实际的代码提交。

我们虚构的应用程序的一部分使用 NGINX 来响应来自 web 前端的 API 请求。虽然它们都在同一个域中，但现在不符合新的规则集。该策略由 DevOps 批准，并使用脚本化流程通过 OPA 网关守护设备来实施。

```
apiVersion: templates.gatekeeper.sh/v1beta1
kind: ConstraintTemplate
metadata:
  name: k8shttpsonly
  annotations:
    description: Requires Ingress resources to be HTTPS only; TLS configuration should
      be set and `kubernetes.io/ingress.allow-http` annotation equals false.
spec:
  crd:
    spec:
      names:
        kind: K8sHttpsOnly
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8shttpsonly
        violation[{"msg": msg}] {
          input.review.object.kind == "Ingress"
          re_match("^(extensions|networking.k8s.io)/", input.review.object.apiVersion)
          ingress := input.review.object
          not https_complete(ingress)
          msg := sprintf("Ingress should be https. tls configuration and allow-http=false annotation are required for %v", [ingress.metadata.name])
        }
        https_complete(ingress) = true {
          ingress.spec["tls"]
          count(ingress.spec.tls) > 0
          ingress.metadata.annotations["kubernetes.io/ingress.allow-http"] == "false"
        }
```

然后，我们通过 DevOps 配置的自动化，使用附加脚本激活以下约束。

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sHttpsOnly
metadata:
  name: ingress-https-only
spec:
  match:
    kinds:
      - apiGroups: ["extensions", "networking.k8s.io"]
        kinds: ["Ingress"]
```

此时，我们还没有重新部署有问题的服务。它已经在集群中运行。我们正在做的是实现一种方法来管理 Kubernetes 中的策略，就像我们用来部署编译好的代码一样。

对于以前通过端口 80 提供服务的容器，现在违反了策略约束。状态消息被中继，并且通过不允许不安全协议上的功能来解决符合性问题。

# 策略代码保护的不仅仅是端口

在本例中，不仅我们的软件代码是 CI/CD 流程的一部分，而且用于通过 OPA Gatekeeper 实现约束的必要文件也是如此。为这些资源保留一个结构化的存储库使得团队能够快速地在新的策略上行动。

这是一种与生俱来的采用策略即代码方法的能力。随着时间的推移，在生产环境中以所需的规模手动实施策略更改将会带来负面影响。如果某种自动化没有发挥作用，这尤其如此。多亏了 OPA Gatekeeper 的许多运作方式，大部分工作都可以以一种帮助我们确切知道会发生什么的方式来计划、讨论和实施。

Magalix 的人员认识到，在实施这些方法时，管理和维护 Kubernetes 集群要容易得多。通过做大量繁重的工作，他们可以轻松地投入并利用这些特性，而不需要太多的学习过程。像前面提到的 [KubeGuard](https://www.magalix.com/product/kubeguard) 和[kubedavisor](https://www.magalix.com/product/kubeadvisor)这样的产品很好地配合在一起，形成了一个有凝聚力的系统

【https://www.magalix.com】最初发表于[](https://www.magalix.com/blog/securing-your-deploy-time-environment-with-pac)**。**