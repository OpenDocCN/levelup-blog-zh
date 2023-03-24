# Rust 和 Golang Web APIs 性能测试结果—评估数据点

> 原文：<https://levelup.gitconnected.com/rust-and-golang-web-apis-performance-testing-results-objective-data-points-to-choose-between-fd7a84c4272f>

![](img/25211aa26706baf1c4be9233b13d5375.png)

在这篇文章中，我将分享一些用 **Golang** 和 **Rust** 编写的 API 的性能评估数据。在我继续之前，我建议您阅读下面两篇文章，因为这篇文章是这两篇文章的继续。

> 声明:不要仅仅依靠这篇文章来决定 Golang 和 Rust。这不是一篇 A vs B 的文章。其目的是提供一些数据点，供人们在评估时使用。这两种语言在现实世界中可能有不同的用例，在选择任何一种之前，必须对用例进行仔细的评估。

在第一篇文章中，我从理论上描述了对于任何 Web API 开发项目，如何在 Rust 和 Golang 之间进行选择。

[](https://shanmukhsista.medium.com/golang-vs-rust-for-web-api-development-projects-a-quick-comparison-fcc2ae2d0f6d) [## Web API 开发项目的 Golang 与 Rust:快速比较

### 选择 web 框架是一个极其艰难的决定。我们必须真正理解需求、用例，以及…

shanmukhsista.medium.com](https://shanmukhsista.medium.com/golang-vs-rust-for-web-api-development-projects-a-quick-comparison-fcc2ae2d0f6d) 

接下来，我查看了如何使用 Rust 实现一个简单的 REST API，以便为我的比较获得一个基线。我的假设是，就计算性能而言，Rust 将比 go 更快，这是不言而喻的。

[](https://shanmukhsista.medium.com/go-vs-rust-web-api-performance-testing-rust-baseline-part-1-f35c5f21e64b) [## Go vs Rust Web API 性能测试— Rust 基线第 1 部分

### 人们普遍认为 RUST APIs 是性能最好的 API，因为它的语言语义和人工记忆…

shanmukhsista.medium.com](https://shanmukhsista.medium.com/go-vs-rust-web-api-performance-testing-rust-baseline-part-1-f35c5f21e64b) 

这两个 API 在实现方面是相同的。我甚至为一些结构保留了动态数组而不是固定分配，以保持代码尽可能的相似。

# 实施摘要

总之，我使用了一个名为 Echo 的 web 框架来编写我的 API。并对部署进行了分类，以确保我可以标准化远程测试环境。

**所有的机器和测试装置都是全新的，一模一样。**

# 负载测试脚本

在这种情况下，测试脚本保持不变。下面是剧本，供你参考。我将用它来负载测试我们的 API，让 3000 个虚拟用户同时访问我们的服务，并测量性能。

```
import http from 'k6/http';export default function () {
  const url = '[http://x.x.x.x:3000/experiments'](http://10.128.0.2:3000/experiments');
  const payload = JSON.stringify({
        "name":"new_home_page",
        "variants":[
                {
                        "name":"blue_button",
                        "allocation_percent":50.0
                },
                {
                        "name":"red_button",
                        "allocation_percent":50.0
                }
        ]
}
  );const params = {
    headers: {
      'Content-Type': 'application/json',
    },
  };http.post(url, payload, params);
}
```

# 测试命令

```
k6 run --vus 3000 --iterations 1000000 script.js
```

# 结果—场景#1

第一个场景是一个基本的 API 调用，没有任何额外的 CPU 计算。Golang 的一个有趣的观察结果是，这一次 CPU 的峰值达到了 164%。而当我使用 Rust 进行同样的测试时，这个概率是 85%。好消息是，此时请求也没有失败**。因此，就使用这些 API 的消费者而言，没有请求失败。**

# 结果场景#2

在这个场景中，我更新了代码，添加了基于实验名和当前 Unix 时间戳(以秒为单位)的 MD5 散列计算。然后用这个新散列更新 name 字段。我们的目标是看看是否有任何显著的变化是由于安全散列引起的。这主要是出于我的好奇心。

CPU 峰值也达到了 180% — 190%。我假设 GCP 有办法为`e2-medium`实例报告超过 100%的 CPU。这方面还需要研究。在 Rust 的情况下，这仍然是大约 100%的 CPU。

下面是详细的结果。

# 结论

虽然我还在对 CPU 的使用情况进行一些研究，但是两个测试的结果并没有显示出很大的差异。Rust 确实有一个改进的 RPS，但是我不认为它对这个设置来说是足够的。

```
+----------+----------+----------+----------+----------+------+
| Scenario | Latency  | Latency  | Latency  | Requests | CPU  |
|  #1      | (P95)    | (P90)    | (P50)    | /Sec     | Peak |
+----------+----------+----------+----------+----------+------+
| Rust     | 821.16ms | 580.66ms | 253.49ms | 9254     | 85%  |
+----------+----------+----------+----------+----------+------+
| Golang   | 828.11ms | 585.68ms | 257.11ms | 9115     | 160% |
+----------+----------+----------+----------+----------+------+
```

> 免责声明—这个测试是在一个简单的 API 上运行的，没有很多代码。我敢肯定，当我构建这个应用程序时，性能会有所不同，我们有更多的内存和 CPU 计算。

我可以肯定地说一件事。除非我正在构建一个复杂的 **CPU 密集型** / **内存高效型** / **高度安全型**平台即服务，否则我会毫不犹豫地选择 Golang。我喜欢 Rust，我也相信它在很大程度上是一种通用编程语言。

当我们构建后端系统时，随着时间的推移，需求确实会变得复杂，代码的复杂性也是如此。此外，由于这篇文章中提到的原因，我会选择 Golang 来启动所有的项目。随着我的应用程序的增长，如果我注意到我的应用程序有显著的峰值/资源影响，我会计划将这些部分迁移到 Rust。

请随时通过评论或写私人笔记来分享您的想法。

> ***请不要忘记关注我的更多帖子:)***