# 如何用 Kubernetes 部署 Apache Kafka

> 原文：<https://levelup.gitconnected.com/how-to-deploy-apache-kafka-with-kubernetes-9bd5caf7694f>

![](img/e0056b1d1e917aacc5d881c3dcbdb7d2.png)

克里斯蒂亚诺·菲尔马尼在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 咖啡店里的编码

## 在迁移到云之前，通过在本地部署 Kubernetes 开始使用 Kafka。

Kafka 是大型微服务架构系统事实上的事件存储和分布式消息代理解决方案。Kubernetes 是编排容器化服务的行业标准。对于许多组织来说，在 Kubernetes 上部署 Kafka 是一种省力的方法，符合他们的架构策略。

在本帖中，我们将看看在 Kubernetes 上托管 Kafka 的吸引力，提供关于这两种应用程序的快速入门。最后，我们将通过一种与云无关的方法来配置 Kubernetes，以部署 Kafka 及其兄弟服务。

# 技术入门

让我们先来快速回顾一下库伯涅茨和卡夫卡。

## 库伯内特斯

谷歌在 2003 年开始开发 Kubernetes (k8s)。当时，这个项目被称为[博格](https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes/)。2014 年，Google 在 Github 上发布了 k8s 作为[开源项目，k8s 很快通过微软、红帽、Docker 等挑选了合作伙伴。多年来，越来越多的努力使用 Kubernetes，包括](https://github.com/kubernetes/kubernetes) [GitHub](https://github.blog/2017-08-16-kubernetes-at-github/) 本身和流行游戏 [Pokémon GO](https://cloud.google.com/blog/products/containers-kubernetes/bringing-pokemon-go-to-life-on-google-cloud) 。在 2022 年，我们看到 k8s 在 AI/ML 领域的使用越来越多，并且越来越强调安全性。

将 k8s 引入云开发生命周期提供了几个关键优势:

*   零停机部署
*   可量测性
*   不变的基础设施
*   自我修复系统

这些好处很多都来自于 k8s 中声明性配置的使用。为了改变基础设施的配置，必须销毁和重建资源，从而强制实现不变性。此外，如果 k8s 检测到资源偏离了声明的规范，它会尝试重新构建系统的状态以再次匹配该规范。

对于开发人员来说，使用 k8s 意味着最终结束令人沮丧的午夜部署，在这种情况下，您必须放弃一切，直接手动扩展服务或修补生产环境。

## 阿帕奇卡夫卡

Kafka 是一个开源的分布式流处理工具。Kafka 允许多个“生产者”向“主题”添加消息(键值对)。这些消息在每个主题中作为一个队列排序。“消费者”订阅主题，并可以按照消息到达队列的顺序检索消息。

Kafka 托管在一个通常被称为“代理”的服务器上。不同地区可以有很多不同的卡夫卡经纪人。除了 Kafka 经纪人，另一个名为 Zookeeper 的服务使不同的经纪人保持同步，并帮助协调主题和消息。

Kafka 的高明之处在于，它可以在分发的同时，以相对低廉的每 MB 成本，每秒处理数十万条消息。

卡夫卡经常占据类似微服务架构的“中枢神经系统”的位置。消息在生产者和消费者之间传递，实际上，这是你的云中的服务。同一个服务既可以是 Kafka 中相同或不同主题的消息的消费者，也可以是其生产者。

一个示例用例是在您的应用程序中创建新用户。用户服务发布关于“供应用户”主题的消息。电子邮件服务使用这条关于新用户的消息，然后向他们发送一封欢迎电子邮件。用户和电子邮件服务不必直接相互发送消息，但是它们各自的作业是异步执行的。

# 用 Kubernetes 部署 Kafka

对于我们的迷你项目演练，我们将使用 Minikube 以云中立的方式设置 Kubernetes 和 Kafka，这允许我们在单台机器上运行整个 k8s 集群。我们安装了以下应用程序:

*   [Minikube](https://minikube.sigs.k8s.io/docs/start/) 版本 v1.25.2
*   [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) 客户端版本 1.23.3

## Minikube 设置

安装 Minikube 后，我们可以用 minikube start 命令启动它。然后，我们可以看到状态:

```
$ minikube statusminikubetype: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

设置 Kubernetes 在您选择的云提供商中运行的说明可以在每个提供商的文档中找到(例如， [AWS](https://aws.amazon.com/eks/) 、 [GCP](https://cloud.google.com/kubernetes-engine) 或 [Azure](https://docs.microsoft.com/en-us/azure/aks/) )，但是下面列出的 YAML 配置文件应该适用于所有提供商，对 IP 地址和相关字段稍作调整。

## 定义 Kafka 名称空间

首先，我们使用一个名为`00-namespace.yaml`的文件为部署所有 Kafka 资源定义一个名称空间:

```
apiVersion: v1
kind: Namespace
metadata:
  name: "kafka"
  labels:
    name: "kafka"
```

我们使用`kubectl apply -f 00-namespace.yaml`来应用这个文件。

我们可以通过运行`kubectl get namespaces`来测试名称空间是否创建正确，验证 Kafka 是 Minikube 中存在的名称空间。

## 部署动物园管理员

接下来，我们将 Zookeeper 部署到我们的 k8s 名称空间。我们用以下内容创建一个文件名`01-zookeeper.yaml`:

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: zookeeper-service
  name: zookeeper-service
  namespace: kafka
spec:
  type: NodePort
  ports:
    - name: zookeeper-port
      port: 2181
      nodePort: 30181
      targetPort: 2181
  selector:
    app: zookeeper
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: zookeeper
  name: zookeeper
  namespace: kafka
spec:
  replicas: 1
  selector:
    matchLabels:
      app: zookeeper
  template:
    metadata:
      labels:
        app: zookeeper
    spec:
      containers:
        - image: wurstmeister/zookeeper
          imagePullPolicy: IfNotPresent
          name: zookeeper
          ports:
            - containerPort: 2181
```

在这个 YAML 文件中创建了两个资源。第一个是名为`zookeeper-service`的服务，它将使用在名为`zookeeper`的第二个资源中创建的部署。部署使用`wurstmeister/zookeeper` Docker 映像作为实际的 Zookeeper 二进制文件。该服务在内部 k8s 网络的一个端口上公开该部署。在这种情况下，我们使用标准的 Zookeeper 端口`2181`，Docker 容器也公开了这个端口。

我们用下面的命令应用这个文件:`kubectl apply -f 01-zookeeper.yaml`。

我们可以测试成功创建的服务，如下所示:

```
$ kubectl get services -n kafka
NAME               TYPE      CLUSTER-IP     PORT(S)         AGE
zookeeper-service  NodePort  10.100.69.243  2181:30181/TCP  3m4s
```

我们看到了 Zookeeper 的内部 IP 地址(`10.100.69.243`)，我们需要告诉代理在哪里监听它。

## 部署 Kafka 代理

最后一步是部署 Kafka 代理。我们用下面的内容创建一个`02-kafka.yaml`文件，我们用 Zookeeper 的上一步中的`CLUSTER-IP`替换`<ZOOKEEPER-INTERNAL-IP>`。如果不采取这一步骤，代理将无法部署。

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: kafka-broker
  name: kafka-service
  namespace: kafka
spec:
  ports:
  - port: 9092
  selector:
    app: kafka-broker
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: kafka-broker
  name: kafka-broker
  namespace: kafka
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kafka-broker
  template:
    metadata:
      labels:
        app: kafka-broker
    spec:
      hostname: kafka-broker
      containers:
      - env:
        - name: KAFKA_BROKER_ID
          value: "1"
        - name: KAFKA_ZOOKEEPER_CONNECT
          value: <ZOOKEEPER-INTERNAL-IP>:2181
        - name: KAFKA_LISTENERS
          value: PLAINTEXT://:9092
        - name: KAFKA_ADVERTISED_LISTENERS
          value: PLAINTEXT://kafka-broker:9092
        image: wurstmeister/kafka
        imagePullPolicy: IfNotPresent
        name: kafka-broker
        ports:
        - containerPort: 9092
```

同样，我们为一个 Kafka 代理创建了两个资源——服务和部署。我们运行`kubectl apply -f 02-kafka.yaml`。我们通过查看命名空间中的 pod 来验证这一点:

```
$ kubectl get pods -n kafka
NAME                            READY   STATUS    RESTARTS   AGE
kafka-broker-5c55f544d4-hrgnv   1/1     Running   0          48s
zookeeper-55b668879d-xc8vd      1/1     Running   0          35m
```

Kafka Broker pod 可能需要一分钟才能从`ContainerCreating`状态转换到`Running`状态。

注意`02-kafka.yaml`中的那一行，我们在那里为`KAFKA_ADVERTISED_LISTENERS`提供了一个值。为了确保 Zookeeper 和 Kafka 可以使用这个主机名(`kafka-broker)`)进行通信，我们需要在本地机器上的`/etc/hosts`文件中添加以下条目:

```
127.0.0.1 kafka-broker
```

## 测试卡夫卡主题

为了测试我们可以在 Kafka 中发送和检索来自一个主题的消息，我们需要为 Kafka 公开一个端口，使它可以从 localhost 访问。我们运行以下命令来公开端口:

```
$ kubectl port-forward kafka-broker-5c55f544d4-hrgnv 9092 -n kafka
Forwarding from 127.0.0.1:9092 -> 9092
Forwarding from [::1]:9092 -> 9092
```

上面的命令`kafka-broker-5c55f544d4-hrgnv`引用了我们在`kafka`名称空间中列出 pod 时看到的 k8s pod。该命令使该 pod 的端口`9092`在`localhost:9092`时在 Minikube k8s 集群之外可用。

为了方便地从 Kafka 发送和检索消息，我们将使用名为 [KCat](https://github.com/edenhill/kcat) (以前的 Kafkacat)的命令行工具。为了创建名为`test`的消息和主题，我们运行以下命令:

```
$ echo "hello world!" | kafkacat -P -b localhost:9092 -t test
```

该命令应该没有错误地执行，表明生产者在 k8s 中与 Kafka 通信良好。我们如何看到名为`test`的队列中当前有哪些消息？我们运行以下命令:

```
$ kafkacat -C -b localhost:9092 -t testhello world!
% Reached end of topic test [0] at offset 1
```

我们做到了！我们已经成功地用 Kubernetes 部署了 Kafka！

## 后续步骤

Kafka 是对实现实时、事件驱动的架构和系统感兴趣的组织的关键角色。使用 Kubernetes 部署 Kafka 是一个很好的开始，但是组织还需要弄清楚如何让 Kafka 与他们现有的 API 生态系统无缝、安全地工作。他们需要的是在 API 的整个生命周期中处理管理和安全的工具。

在现有的提供商中，我遇到了 Gravitee，这是一个领先的解决方案，特别专注于帮助组织管理、保护、治理和产品化他们的 API 生态系统，无论他们构建的是什么协议、服务或风格。Gravitee 甚至有一个 [Kafka 连接器](https://docs.gravitee.io/apim/3.x/apim_publisherguide_introducing_kafka.html)，它通过暴露端点来接收数据，端点将请求转换成消息，然后可以发布到 Kafka 主题。它还可以通过 Websocket 等网络友好协议将 Kafka 事件传输给消费者。

# 结论

在本文中，我们讨论了 Kafka 如何通过作为一个中枢神经系统向许多不同的服务转发消息来帮助编排微服务架构。为了部署 Kafka，我们研究了 Kubernetes，这是一个强大的容器编排平台，您可以在本地运行(使用 Minikube)或在云提供商的生产环境中运行。最后，我们演示了如何使用 Minikube 建立一个本地 Kubernetes 集群，部署 Kafka，然后使用 KCat 验证成功的部署和配置。

部署愉快！