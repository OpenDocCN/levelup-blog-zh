# 加密数据库备份并保存在谷歌云平台上

> 原文：<https://levelup.gitconnected.com/encrypt-database-backup-and-save-on-google-cloud-platform-2f0421987569>

## 为了改进备份功能，我添加了同步加密备份到 GCP。

![](img/3664c00a3264431047f44a78f845ef2f.png)

来自 [Pexels](https://www.pexels.com/photo/close-up-photo-of-mining-rig-1148820/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [panumas nikhomkhai](https://www.pexels.com/@cookiecutter?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 摄影

# 介绍

在我用一个`cronjob`设置了我的数据库备份之后，我在想一个好的解决方案，在服务器崩溃的情况下也有备份。

您可以在此阅读我如何创建自己的简单备份策略:

[](/everybody-needs-backups-a-lesson-learned-the-hard-way-b4b720afaa3a) [## 每个人都需要备份——这是一个惨痛的教训

### 我的 Docker 群中的一个大事件，以及事件发生后我开发的解决方案。

levelup.gitconnected.com](/everybody-needs-backups-a-lesson-learned-the-hard-way-b4b720afaa3a) 

第一个想法是类似于在我的笔记本电脑上运行并每天下载数据库的`rsync`作业。但这不是一个真正好的解决方案，所以我考虑了一个包括云存储的解决方案。因为 GCP 有 300 美元的免费学分，所以我选择了它而不是 AWS。

我在这里创建了一个免费账户[，然后我创建了我的第一个存储桶。](https://console.cloud.google.com/)

# 安装 Google Cloud SDK

在云控制台的初始设置完成后，我执行了以下步骤将我的服务器连接到 google storage。如果你正在和一个码头工人群体一起工作，这个过程必须在我的群体中的每个工人身上重复。如果你想建立一个 Docker Swarm， [**你可以从我的另一篇文章**](/docker-swarm-in-a-nutshell-ed2a9c42cd7c) **中读到。**

**下载谷歌云 SDK**

```
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-353.0.0-linux-x86_64.tar.gz 
```

**提取它**

```
tar -xf google-cloud-sdk-353.0.0-linux-x86_64.tar.gz
```

**安装 SDK**

```
./google-cloud-sdk/install.sh
```

安装过程完成后，您必须重新连接外壳！

**初始化 SDK**

```
./google-cloud-skd/bin/gcloud init
```

在初始化过程中，我必须登录我的谷歌账户

**找到水桶**

```
gsutil ls
```

现在机器已连接到 GCP，我可以使用 ***gsutil*** 来备份我的数据。

# 加密

现在我在每台服务器上都有了 google 连接，我可以开始创建备份脚本了。但是我首先收集了关于如何压缩数据库备份并以加密方式存储它们的信息。**谷歌不应该有我的数据库！**我知道: ***偏执***

我选择了一个加密过程，该过程使用系统上的密码文件(我也将该文件下载到了我的机器上)。所以我必须创建一个安全的密码，并将其导出到一个变量中，然后可以在脚本中使用。

在文件中生成随机密码

```
head -c 100 /dev/urandom | strings -n1 | tr -d '[:space:]' | head -c 40 >> ~/.pass
```

将文件导出到。当我在壳里面的时候，巴沙尔使用它

```
echo "export PASS=~/.pass" >> ~/.bashrc && source ~/.bashrc
```

使文件"**安全**

```
chmod 400 ~/.pass
```

有了这些`.pass`文件，我可以加密和压缩一个文件夹:

```
tar czf - . | openssl enc -e -aes-256-cbc -pbkdf2 -iter 100000 -salt -out archive.enc -pass env:PASS
```

为了解密，我必须使用:

```
openssl enc -d -aes-256-cbc -pbkdf2 -iter 100000 -salt -in archive.enc -pass env:PASS | tar zxf - --directory backup_restore
```

# 创建备份外壳脚本

最后一步是真正创建一个`shell-script`，然后在一个`cronjob`中执行。在研究和测试之后，创建了这个脚本:

**重要的是知道你是否阅读/使用这个文件:**

1.  将`YOUR_BACKUP_STORAGE_PATH`替换为您存储备份的路径。这个文件夹**必须**有两个子目录:`backups_encrypted`和`backups`
2.  `YOUR_BUCKET_HERE`应更换为您想要使用的铲斗。用`gsutil ls`找
3.  `PATH_TO_GOOGLE_SDK_FOLDER`是您安装 Google Cloud SDK 的文件夹

这个脚本现在可以在`cronjob`中使用:

```
30 1 * * 6 export PASS=/root/.pass; /bin/sh /PATH_TO_SCRIPT/SCRIPTNAME.sh
```

这个`cronjob`每周六 1:30 执行。使用 [crontab.guru](https://crontab.guru/) 进行`crontab`创作

**非常重要:**您必须在执行脚本之前导出`PASS`变量，因为`cronjob`不是在您作为用户所在的 bash 中执行的，并且之前导出的`PASS`变量不存在

**也非常重要:**记住在集群中的每个 worker 节点上重新创建`cronjob`(如果你使用 Docker 集群的话)。**否则，数据将会丢失**。

# 结束语

在这篇简单的操作指南中，我解释了如何使用 Google Cloud SDK 在我的 Google Bucket 中保存一个加密的 zip 文件。

我有另一个教程，解释如何上传文件到亚马逊。您可以从本教程复制加密部分，并将其与上传到 S3 的文件结合起来:

[](https://aws.plainenglish.io/how-to-backup-your-files-to-aws-s3-b09f9378c082) [## 如何将您的文件备份到 AWS S3

### 一个快速的可视化增强教程，涵盖了将您的重要文件保存到 AWS S3 所需的每个步骤。

aws .平原英语. io](https://aws.plainenglish.io/how-to-backup-your-files-to-aws-s3-b09f9378c082) 

我希望这篇文章对您有所帮助，并且能够在云中保存您宝贵的备份。我很想听听你的想法，如果你有任何问题，请写在下面。*👇👇👇*

```
**Want to Connect With the Author?**Here's my [GitHub](https://github.com/paulknulst) handle and [LinkedId](https://linkedin.com/in/paulknulst) profile.
```

*本文最初发表于我的博客*[*https://www . paulsblog . dev/encrypt-database-backup-and-save-on-Google-cloud-platform/*](https://www.paulsblog.dev/encrypt-database-backup-and-save-on-google-cloud-platform/)