# 用于静态分析的 Kubesec

> 原文：<https://levelup.gitconnected.com/static-analysis-kubesec-cc4ed1fb88e3>

## kube sec——安全分析概述

![](img/c42bb1514654bb0139b879cac240bd7b.png)

克里斯多夫·伯恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**kubernetes 资源的静态分析**可以帮助我们识别安全威胁，并在部署前修复它们。

Kubernetes 的静态分析工具之一是 [kubesec](https://kubesec.io/) 。kubesec 将帮助我们分析 Kubernetes 资源的安全风险。

## kubesec —二进制安装:

在 Linux 的情况下，使用 [**uname**](https://medium.com/geekculture/linux-command-dmesg-uname-d2e4f98d6c98) 检查您的 Linux 架构:

```
>> uname --all

Linux controlplane 5.4.0-1093-gcp #102~18.04.1-Ubuntu SMP Sat Oct 29 06:35:49 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

然后从这里获取二进制— [最新发布](https://github.com/controlplaneio/kubesec/releases)。并将其安装在您的系统上。

```
>> wget https://github.com/controlplaneio/kubesec/releases/download/v2.12.0/kubesec_linux_amd64.tar.gz

>> tar -xvf  kubesec_linux_amd64.tar.gz
CHANGELOG.md
LICENSE
README.md
kubesec

>> mv kubesec /usr/bin/
```

现在，我们将扫描以下 pod 清单以分析安全风险:

```
# pod manifest
# pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-1
  name: pod-1
spec:
  containers:
  - image: nginx
    name: pod-1
    securityContext:
      privileged: true
```

## kubesec 扫描:

```
>> kubesec scan pod.yaml

# ---------------------------------------------------------------------------
[
  {
    "object": "Pod/pod-1.default",
    "valid": true,
    "fileName": "pod.yaml",
    "message": "Failed with a score of -30 points",   # failed
    "score": -30,        # <-------
    "scoring": {
      "critical": [
        {
          "id": "Privileged",   # <-------------
          "selector": "containers[] .securityContext .privileged == true",
          "reason": "Privileged containers can allow almost completely unrestricted host access",
          "points": -30
        }
      ],
      "advise": [
        {
          "id": "ApparmorAny",
          "selector": ".metadata .annotations .\"container.apparmor.security.beta.kubernetes.io/nginx\"",
          "reason": "Well defined AppArmor policies may provide greater protection from unknown threats. WARNING: NOT PRODUCTION READY",
          "points": 3
        },
        {
          "id": "ServiceAccountName",
          "selector": ".spec .serviceAccountName",
          "reason": "Service accounts restrict Kubernetes API access and should be configured with least privilege",
          "points": 3
        },
        {
          "id": "SeccompAny",
          "selector": ".metadata .annotations .\"container.seccomp.security.alpha.kubernetes.io/pod\"",
          "reason": "Seccomp profiles set minimum privilege and secure against unknown threats",
          "points": 1
        },
        {
          "id": "LimitsCPU",
          "selector": "containers[] .resources .limits .cpu",
          "reason": "Enforcing CPU limits prevents DOS via resource exhaustion",
          "points": 1
        },
        {
          "id": "LimitsMemory",
          "selector": "containers[] .resources .limits .memory",
          "reason": "Enforcing memory limits prevents DOS via resource exhaustion",
          "points": 1
        },
        {
          "id": "RequestsCPU",
          "selector": "containers[] .resources .requests .cpu",
          "reason": "Enforcing CPU requests aids a fair balancing of resources across the cluster",
          "points": 1
        },
        {
          "id": "RequestsMemory",
          "selector": "containers[] .resources .requests .memory",
          "reason": "Enforcing memory requests aids a fair balancing of resources across the cluster",
          "points": 1
        },
        {
          "id": "CapDropAny",
          "selector": "containers[] .securityContext .capabilities .drop",
          "reason": "Reducing kernel capabilities available to a container limits its attack surface",
          "points": 1
        },
        {
          "id": "CapDropAll",
          "selector": "containers[] .securityContext .capabilities .drop | index(\"ALL\")",
          "reason": "Drop all capabilities and add only those required to reduce syscall attack surface",
          "points": 1
        },
        {
          "id": "ReadOnlyRootFilesystem",
          "selector": "containers[] .securityContext .readOnlyRootFilesystem == true",
          "reason": "An immutable root filesystem can prevent malicious binaries being added to PATH and increase attack cost",
          "points": 1
        },
        {
          "id": "RunAsNonRoot",
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege",
          "points": 1
        },
        {
          "id": "RunAsUser",
          "selector": "containers[] .securityContext .runAsUser -gt 10000",
          "reason": "Run as a high-UID user to avoid conflicts with the host's user table",
          "points": 1
        }
      ]
    }
  }
]
```

如果我们分析上图，我们会注意到 **pod.yaml** 没有通过安全风险分析测试。原因是我们用定义的**特权**模式配置了 pod 清单，这可能是一个主要的安全风险:

```
"critical": [
        {
          "id": "Privileged",   # <-------------
          "selector": "containers[] .securityContext .privileged == true",
          "reason": "Privileged containers can allow almost completely unrestricted host access",
          "points": -30
        }
      ],
```

为了补救(通过安全测试)，我们必须重新配置 **pod.yaml** :

```
# pod manifest
# pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-1
  name: pod-1
spec:
  containers:
  - image: nginx
    name: pod-1
    securityContext:
      privileged: false   #<-----
```

我们已将“**特权**模式”更改为“**假**”。现在再次运行 **kubesec 扫描**命令:

```
>> kubesec scan pod.yaml

[
  {
    "object": "Pod/pod-1.default",
    "valid": true,
    "fileName": "pod.yaml",
    "message": "Passed with a score of 0 points",  #<-----
    "score": 0,
    "scoring": {
      "advise": [
        {
          "id": "ApparmorAny",
          "selector": ".metadata .annotations .\"container.apparmor.security.beta.kubernetes.io/nginx\"",
          "reason": "Well defined AppArmor policies may provide greater protection from unknown threats. WARNING: NOT PRODUCTION READY",
          "points": 3
        },
       ....
        {
          "id": "RunAsNonRoot",
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege",
          "points": 1
        },
       ....
```

现在我们已经通过了考验。但是有一堆建议给我们，以加强安全。目前，我们的分数是 **0。为了提高我们的分数，让我们按照 kubesec 提供的建议修改我们的 pod 清单。**

```
# pod manifest
# pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-1
  name: pod-1
spec:
  containers:
  - image: nginx
    name: pod-1
    securityContext:
      privileged: false
      runAsNonRoot: true   # advised by kubesec
```

现在扫描 kubesec 的 **pod.yaml** 文件:

```
>> kubesec can pod.yaml

# ---------------------------------------------------------------------------
[
  {
    "object": "Pod/pod-1.default",
    "valid": true,
    "fileName": "pod.yaml",
    "message": "Passed with a score of 1 points",
    "score": 1,     # <----
    "scoring": {      
      "passed": [    # <----
        {
          "id": "RunAsNonRoot",
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege",
          "points": 1
        }
      ],
      "advise": [
        {
          "id": "ApparmorAny",
          "selector": ".metadata .annotations .\"container.apparmor.security.beta.kubernetes.io/nginx\"",
          "reason": "Well defined AppArmor policies may provide greater protection from unknown threats. WARNING: NOT PRODUCTION READY",
          "points": 3
        },
...
```

现在，如果我们分析上面的演示，我们会看到我们的分数从“0”提高到了“1”。使用 kubesec 提供的建议，我们可以提高工作负载的安全性。

## kubesec 即服务:

在 v2.kubesec.io/scan[的 HTTPS](https://v2.kubesec.io/scan)也可以买到 Kubesec

可以在我们的系统上不安装 **kubesec** 二进制文件的情况下使用 **kubesec** 。为了实现这一点，我们必须用我们的 kubernetes 清单向远程 kubesec 服务器发送请求。

```
# "kubesec scan" request to remote kubesec server
>> curl -sSX POST --data-binary @"pod.yaml" https://v2.kubesec.io/scan
```

## 使用 kubesec 的其他方法如下:

*   [码头集装箱图像](https://hub.docker.com/r/kubesec/kubesec/tags)在`docker.io/kubesec/kubesec:v2`
*   [Kubernetes 入场管理员](https://github.com/stefanprodan/kubesec-webhook)
*   [Kubectl 插件](https://github.com/stefanprodan/kubectl-kubesec)

> *如果你觉得这篇文章很有帮助，请点击* ***跟随*** *👉******拍手*** *👏* *按钮帮助我写更多这样的文章。
> 谢谢🖤***

## **🚀👉关于 Kubernetes 的所有文章**

**![Md Shamim](img/b46bdc53005abde6c6cb3e8ff0c200c3.png)

[Md 沙米姆](https://medium.com/@shamimice03?source=post_page-----cc4ed1fb88e3--------------------------------)** 

## **关于 Kubernetes 的所有文章**

**[View list](https://medium.com/@shamimice03/list/all-articles-on-kubernetes-7ae1a0f96f3b?source=post_page-----cc4ed1fb88e3--------------------------------)****24 stories****![](img/f1050aa27a3ef03122558b1ba1de1f58.png)****![](img/f1c4131e92176033bce05392de197205.png)****![](img/27d4385154af67764cf37713dbbdc38e.png)**