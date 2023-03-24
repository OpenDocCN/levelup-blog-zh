# Kubernetes:将多个 YAML 文件合并成一个

> 原文：<https://levelup.gitconnected.com/kubernetes-merge-multiple-yaml-into-one-e8844479a73a>

我们想建立一个从单独的 Kubernetes YAML 文件的多资源文件。让我们使用 *Kustomize 创建一个！*

![](img/dce4062b032bb3b64943c8be42311cb3.png)

## 内容

*   第 1 部分:本文
*   第 2 部分:[更改不同环境生产/测试的基本 YAML 配置](https://medium.com/@wuestkamp/kubernetes-change-base-yaml-config-for-different-environments-prod-test-6224bfb6cdd6)

## 什么是多资源文件

这只是一个包含多个资源的 YAML，由三个破折号`—--`分隔

## 使用 Kustomize 的示例

假设我们有四个单独的 YAML 文件:

```
services.yaml
deployments.yaml
namespaces.yaml
storage.yaml
```

为此，我们可以使用 *Kustomize* 。如果您有最新的`kubectl`，那么 *Kustomize* 已经包含在内，否则单独安装:

[](https://github.com/kubernetes-sigs/kustomize) [## kubernetes-sigs/kustomize

### kustomize 让您自定义原始的，无模板的 YAML 文件的多种用途，让原来的 YAML 原封不动…

github.com](https://github.com/kubernetes-sigs/kustomize) 

我们在同一个文件夹中创建一个新文件`kustomization.yaml`。让我们假设这些文件(包括我们新的`kustomization.yaml`)在一个名为`conf-files`的文件夹中。`kustomization.yaml`将看起来像下面的文件:

```
**apiVersion:** kustomize.config.k8s.io/v1beta1
**kind:** Kustomization

**resources:** - services.yaml
  - deployments.yaml
  - namespaces.yaml
  - storage.yaml
```

然后我们运行`kubectl kustomize build conf-files > compiled.yaml`。结果是一个名为`compiled.yaml`的新鲜漂亮的多资源文件。

*Kustomize* 将合并指定的文件，同样**按照正确的顺序**，就像最初创建名称空间等等。

## 等等，还有呢！

Kustomize 可以做得更多，比如为测试或生产中的不同环境修改基本 YAML 文件。这就是它的实际设计目的:)你可以在[第二部分](https://medium.com/@wuestkamp/kubernetes-change-base-yaml-config-for-different-environments-prod-test-6224bfb6cdd6)中看到这一点。

# 成为 Kubernetes 认证

[![](img/cf3901a56841fcb55f9e4e17b9f07672.png)](https://killer.sh)

[https://killer.sh](https://killer.sh)