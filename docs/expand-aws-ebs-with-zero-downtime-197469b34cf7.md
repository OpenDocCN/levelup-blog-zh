# 零停机扩展 AWS EBS

> 原文：<https://levelup.gitconnected.com/expand-aws-ebs-with-zero-downtime-197469b34cf7>

![](img/54551e96e9b22bbdfd097b97598fb640.png)

每当您需要扩展 EBS 卷大小时，都可以应用这种方法，以避免停止实例和分离卷。

让我们开始吧。

SSH 到您的实例，并查看可用的存储

![](img/a963b924c9d30c3ee088f2562433f87a.png)

可以看到，存储是 8G aprx。

您可以通过以下方式查看您的

```
df -h
```

现在让我们展开它。

# 步骤:-

从服务列表中选择“EC2 ”,然后选择要扩展的实例

![](img/dfb38975ea90443b26a21cf66ee9235b.png)

单击 EC2 描述下的卷 ID

![](img/4c7604f15f7b67cf70d61c3af7786cb9.png)

选择要调整大小的卷，右键单击“修改卷”

![](img/6cb3e49453631eecab05fc755b78e239.png)

为您的 EBS 卷设置新的大小(在本例中，我将 8GB 卷扩展到 16GB)

![](img/f97655a232fafd5f8044423ad46efa0d.png)

点击是

![](img/e00c45c10fc5d3dca2381cfeea761c8d.png)

检查附加到 EC2 实例的大小

![](img/b746f9d9cea35463f6b3e64cb919781c.png)

现在，我们需要扩展分区本身。

SSH 或通过任何其他方法连接到 EC2 实例，我们刚刚扩展的 EBS 连接到该实例。

![](img/7665c0b3dd50e4c3c25e689b551d1f01.png)

键入以下命令以列出我们的块设备:

```
lsblk
```

![](img/391cf623de7e340c6037bffae66f4058.png)

正如您可以看到的，根卷的*大小反映了新的大小 16GB，分区的大小反映了原始大小 8 GB，并且在您可以扩展文件系统*之前必须进行扩展。

为此，请键入以下命令:

```
sudo growpart /dev/xvda 1
```

![](img/2816a31afece85e92421af161ce2bcbf.png)

> *小心，设备名和分区号之间有一个空格！*

现在，我们可以检查分区是否反映了增加的卷大小(我们可以使用已经使用过的 lsblk 命令进行检查):

![](img/817c8e35e6d91372961ef002630e6965.png)

最后但同样重要的是，我们需要扩展文件系统本身。
如果您的文件系统是 ext2、ext3 或 ext4，请键入:

```
sudo resize2fs /dev/xvda1
```

![](img/8b3e70ed14d17557981f9a6f3593ad5c.png)

如果您的文件系统是 XFS，请键入:

```
sudo xfs_growfs /dev/xvda1
```

最后，我们可以通过键入以下命令来检查我们的扩展文件系统:

```
df -h
```

如果一切顺利，我们应该能够看到我们的有效文件系统扩展大小:

![](img/5a9ffa4076fe8da5221e7c4ee7837732.png)

您刚刚扩展了 EBS 卷大小，没有停机时间

# 结论

如果您发现需要扩展 prod 实例中的空间，这种方法将帮助您在不停机的情况下实现这一目标。