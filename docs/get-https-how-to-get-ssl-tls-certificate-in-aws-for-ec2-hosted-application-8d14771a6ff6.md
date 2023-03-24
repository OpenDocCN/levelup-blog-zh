# 去找 HTTPS🔐！如何在 AWS 中为 EC2 托管的应用程序获取 SSL\TLS 证书？

> 原文：<https://levelup.gitconnected.com/get-https-how-to-get-ssl-tls-certificate-in-aws-for-ec2-hosted-application-8d14771a6ff6>

今天，我们将看看如何为 EC2 上的网站/应用获得 HTTPS。

如果您之前已经将 nginx 配置为 HTTP，我们需要设置它。一旦你完成了 can，我们就可以进入下一步。

**获得 HTTPS 的步骤**

1.  获得 SSL/TLS 认证
2.  创建负载平衡器
3.  通过负载平衡器路由所有流量
4.  所有交通都要经过 HTTPS

在本例中，我们使用了 route 53 的域名服务。即使你没有一个，你仍然可以使用本教程，稍作修改，这将被提及。

![](img/1fdf3c1d1576d95fbe8dc4f113ee1bf6.png)![](img/929be7b4d20ca44754afe05bed5cea89.png)

# 获得 SSL/TLS 认证

现在我们可以从 AWS 证书管理器获得一个免费的证书

![](img/c90d02ec100cf3a6e2cb2b074c2a4d89.png)![](img/cf2a32891c7ce1a8f2f3fd5b1ee3d2a9.png)

1.  转到证书管理器

![](img/91d1efb96e9c160d04ab90961066786d.png)![](img/a2aaa7c863808f94cdaf790e735a2ba2.png)

2.点击申请证书

![](img/6a5513e9d5d0f536e43c3d66b59274a1.png)![](img/03576fc95fcd01dd0d374d8405ab4792.png)

3.选择公共证书并单击请求证书

![](img/7abb795e03023d26a22ca798e487a804.png)![](img/72d98005fb874236388db116f1806234.png)

4.输入您的域名。com 或任何子域，如果需要的话。&单击下一步

![](img/2ff26c06a381f43682f959b75a32fc04.png)![](img/72c7bd54d3e17075a7e19c4c8ff64748.png)

5.选择 DNS 验证以快速生成证书，当然，如果需要，您也可以使用电子邮件验证&单击下一步

![](img/988b1ab4fae8c1d719407aa6c6745bdd.png)![](img/3da1fd500eec2ef7957a80103a807971.png)

6.如果你想添加标签，你可以，但我现在跳过&点击审查

![](img/a6c5ef3b48316d2c5b06452e765e57ad.png)![](img/5db873b3abe5f658bb8e54e786b18036.png)

7.查看所有详细信息，如果正确，现在单击确认并请求。

![](img/4f050ba3af7602333c5a9536b8ad9581.png)![](img/24547aeb07cbdac77113410df312a9ad.png)

8.现在，您需要在您的托管区域中用这个值创建一个记录。但是 AWS 让你来替你做这项工作。因此，您可以在 53 号公路上点击“创建记录”,或者自己手动创建记录，然后点击“继续”

![](img/2e22b42bb122c31eb38c2a77789fe73b.png)

等待几分钟，你会得到一个已发布的状态。一旦你做到了，你就可以进入下一步。

# 创建负载平衡器

如果您的应用程序托管在 EC2 实例上，我们需要确保我们已经通过负载平衡器传输了它。

![](img/c47fa004049d46e80c67d53e8609ad04.png)![](img/db25f3c7ef256f3f9d0a0312e96eae8f.png)

我们只能通过负载平衡器附加 ACM 证书。

![](img/b38e9e4c9327319a588f4f90ce3b9914.png)![](img/93e4aca9d9175d8bde04c2fd1d7f454c.png)

1.  创建新的负载平衡器选择 https/https

![](img/7e93d6188e49da44bcfc19a283a8008a.png)![](img/d216b4fe25786b9c095a9cd01c0bac3a.png)

2.添加一个监听器，并使用端口 443 添加 https 单击配置安全设置

![](img/b8bc147260b9191ac6fc7b3af0097070.png)![](img/6227bb2529476fc643f4989ddced8c55.png)

3.选择从 ACM 中选择一个证书，并选择您刚刚获得的证书&单击下一步

![](img/751c097ba1f97e92672f263144696eef.png)![](img/ccde0376f0a36120eceb87b5f46ac662.png)

4.选择使用端口 80 连接到目标组的 HTTP 协议，因为您不需要 loadbalncer 通过 HTTPS 与 ec2 实例对话，HTTP 足以完成这项工作&单击 next

![](img/b21dac6a6f3e0ad74be9faf82be09914.png)![](img/caacb15c41fd8c7b746e8d9e97f77a65.png)

5.将 EC2 添加到您的目标组，然后单击“下一步”

![](img/e3978e5ad78bd19489f792bd837174ee.png)![](img/d726804158ffde8cb3dc63adf6d15430.png)

6.查看所有信息，然后单击“创建”

现在，我们已经成功地创建了一个负载平衡器，HTTPS 应该在几分钟内工作。

清除您的浏览器缓存或查看您的目标组是否通过端口 80 设置。

# 所有交通都要经过 HTTPS

这肯定是最短的一步。我们在这里试图实现的是强制流量通过 HTTPS，即使他们试图通过 HTTP。

![](img/3d70ed547d3f35dfba44ccf282976180.png)![](img/0d711a87e064a69fcd4ec10fe7d62121.png)

1.  转到您的负载平衡器并选择您的负载平衡器，然后选择端口 80 并点击编辑

![](img/89ff5c276f664cf61e84ce45533f7841.png)![](img/571d06cdb30eb057caed2ba03be52115.png)

2.将路线更改为重定向至 HTTPS，然后单击顶部的更新。

> *🥳就是这样，你已经成功将你的 HTTP app 改为 HTTPS*

[](https://www.linkedin.com/in/alvisf/) [## 软件工程师- EY | LinkedIn

### 在全球最大的职业社区 LinkedIn 上查看 Alvis F 的个人资料。阿尔维斯有 5 个工作列在他们的…

www.linkedin.com](https://www.linkedin.com/in/alvisf/)