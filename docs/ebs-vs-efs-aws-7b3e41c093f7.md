# 亚马逊网络服务中的 EBS vs EFS

> 原文：<https://levelup.gitconnected.com/ebs-vs-efs-aws-7b3e41c093f7>

## 了解 AWS 中弹性块存储和弹性文件系统的区别

![](img/7b29a1ee187f80cd93b554960556d94f.png)

由[弗罗林·帕拉马尔丘克](https://unsplash.com/@florinpalamarciuc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

## 介绍

在存储方面，Amazon Web Services 提供了广泛的解决方案，每种解决方案都有自己的优缺点。

在今天的短文中，我们将讨论亚马逊网络服务中两种最基本的存储类型，即弹性块存储和弹性文件系统。此外，我们将讨论它们的主要区别，这将有助于我们理解在哪些特定的用例中使用其中一种可能比另一种更有益。

## 弹性块存储

弹性块存储(EBS)是一个**网络驱动器**，可以将**附加到您的 EC2** 实例上。EBS 卷允许您的实例在终止后保存数据。这意味着您可以重新创建一个 EC2 实例，并挂载旧的 EBS 卷。您甚至可以将它从一个实例中分离出来，并将其附加到另一个实例中。

由于 EBS 卷是网络驱动器，它们使用网络来与实例通信，因此您可能会观察到一些延迟。

请注意，弹性块存储被绑定到特定的可用性区域，这意味着如果 EBS 卷是在`eu-west-1a`中创建的，则它不能被附加到`eu-west-1b`。但是，您可以拍摄在一个可用性分区中创建的原始 EBS 卷的快照，并将创建的快照附加到不同可用性分区中的 EC2 实例。在计费方面，EBS 卷具有调配的容量，这意味着您将为需要调配的完整大小付费。

一般来说，当您需要将高性能存储服务附加到单个 EC2 实例时，您应该使用 EBS。

## 弹性文件系统

另一方面，弹性文件系统(EFS)是一个托管的**网络文件系统(NFS)** ，它可以一次连接到多个 EC2 实例。请注意，与 EBS 不同，EFS 可以使用托管在不同可用性区域中的 EC2 实例。

EFS 最大的优势是它的可扩展性。与 EBS 相比，弹性文件系统可以按需增长和收缩，而无需配置新的基础架构。在计费方面，没有容量规划，这意味着您要为将要使用的存储付费。

现在有了一个额外的存储类别，称为 **EFS 非频繁访问** (EFS-IA)，它针对不经常访问的文件进行了成本优化。与标准 EFS 相比，该课程可为您节省高达 92%的成本。基于生命周期策略，弹性文件系统会根据这些文件被另一个服务访问的最后时间，自动将它们移动到 EFS-IA。这一过程将在幕后进行，对任何访问 EFS 的服务或应用程序都是完全透明的。

通常，当您需要一个共享文件存储，多个 EC2 实例可以利用它时，您应该使用 EFS。此外，当您需要一个可根据商店的实际使用情况进行扩展和收缩的可扩展解决方案时，非常适合。

## 最后的想法

在今天的指南中，我们讨论了如何使用弹性块存储和弹性文件系统在 Amazon Web Services 上存储数据。一般来说，在将高性能存储连接到单个 EC2 实例时，应该使用 EBS，而在需要将高度可扩展的存储连接到多个实例(甚至跨不同的可用性区域)时，应该使用 EFS。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**你可能也会喜欢**

[](https://towardsdatascience.com/aws-ec2-instance-types-a29385912222) [## AWS EC2 实例类型说明

### 了解 Amazon Web Services 上不同类型的 EC2 实例

towardsdatascience.com](https://towardsdatascience.com/aws-ec2-instance-types-a29385912222) [](https://betterprogramming.pub/5-things-to-consider-when-choosing-your-aws-region-484e800cb6f0) [## 选择 AWS 地区时需要考虑的 5 件事

### 如何为你的亚马逊网络服务找到合适的区域

better 编程. pub](https://betterprogramming.pub/5-things-to-consider-when-choosing-your-aws-region-484e800cb6f0)