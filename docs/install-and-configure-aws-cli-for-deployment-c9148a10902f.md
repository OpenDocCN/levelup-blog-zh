# 为部署安装和配置 AWS CLI

> 原文：<https://levelup.gitconnected.com/install-and-configure-aws-cli-for-deployment-c9148a10902f>

![](img/4417fe62a548736e3e8e82287dabf299.png)

我假设您有一台普通的机器，没有安装任何与 AWS CLI 相关的东西。为了使这更容易，我将使用命令行，而不是通过网站导航

# 首先:创建一个免费的亚马逊账户

我们将需要它来部署到 AWS。它简单明了。只需按照这里的说明[https://AWS . Amazon . com/premium support/knowledge-center/create-and-activate-AWS-account/](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

这里是 https://portal.aws.amazon.com/billing/signup#/start[的报名页面](https://portal.aws.amazon.com/billing/signup#/start)

# 1.下载 AWS CLI V2

```
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
```

# 2.安装 awscli

```
sudo installer -pkg AWSCLIV2.pkg -target /
```

# 3.验证安装

```
aws --versionOutput will be close but not exact to the following:aws-cli/2.1.28 Python/3.8.8 Darwin/20.3.0 exe/x86_64 prompt/off
```

# 4.安装 XCode 命令行工具

```
xcode-select --install
```

# 配置 AWS CLI

# 1.获取凭据

为了能够使用 AWS CLI，您需要创建一个新用户并获得访问密钥和密码。让我们从控制台开始。请按照之前的视频。在视频中会更容易解释

# 1-搜索 IAM

![](img/a4e3ebf8aecab1321172dc29594a9d25.png)

# 2 —选择 IAM 并输入用户名(这里我使用的是 aws-cli)

![](img/8b8faaabfb63a478e60b4e7a4f075480.png)

# 3-选择编程访问，然后选择下一步:权限

![](img/ab681a92b0af8295bb1ab8180df56598.png)

# 4-选择直接附加现有策略

![](img/ffcacf3c79ced391cda7dfec118e1856.png)

# 5-选择管理员访问权限，然后选择下一步:标记

![](img/8fdeb325ee2119f57c75bb6f6df8ab80.png)

# 6-跳过标签并单击下一步:预览

![](img/e2b14058cda3fb45e9c3ad5d771a5eef.png)

# 7-单击下一步:创建用户

![](img/0d6fbf3d2b12de0896eca41ff60da4a0.png)

# 8-下载凭据 CSV

![](img/a11caf7b730018b592ae274a98cd6f11.png)

# 9-打开下载的文件，它将类似于以下内容

![](img/2b36cd2c7915c43d60ae3a8847b8c07a.png)

我们将在接下来的步骤中使用这些凭据

# 10-打开命令窗口/终端并写入

```
aws configure
```

![](img/2e9dae90fca7e6a34dff9cba2993b40d.png)

# 11-它会要求您输入 AWS 访问密钥 ID

从下载的 csv 中放一个

![](img/c3709a24b0899948e0b76e3276b1eaf4.png)![](img/65e4ace1d82f0eef543e776849488111.png)

# 12-从同一文件添加 AWS 密钥

![](img/227e455c9616acea2fd5c4cb6acb009f.png)![](img/f958814b3fbc71f06fee5a87b330bc7b.png)

# 13 —填写您所在的地区

确保选择您用来创建资源的相同区域(在我的例子中，我使用 us-east-a)

![](img/c875514cd73066fcdb952578d774fe8c.png)![](img/c81a8aa9cbe2191401f7823fe220f638.png)

您可以通过运行来测试

```
aws s3 ls
```

最初发布于 LinkedIn:

[](https://www.linkedin.com/pulse/install-configure-aws-cli-deployment-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/) [## 为部署安装和配置 AWS CLI

### 在我们创建的大多数项目中，我们将经常与 AWS mor 打交道。因此，我决定创造这个…

www.linkedin.com](https://www.linkedin.com/pulse/install-configure-aws-cli-deployment-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/)