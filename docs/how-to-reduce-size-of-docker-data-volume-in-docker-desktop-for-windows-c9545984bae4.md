# 如何减少 Docker 桌面的 Docker 数据量

> 原文：<https://levelup.gitconnected.com/how-to-reduce-size-of-docker-data-volume-in-docker-desktop-for-windows-c9545984bae4>

## 超级简单

## 用一个命令调整 Docker 数据卷的大小，并在硬盘上回收大量空间

![](img/b04e57cf580f42faaeb90e17128d8e3a.png)

照片由 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/collections/8865261/docker%2Fcontainer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果您使用 Docker Desktop for Windows，它使用 WSL2，那么您的所有图像和容器文件都存储在一个单独的虚拟卷(vhdx)中。

这种虚拟硬盘能够在需要更多空间时自动增长，直到达到一定的限制。但是，如果您使用`docker system prune --all`或`docker volume prune`来回收空间，vhdx 不会自动收缩。Windows 提供了两种手动收缩虚拟磁盘的方法，以便释放未使用的空间。

# 1.使用优化 VHD

Optimize-VHD cmdlet 可用于优化一个或多个虚拟硬盘文件中的空间分配。它回收未使用的数据块，并重新排列这些数据块以便更有效地打包，这将减少虚拟硬盘文件的整体大小。但是不能调整 ***固定虚拟硬盘*** 的大小。

要启动该流程，请切换到 PowerShell(以管理员身份)并执行:

```
Optimize-VHD -Path "path to your ext4.vhdx" -Mode Full
```

如果您没有将 docker-desktop-data 移动到新的位置([阅读我文章的这一部分以了解如何移动它](https://medium.com/geekculture/beginner-friendly-introduction-into-devops-with-docker-on-windows-6aac2de2db33#ca2c))您可以在这个位置找到它:

```
%LOCALAPPDATA%\Docker\wsl\data\ext4.vhdx
```

# 2.使用 Diskpart 调整 VHD 的大小

如果你不能在你的 Windows 上使用优化 VHD(通常这不能在 Windows Home 中完成)，你可以使用另一种方法来调整你的 docker-desktop-data ext4.vhdx 虚拟磁盘的大小。

您将使用 diskpart 压缩虚拟磁盘。

为此，打开一个终端并关闭 Linux 的 Windows 子系统

```
wsl --shutdown
```

之后，你可以启动`diskpart` 并选择你的 docker-desktop ext4.vhdx

```
diskpart
select dockhdd file="path to your ext4.vhdx"
```

作为一个标识符，我选择了`vdisk`，但是你可以随意命名。

现在，您可以附加、压缩、分离虚拟磁盘，并关闭 diskpart。

```
attach dockhdd readonly
compact dockhdd[ ... this could take some time ]detach dockhdd
close
```

# 结束语

我希望这篇关于调整 Docker 虚拟硬盘大小的文章对你有所帮助，并且能够复制它。

我很想听听你对这些方法的想法和想法。如果你知道另一种方法或有任何问题，请写在下面。如果可能的话，我试着回答他们。

*✍️写的*

***保罗·克努斯****丈夫，两个孩子的父亲，极客，终身学习者，科技爱好者&软件工程师*

*本文最初发表在我的博客上*[*https://www . paulsblog . dev/how-to-reduce-size-of-docker-data-volume-in-docker-desktop-for-windows/*](https://www.paulsblog.dev/how-to-reduce-size-of-docker-data-volume-in-docker-desktop-for-windows/)

***问好*** *🙌***:*[*Twitter*](https://www.twitter.com/paulknulst)*，*[*LinkedIn*](https://www.linkedin.com/in/paulknulst/)*，* [*GitHub*](https://github.com/paulknulst)*