# 在 S3 AWS 上创建网站:一步一步来

> 原文：<https://levelup.gitconnected.com/creating-a-web-site-on-aws-s3-step-by-step-980ecaafa7f1>

![](img/4b563ad90ac9009cb019839349407c1d.png)

我一直在我的文章中使用 AWS S3 部署，特别是微前端，我想给出一些简单的步骤，你可以在 AWS S3 bucket 上创建一个类似于我们在下面的[文章](https://www.linkedin.com/pulse/deploying-micro-frontends-aws-step-using-gitlab-react-rany/)中创建的网站:

[](https://www.linkedin.com/pulse/deploying-micro-frontends-aws-step-using-gitlab-react-rany) [## 使用 Gitlab、React、Webpack 5 和模块联盟逐步将微前端部署到 AWS

### 在我之前的文章(https://levelup.gitconnected。

www.linkedin.com](https://www.linkedin.com/pulse/deploying-micro-frontends-aws-step-using-gitlab-react-rany) 

结果可以在 http://mfe1.s3-website-us-east-1.amazonaws.com/的[查看](http://mfe1.s3-website-us-east-1.amazonaws.com/)

![](img/989bd6bfc41b93276d9a3a8d49f1b2b7.png)

# 首先:创建一个免费的亚马逊账户

我们将需要它来部署到 AWS。它简单明了。只需按照这里的说明[https://AWS . Amazon . com/premium support/knowledge-center/create-and-activate-AWS-account/](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

这里是报名页面[https://portal.aws.amazon.com/billing/signup#/start](https://portal.aws.amazon.com/billing/signup#/start)

# 2-转到 AWS 控制台并登录，然后选择 S3

![](img/ba7f7b8abd1f5057a5b7414a658b872e.png)

# 3-创建一个存储桶

![](img/c8fe2dac3937021ebb0258703ce0ab1f.png)

# 4-选择没有空格或大写字母的唯一名称

![](img/713cde82c77b5fceacd9b64f90b377bb.png)

# 5 —公开

取消选中阻止流量

![](img/e38f600d33943c9d553d745d844d2097.png)

并承认

![](img/6034e6cf823bfcc26b1fed535b24b308.png)

单击创建存储桶

![](img/851a90013f0f891e8b5ceb3bc86a8d69.png)

现在，选择您创建的 bucket(注意，我更改了名称，因为原来的名称不是惟一的)

![](img/22b7d5ca99cdf759e4c632e52a944076.png)

选择属性

![](img/1ba02739dd229a1eb576d81c3700b2a1.png)

在最后，编辑网站托管

![](img/6fc5e31a672087eca338322702d965cc.png)

启用:

![](img/a4835600d2e4d9b13e66d0338b03a6b9.png)

选择主机静态网站

![](img/511d2bd39ea2e2da2f8cbc17c8d652d4.png)

在索引文档和错误页面中写入 index.html

![](img/adc869f4659d798c5d356f56134f6496.png)

单击保存更改

![](img/16aa983648cdb1fbbc50f8f0b5a07573.png)

现在，转到权限

![](img/fd9c78bc409b8c0ada8a1a22bc623930.png)

向下滚动到存储桶策略，然后单击编辑

![](img/175cbcadafad9d1c98cc3b8def630a02.png)

添加以下策略以对您的存储桶启用只读(使用您的存储桶名称更改 xxxxx)

```
{
  "Id": "Policy1615751075403",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1615751021409",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::xxxxx/*",
      "Principal": "*"
    }
  ]
}
```

![](img/df3665ca7bf2321090c9d1dbc87ed62f.png)

单击保存更改

![](img/40fe1953f4500e4c9e66378a96c3aaa5.png)

保存 URL，因为我们稍后会用到它。再次转到属性

![](img/08ff0414b0d0bd7c26736bcedf0ecfc3.png)

滚动到最后，网站托管部分，你会发现桶的公共网址。复制并保存它，因为我们将在本文末尾导航到它

![](img/fc07d527d45dec0fd28fbb672b004744.png)

我的是[http://micro-front-ends.s3-website-us-east-1.amazonaws.com](http://micro-front-ends.s3-website-us-east-1.amazonaws.com/)它将在文章的最后看起来如下

![](img/8a524a8872180833e7d85b91eb7cf978.png)

这就是了。为了测试它，让我们执行以下操作:

1.  按照以下文章在您的计算机上安装和配置 AWS-CLI

[](https://www.linkedin.com/pulse/install-configure-aws-cli-deployment-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0) [## 为部署安装和配置 AWS CLI

### 在我们创建的大多数项目中，我们将经常与 AWS mor 打交道。因此，我决定创造这个…

www.linkedin.com](https://www.linkedin.com/pulse/install-configure-aws-cli-deployment-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0) 

2.克隆微前端项目

```
git clone [https://github.com/ranyelhousieny/react-microfrontends.git](https://github.com/ranyelhousieny/react-microfrontends.git)
```

进入 mfe2 并运行纱线安装

```
Rany>cd react-microfrontends Rany>cd mfe2Rany>yarn install
```

构建 webpack 项目

```
yarn webpack --config webpack.prod.js
```

将 dist 复制到您刚刚创建的 bucket 中(当然，用您的 Bucket 中的名称替换下面的名称)

```
aws s3 sync dist s3://micro-front-ends
```

![](img/70a6162ff80a5968c838b4207a0d9909.png)

如果你导航到你之前保存的网址，你会发现类似下面的[http://micro-front-ends.s3-website-us-east-1.amazonaws.com](http://micro-front-ends.s3-website-us-east-1.amazonaws.com/)

![](img/4b0b9bdf4c3a963f057496112de6733e.png)

恭喜

# 现在，让我们按照这篇[文章](https://www.linkedin.com/pulse/adding-cloudfront-web-enabled-aws-s3-bucket-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/)添加 CloudFront 发行版

[](https://www.linkedin.com/pulse/adding-cloudfront-web-enabled-aws-s3-bucket-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/) [## 将 CloudFront 添加到支持 Web 的 AWS S3 Bucket

### 本文建立在前一篇文章(https://www.linkedin。

www.linkedin.com](https://www.linkedin.com/pulse/adding-cloudfront-web-enabled-aws-s3-bucket-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/) 

您可能会从以下网址获得指向上一个网站的相关文章:

[](https://www.linkedin.com/pulse/micro-frontends-hands-on-example-using-react-webpack-rany) [## 使用 React、Webpack 5 和模块联合逐步实现微前端

### 在这篇文章中，我将一步一步地创建两个微前端反应组件，并呈现一个按钮组件…

www.linkedin.com](https://www.linkedin.com/pulse/micro-frontends-hands-on-example-using-react-webpack-rany) [](https://www.linkedin.com/pulse/deploying-micro-frontends-aws-step-using-gitlab-react-rany) [## 使用 Gitlab、React、Webpack 5 和模块联盟逐步将微前端部署到 AWS

### 在我之前的文章(https://levelup.gitconnected。

www.linkedin.com](https://www.linkedin.com/pulse/deploying-micro-frontends-aws-step-using-gitlab-react-rany)