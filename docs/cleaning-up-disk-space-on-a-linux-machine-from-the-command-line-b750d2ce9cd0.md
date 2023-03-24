# 从命令行清理 Linux 机器上的磁盘空间

> 原文：<https://levelup.gitconnected.com/cleaning-up-disk-space-on-a-linux-machine-from-the-command-line-b750d2ce9cd0>

![](img/20acbc4feebd6544ad9fb1c11fdeefae.png)

照片由[迈克尔·泽兹奇](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我仍然在 AWS 上运行 EC2 实例，这个实例是我几年前配置的，用来托管我的一些副业项目。它使用的 Linux AMI (Amazon 机器映像)已经过时了，而且在我的网站流量很大的时候性能也很差。

最近，我附加到 EC2 实例的 EBS(弹性块存储)卷已满。我不想花一大笔钱来升级 EC2 实例或 EBS 卷的大小，于是开始探索如何删除机器上占用空间的一些不必要的文件。

我绝不是 Linux 方面的专家。因此，我可能谷歌了 20 种不同的东西来寻找可以帮助我的解决方案。堆栈溢出、博客帖子和帮助论坛拯救了我。

现在我正在向前支付它并且分享一些帮助我的命令。

*注意:这些命令适用于 Ubuntu/Debian Linux 发行版。对于其他版本的 Linux，里程数可能会有所不同。*

# 检查磁盘使用情况和可用磁盘空间

要检查机器的磁盘使用情况，并查看剩余的可用空间，您可以在终端中运行以下命令:

```
df -h
```

这列出了您的机器上安装的所有文件系统、它们的位置、它们的大小、使用了多少存储空间以及有多少存储空间可用。

对我来说，这表明我的根文件系统 100%满了！

*来源:*[*https://www . how togeek . com/409611/how-to-view-free-disk-space-and-disk-usage-from-the-the-Linux-terminal/*](https://www.howtogeek.com/409611/how-to-view-free-disk-space-and-disk-usage-from-the-linux-terminal/)

# 自动移除不必要的依赖关系

在 Linux 机器上安装依赖项时，通常使用 APT，这是一种高级打包工具。随着时间的推移，您可能已经安装了不再需要的软件包。要自动删除您可能已经安装的任何不必要的依赖项，您可以运行:

```
sudo apt-get autoremove
```

*来源:*[*https://askubuntu . com/questions/527410/what-is-the-advantage-of-use-sudo-apt-get-autoremove-over-a-cleaner-app*](https://askubuntu.com/questions/527410/what-is-the-advantage-of-using-sudo-apt-get-autoremove-over-a-cleaner-app)

# 清理缓存的包

清理存储在机器上的`/var/cache`目录中的包文件也很有帮助。您可以通过运行以下命令来实现这一点:

```
sudo apt-get clean
```

*来源:*[*https://www . network world . com/article/3453032/cleaning-up-with-apt-get . html*](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)

# 查找和删除文件名与模式匹配的文件

在我使用 Git 的早期，我并没有完全理解`.gitignore`文件的价值，它告诉 Git 忽略某些文件或目录。因为我没能充分利用这个文件，我有很多像`.DS_Store`或 NPM 调试日志这样的文件占据了我的机器空间。

为了找到我机器上所有子目录中的所有`.DS_Store`文件，我运行了:

```
find . -name ".DS_Store"
```

这将列出所有匹配的文件，但不对它们做任何处理。这更像是一次演习。如果您想删除这些文件，您可以运行相同的命令，但是要像这样给它传递`-delete`标志:

```
find . -name ".DS_Store" -delete
```

因为这些文件由于没有在`.gitignore`文件中列出而仍然在我的源代码控制中，所以我也需要提交和推送这个更改，并修改我的`.gitignore`文件以忽略这些讨厌的文件。

*来源:*[*https://UNIX . stack exchange . com/questions/3672/how-do-I-delete-all-files-with-a-a-given-name-in-all-subdirectories*](https://unix.stackexchange.com/questions/3672/how-do-i-delete-all-files-with-a-given-name-in-all-subdirectories)

# 显示所有子目录及其大小，按大小排序

我想看看我的众多项目中哪一个占用的空间最大。为了找到该列表，我运行了以下命令，该命令遍历当前目录中的所有子目录，从最大到最小对它们进行排序，并以人类可读的格式打印它们的大小:

```
du -hs * | sort -hr
```

*来源:*[*https://super user . com/questions/554319/display-each-sub-directory-size-in-a-list-format-using-one-line-command-in-bash*](https://superuser.com/questions/554319/display-each-sub-directory-size-in-a-list-format-using-one-line-command-in-bash)

# 列出一个目录中的所有文件和目录，按大小排序

一旦我确定了我最大的目录，我需要挖掘这些目录中哪些文件占用了太多的空间。

只需运行以下命令，就可以列出给定目录中的所有文件和目录:

```
ls
```

但是，您也可以向该命令传递附加标志，以便对输出进行排序和格式化。通过使用以下命令，您可以列出一个目录中的所有文件和目录，这些文件和目录按其大小排序，并以人类可读的格式显示其大小信息:

```
ls -laSh
```

*来源:*[*https://www . tecmint . com/list-files-ordered-by-size-in-Linux/*](https://www.tecmint.com/list-files-ordered-by-size-in-linux/)

# 在机器上的任何地方搜索大文件

在我浏览了每个目录，清理了我不需要的文件或者删除了我不再展示的项目之后，我仍然有相当多的磁盘空间被用完了。我不容易确定我的项目中哪里出错了，或者是什么占据了所有的空间。

下一个命令是救命稻草。这将搜索超过指定大小(在我的例子中是 50MB)的大文件，并将它们打印到控制台。

```
sudo find / -type f -size +50M -exec ls -lh {} \;
```

我对我的发现感到惊讶。两个最大的罪魁祸首是来自`snapd`的一些我不再需要的缓存文件和来自 MongoDB 的一些我在这个类似开发的环境中不需要的预分配存储空间。

此时，只需运行`rm <filename>`和`rm -rf <directory name>`就可以删除我不想要的文件和目录。

*来源:*[*https://stack overflow . com/questions/2003 16 04/Amazon-ec2-disk-full/2003 21 45*](https://stackoverflow.com/questions/20031604/amazon-ec2-disk-full/20032145)

# 可以走了！

![](img/b9733726e81a131965f8adaa9b8d9ef1.png)

照片由 [Japheth Mast](https://unsplash.com/@japhethmast?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

经过几个小时的故障排除，我终于将磁盘空间使用量恢复到正常水平，EC2 实例也再次正常工作。成功！