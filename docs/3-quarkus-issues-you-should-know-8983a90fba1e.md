# 你应该知道的 3 个夸库拉姆达问题

> 原文：<https://levelup.gitconnected.com/3-quarkus-issues-you-should-know-8983a90fba1e>

## 我和夸库斯·拉姆达之间的问题

![](img/394aaedb04c16ec413ecd3438ec1ded0.png)

照片由[马体·米罗什尼琴科](https://www.pexels.com/@tima-miroshnichenko?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄，来自[Pexels](https://www.pexels.com/photo/woman-in-black-leather-jacket-sitting-at-the-table-5717262/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)——作者编辑

Quarkus 是一个创建云原生项目的框架。该框架的主要特点是可以使用 GraalVM。

Quarkus 承诺快速启动、低内存占用和良好的开发人员体验。

# 我们在建造什么？

带夸库的 AWS Lambda。

Lambda 应该依次调用两个端点。两个端点具有相同的自定义标头。标头包含授权和附加元数据。标头元数据将作为 lambda 的输入。

*一个端点失效— Lambda 失效。Lambda 失败-生成错误信息。*其中一个端点可能失败，应返回失败原因。

使用 SSL 保护端点。Lambda 需要自定义 CA 证书。信任存储与 Lambda 一起出现。

以下是你可能面临的夸库问题。

# 您可能与`@RequestScoped`有问题

*您想要自定义标题吗？*你需要自定义`ClientsHeadersFactory`。

[来源](https://quarkus.io/guides/rest-client#custom-headers-support)

当 lambda 被调用时，我们的头就出现了。T2 和 T3 都帮不了我们。*我们需要返回一个自定义标题地图。*

您需要一个单独的 bean。

在 lambda 中注入这个 bean，用自定义属性填充头部。

在`CustomClientsHeadersFactory`中注入 bean，并使用定制的头属性。

每个请求都用新的属性填充头。你很想把`@RequestScoped`放到标题上。`@ApplicationScoped`在当前环境下不太适用。

`@RequestScoped`对我没用。lambda 中的 headers 属性为空。

你会发现[上下文传播会引起问题。](https://github.com/quarkusio/quarkus/issues/4826)即使加上`quarkus-smallrye-context-propagation`这个也不行。

把这个豆子留作`ApplicationScoped`。因为每个 lambda 调用都会填充头，所以不会有问题。

# 您需要一个自定义证书

两个端点都受到 SSL 的保护。您需要在`trustStore`中包含证书。

问题是如何包含证书。你在本地测试吗？你在 AWS Lambda 上测试吗？让我们看看你可能会遇到什么问题。

你想在本地测试 Lambda 吗？您将无法从本机映像调用`https`端点。

如果没有正确的证书，对端点的调用将会失败。大多数 CA 证书都是默认的`trustStore`。默认`trustStore`在`$GRAALVM_HOME/Contents/Home/lib/security/cacerts`。

默认情况下，Quarkus 会将这个`trustStore`嵌入到本机 Docker 映像中。无论哪种方式，您都需要添加一个证书才能使其工作。

Docker 图像嵌入了默认的`trustStore`。`function.zip`不会嵌入默认的`trustStore`。当您将功能部署到 AWS 时，这可能会导致问题。

让我们来看看这个问题的解决方案。

要将您的证书添加到`function.zip`中，您需要一个额外的目录。

这个目录叫做`[zip.native](https://quarkus.io/guides/amazon-lambda#modifying-function-zip)`。你应该把它放在`src/main`里。`zip.native`应包含`cacerts`文件和自定义`bootstrap.sh`。

`cacerts`就是`trustStore`。应该持有你需要的所有证件。`bootstrap.sh`应该把这个`trustStore`嵌入到原生的`function.zip`里面。

```
**# bootstrap.sh
#!/usr/bin/env bash** ./runner -Djava.library.path=./ -Djavax.net.ssl.trustStore=./cacerts -Djavax.net.ssl.trustStorePassword=changeit
```

# 您将获得本地主机的证书问题

假设您有一个样例 Express 服务器。这个服务器将模拟真实的端点。对`https://localhost:3000/mockexpressendpoint`的呼叫不起作用。

在本地上调用终结点不起作用。第一个问题是，`localhost`不是正确的 URL。第二个问题，没有针对`localhost`端点的证书。第三个问题，Docker 容器在它自己的网络中，调用`localhost`导致`Connection refused`错误。

让我们看看解决方案。

假设您有一个本地 Express 服务器。联系`localhost`没用。**需要联系** `**host.docker.internal**` **。**

示例 URL 端点:`**https://host.docker.internal/mockexpressendpoint**`

这将解决`Connection refused`问题。你会遇到的下一个问题是证书。

您需要为您的 Express 服务器创建证书。这里有一种创建证书的方法。

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./selfsigneddocker.key -out hostdockerinternal.cer -addext “DNS:host.docker.internal,IP:127.0.0.1”`

添加`**host.docker.internal**`到证书的主题替换名称。使用`-addext`将其添加到备选名称中。这将解决问题:

```
javax.net.ssl.SSLException: Certificate for <host.docker.internal> doesn't match any of the subject alternative names: [xxxxxxx.xxx.xxxxxx.xxx]
```

然后将证书包含在 Express 服务器中。

```
let key = fs.readFileSync(__dirname + '/../selfsigneddocker.key');let cert = fs.readFileSync(__dirname + '/../host.docker.internal.pem');https.createServer({key, cert}, app).listen(3000, () =>console.log(`Example app listening on port 3000!`),);
```

包含证书后，您需要将证书添加到默认的`trustStore`中。导航到`trustStore`目录并执行以下命令。`trustStore`的默认密码是`changeit`。

`sudo keytool -importcert -alias docker -file hostdockerinternal.cer -keystore cacerts`

你有哪些夸夸其谈的问题？请在评论中告诉我。

对于这些问题，你有其他的解决方案吗？评论里说吧。