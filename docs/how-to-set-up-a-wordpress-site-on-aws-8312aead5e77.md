# 如何在 AWS 上建立一个 Wordpress 站点

> 原文：<https://levelup.gitconnected.com/how-to-set-up-a-wordpress-site-on-aws-8312aead5e77>

## 一本没有废话的指南，包含了你需要的所有代码片段

![](img/f7ed16ced42c7e4d86c4a85d07f2af89.png)

按照下面链接中指南的第 1 到 5 节，在 AWS Lightsail 上创建一个新的服务器，用 Wordpress 加载它，并获取它的管理员证书:

 [## 教程:在 Amazon Lightsail 中启动和配置一个 WordPress 实例

### 如果你只需要实例(虚拟的……)的话，Amazon Lightsail 是开始使用 Amazon Web Services (AWS)最简单的方法

lightsail.aws.amazon.com](https://lightsail.aws.amazon.com/ls/docs/en_us/articles/amazon-lightsail-tutorial-launching-and-configuring-wordpress#tutorial-launching-and-configuring-wordpress-sign-up-for-aws) 

## 添加您的域

完成第 5 步后，为您的域创建以下 DNS 记录(您通常可以在 DNS 提供商的仪表板中完成此操作):

```
Type: A
Value: The static IP address that you just attached on AWS in step 5
TTL: Any value will work here
```

现在剩下的就是等待更改传播，这应该不会超过 1 小时。

## 向您的站点添加 SSL 证书，启用 https

一旦 DNS 更改已经传播，并且您可以在您的域中看到 Wordpress 页面，访问 shell 并执行以下命令，用您的域替换 YOURDOMAIN.com:

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:certbot/certbot -y
sudo apt-get update -y
sudo apt-get install certbot -y
DOMAIN=YOURDOMAIN.com
WILDCARD=*.$DOMAIN
sudo certbot -d $DOMAIN -d $WILDCARD --manual --preferred-challenges dns certonly
```

> 关于如何使用自己的 ssh 客户端而不是 AWS 提供的可怕的 web shell 进行连接的简短教程，请查看本文底部的附录

按照 certbot 的说明操作，一旦完成该过程并收到证书，运行以下命令以指示您的 web 服务器使用它们并启用 https:

```
sudo /opt/bitnami/ctlscript.sh stop
sudo mv /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server.crt.old
sudo mv /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server.key.old
sudo mv /opt/bitnami/apache2/conf/server.csr /opt/bitnami/apache2/conf/server.csr.old
sudo ln -s /etc/letsencrypt/live/$DOMAIN/privkey.pem /opt/bitnami/apache2/conf/server.key
sudo ln -s /etc/letsencrypt/live/$DOMAIN/fullchain.pem /opt/bitnami/apache2/conf/server.crt
sudo /opt/bitnami/ctlscript.sh start
```

## 移除 bitnami 横幅

运行这些命令:

```
sudo /opt/bitnami/apps/wordpress/bnconfig --disable_banner 1
sudo reboot
```

恭喜，你成功了！享受你的新 Wordpress 网站！

## 附录:使用您自己的 ssh 客户端进行连接

1.  进入您的 [Lightsail 账户页面](https://lightsail.aws.amazon.com/ls/webapp/account/keys)。
2.  单击您刚刚创建服务器的区域旁边的“下载”按钮。
3.  使用以下命令通过 ssh 连接:

```
ssh -i LightsailDefaultKey-eu-central-1.pem [ubuntu@1](mailto:ubuntu@safudex.com).2.3.4
```

将 AWS 服务器的 IP 替换为 1.2.3.4，将下载文件的文件名替换为“LightsailDefaultKey-eu-central-1 . PEM”。