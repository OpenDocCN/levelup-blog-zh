# Rust And Go Web API 性能测试— Rust 基线#1

> 原文：<https://levelup.gitconnected.com/go-vs-rust-web-api-performance-testing-rust-baseline-part-1-f35c5f21e64b>

![](img/efa3c309ba133978f49765db12d6f98a.png)

由于 RUST APIs 的语言语义和手动内存管理，人们普遍认为它是性能最好的 API。在性能方面，它经常与 C++相比较。让我好奇的一个方面也是 RUST 和 Golang 相比如何。我能收集一些有助于全面评估的数据吗？因此，我决定为一个用 RUST 编写的非常简单的 API 建立一个基线。这篇文章强调了性能的结果。

> 声明:不要仅仅依靠这篇文章来决定 Golang 和 Rust。这不是一篇 A vs B 的文章。其目的是提供一些数据点，供人们在评估时使用。这两种语言在现实世界中可能有不同的用例，在选择任何一种之前，必须对用例进行仔细的评估。

我推荐[阅读我几天前写的一篇比较 Golang 和 RUST](https://shanmukhsista.medium.com/golang-vs-rust-for-web-api-development-projects-a-quick-comparison-fcc2ae2d0f6d) 的文章。那篇文章是这篇文章的动机。Golang 作为一种高效的系统语言，在性能方面可以与 rust 相提并论。但是 Golang 和 rust 的性能真正的区别是什么。我们能对此进行量化吗？这就是我想亲眼看看的原因。

# 设置

设置分为两个阶段和测试。

我创建的第一个设置是 Rust 中的一个新 REST API。[这是一个简单的 API，接受 JSON 输入，反序列化，验证，然后返回响应](https://shanmukhsista.medium.com/real-world-rest-api-using-rust-axum-framework-with-request-validations-and-error-handling-75d4175cef96)。

第二个设置将包括为每个请求计算一个 **MD5 散列。看看这对性能有何影响。我相信这是一个稍微占用 CPU 资源的操作，会对 API 性能产生一些影响。我可能错了！**

我将这个构建归档，然后通过在 GCP 上启动一个新的虚拟机来运行性能测试。这将确保我可以标准化一些设置，并在 Golang 中为相同的 API 重复一次。

# Dockerfile 文件

我的 rust 应用程序的 docker 文件很简单

```
**FROM** rust:1.63.0 **AS *build* WORKDIR /**src**/**openab
**COPY** . .
**RUN** cd management-server **&&** cargo install **--**path .
**RUN** ls **-**al **/**usr**/**local**/**cargo**/**bin

**FROM** debian:stable-slim
**COPY --**from=***build* /**usr**/**local**/**cargo**/**bin**/**management-server **/**bin
**CMD** [**"/bin/management-server"**]
```

发出所有请求的机器将是一个单独的 Docker 实例，具有相同的机器配置。我们将使用`k6`来测试我们的负载。下面是运行测试的脚本和命令。

## 脚本

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

## 测试命令

```
k6 run --vus 3000 --iterations 1000000 script.js
```

该测试使用了一台双核 4 GB 内存的`e2-medium` 机器。我们将对 3000 个虚拟用户同时访问我们的服务器的 100 万个请求进行测试。这应该是测试我们性能的一个重要负载。

# 结果场景 1 —不生成哈希

下面是我们第一次测试的结果。

看着结果，感觉还不错。仅用一个双核 4 GB RAM，我们就能够在 3000 个虚拟用户的情况下**实现 9.5K 的吞吐量**。**机器的 CPU 峰值为 83%。**

# 结果场景#2

对于这个场景，我更新了我们的代码库来生成一个 MD5 散列，用于连接实验名称和 unix 时间戳。我用散列替换实验名，并将其作为响应返回。

```
**let** digest = md5::compute(format!(**"{}-{}"**,e.**name** , since_the_epoch.as_secs()));
e.name = format!(**"{:x}"**, digest) ;
```

我用完全相同的设置运行相同的测试。

结果相当有可比性。即使添加了 md5 散列，结果也有每秒 30 个请求的下降。对延迟(接收响应时间)的影响非常小，但那是对散列的响应。

# 结论

看到结果很有趣，我对吞吐量很满意。3000 个虚拟用户上的 9.5k/秒——对于一台`e2-medium`机器来说，JSON 序列化和反序列化是一个特别好的基准。我注意到的一件事是，CPU 使用率是一致的，没有任何峰值。对于 3000 个虚拟用户同时访问我们的服务，我们的请求延迟或 CPU 性能没有太大差异。结果是可以预测的。

铁锈基线对比到此为止。在接下来的文章中，我将使用 Golang 编写完全相同的程序，并尝试获得这个 API 的一些基线数据。看看会发生什么会很有趣。我对这一个感到兴奋！

> **敬请关注，关注我更多！**