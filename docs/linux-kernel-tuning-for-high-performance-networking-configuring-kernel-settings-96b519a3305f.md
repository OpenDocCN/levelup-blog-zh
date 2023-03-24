# 配置内核设置

> 原文：<https://levelup.gitconnected.com/linux-kernel-tuning-for-high-performance-networking-configuring-kernel-settings-96b519a3305f>

## 高性能网络系列的 Linux 内核调优

![](img/66401b4fb5867a68d7c150454a15e533.png)

照片由 [Christian Wiediger](https://unsplash.com/@christianw?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Linux 内核配置

当安装了任何版本的 linux 时，内核配置都被设置为每个内核设置的默认值。这些缺省值通常是为了让操作系统在通用环境中启动和运行，在许多情况下这些缺省值是合适的。然而，当运行大容量服务时，比如 nginx，必须调整这些缺省值，以满足高网络和 CPU 吞吐量的要求。

例如，nginx 最初是为了解决 [C10k 问题](https://en.wikipedia.org/wiki/C10k_problem)而创建的，因此是大多数企业网站反向代理 web 服务器的最佳选择之一。然而，无论选择什么样的 web 服务器或者网卡上的管道有多粗，linux 内核配置都控制着任何给定主机上可能的最大吞吐量。

# 配置文件位置

在当前的 linux 内核中，可以通过将文件放入以下文件夹之一(按优先顺序)来修改默认配置:

1.  `**/etc/sysctl.d/**`
2.  `**/usr/lib/sysctl.d/**`
3.  `**/run/sysctl.d/**`

/etc/sysctl.conf 中的设置已过时，可能会被忽略，因此请查看您的系统文档。然而，推荐的最佳实践是将这些文件放在`/etc/sysctl.d/`下，并在文件名前加上一个 2 位数加一个破折号，然后是一个组名，最后是。conf(即:10-nginx.conf)。这些文件将按字典顺序加载，因此必须始终配置的设置应该在具有最高优先级的文件中，以确保它们覆盖字典顺序较低的文件中的任何设置(即:在`/etc/sysctl.d/99-sysctl.conf` *中进行的设置将始终覆盖`/etc/sysctl.d/10-nginx.conf`中的相同设置)。

要激活设置，请运行以下命令(需要 sudo)或重新启动系统:

```
sudo sysctl -p /etc/sysctl.d/10-nginx.conf
```

# 最佳实践

*   将内核设置文件放在`/etc/sysctl.d`下。
*   使用 2 位数的破折号前缀来建立加载文件的层次结构，按照重要性从低到高的顺序。
*   建立一个文件名方案，比如`XX-GROUPNAME.conf`。
*   为文件中的内核设置组创建有意义的组名。
*   使用以下格式注释文件中的每个设置，并使用一两个换行符隔开每个设置，以便于阅读:

```
# full.setting.name
# Description of kernel setting from documentation.
# NOTES
# - Add notes about the setting.
# - Each note describes why it's being set, nuances about the
#   setting, and what it is intended to solve.
# RECOMMENDATION
# - Describe any recommended handling of the kernel setting.
# SEE ALSO
# - Any other settings that need to be considered with this setting.
full.setting.name = value
```

*   通过运行以下命令，将文件名替换为系统上的实际文件，在重新启动之前测试每个文件，以发现任何输入错误:

```
sysctl -p /etc/sysctl.d/10-nginx.conf
```

*   将所有必须始终设置的内核设置放在一个带有“99-”前缀的文件中，并强制执行一个策略，只对所有其他设置组使用小于 99 的值。示例:99-sysctl.conf

*注意:在某些系统上，这是/etc/sysctl.conf 的符号链接

# 结论

这些信息只是一个指南，应该有助于组织 linux 内核设置。

如果这篇文章中的任何信息不准确，请发表评论，我会更新文章以纠正信息。