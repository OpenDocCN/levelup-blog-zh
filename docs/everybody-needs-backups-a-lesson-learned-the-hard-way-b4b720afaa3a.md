# 每个人都需要备份——这是一个惨痛的教训

> 原文：<https://levelup.gitconnected.com/everybody-needs-backups-a-lesson-learned-the-hard-way-b4b720afaa3a>

## 我的 Docker Swarm 中的一个大事件以及事件发生后我开发的解决方案。

![](img/2be63d63a7ffaed5ee035e88b986d8c8.png)

照片由来自 [Pexels](https://www.pexels.com/photo/man-showing-distress-3777572/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[安德里亚·皮亚卡迪奥](https://www.pexels.com/@olly?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

# 该事件

这是几天前我艰难地学到的一课。目前，我在一家软件公司做顾问。在我为不同公司远程工作期间，我把我所有投入的时间都记录在我的个人时间追踪软件中，这个软件非常好用。最棒的是我的时间跟踪软件在 docker 环境中的本地机器上运行！这太酷了！

在过去的几周里，我在 Docker Swarm 上工作，建立我个人的 Swarm 环境。一切都结束后，我决定将我的时间跟踪软件移到集群中。因为它已经是一个 docker 容器，我可以轻松地备份 MySQL 数据，并将所有内容放入我的 Swarm。它运行平稳。一切正常！

几天后，我决定在我的 swarm 中创建另一个 docker 容器。所以我写了`docker-compose.yml`并部署了它。我在和我的蜂群一起工作。增加了许多服务(Gitlab、邮件服务器、Portainer 等)。

然后有一天，当我完成工作并想记录我的工作时间时，时间跟踪软件却不可用了。

**于是我开始研究**:到底是怎么回事？为什么我无法访问该网站？我检查了蜂群。每个服务都正确部署并运行。之后，我查了日志…在软件中，我看到一条消息，说数据库不存在。但它应该在那里？？

我在我的 MySQL 容器中。然后我就看到了。**再也没有 DB 了！**这是怎么回事？和`docker stack ps timetracking`一起，我检查了每个集装箱。经过一番研究，我看到一个容器被杀，另一个被部署。通常这不是一个真正的问题。**但是后来…** 我发现它被部署到了我的集群中的另一个节点上。这很糟糕，因为我知道那个节点上没有卷。

我很高兴，因为我认为我可以很容易地改变它。我打开了`docker-compose.yml`并添加了一个约束，即服务应该一直部署在同一个节点上:

```
deploy:
      placement:
        constraints:
          - node.hostname == *****
```

**我知道这会导致在我的集群中的特定节点上进行部署**。现在数据库不会丢失，它应该使用旧卷！

**然后容器重启后，整个卷被销毁。它再也打不开了。**

*幸运的是，几天前当我从本地 docker env 迁移到 swarm 时，我创建了一个数据库备份。我很高兴我没有损失整整一个月的工作时间……*

# 备份脚本

这件事之后，我唯一想做的事情就是为我的 docker env 开发一个简单的备份功能。首先，我`exec`进入每个容器并保存数据库以确保万无一失。下一步真的很简单。我搜索了 docker 备份功能，发现了一些非常有趣的文章和教程，内容是用加密和.....

这太难了，因为我需要一个非常简单的解决方案。我只想将我的数据库从每个 docker 容器复制到一个安全的地方。我决定创建一个小脚本来完成这个任务。然后每天都会执行这个脚本。

经过几个小时的 bash 脚本研究，我开发了一个名为`full-db-backup.sh`的简单文件，稍后我会解释这个文件

一个非常简单的脚本来备份 MySQL 和 MariaDB 容器。

**第 1 行:**
添加 ***#！/bin/bash*** 作为脚本的第一行，告诉操作系统调用指定的 shell 来执行脚本中的命令。

**第 2 行:**
创建一个名为`containers`的变量，并在`$( )`内填入命令内容。该命令列出了所有正在运行的 docker 进程，然后将结果(通过`|`转发给`grep`，后者根据两种模式对它们进行过滤(`myql` 或`maria`)。这些结果将被转发给 awk，它只打印前面列出的行中的最后一项。因此，将有一个包含每个容器名称的数组。

**第 4–5 行:**
开始一个 for 循环，遍历每个容器名。

**第 6 行:**
用`.`拆分的容器名创建一个数组。通常 docker 容器名称是这样创建的:`service-name.replica.some-hash`

**第 8–9 行:**
开始一个 for 循环，遍历字符串数组的每一部分

**第 10–11 行:**
保存变量`simpleName`中的第一个值(服务名),然后中断循环

**第 12 行:**
为循环结束参数

**第 14 行:**
保存变量内的实际时间戳

**第 15 行:**
连接到名为`$container`(数组`$containers`的一部分)的 docker 容器，执行`myqsldump`保存每个数据库。作为密码，它使用一个 ENV 变量(`$MYSL_ROOT_PASSWORD`)，这个变量几乎用于我环境中的每个 MySQL/MariaDB docker 卷。在转储之后，文件被保存在/root/backups 中，使用从容器`simpleName`中创建的名称和实际的时间戳

**第 16 行:**为循环结束参数

# 执行备份

我测试了脚本，它像预期的那样工作。它每次都会创建一个包含整个数据库备份的新文件。

![](img/5c9b134afd1e6cbe7885d81bf65722b2.png)

照片由 [Greyson Joralemon](https://unsplash.com/@greysonjoralemon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/party?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

但是我不得不在间隔中执行脚本**，这样数据库中的更新也会被保存。**

我不想太复杂，所以我决定在每个 docker 群节点上只使用一个简单的 cronjob。这不是最好的东西，但它的工作。这就是我现在想要的。

我用`crontab`创建了一个新的 cronjob。使用下面的命令，我打开了机器上的 cronjob 列表。

```
$> crontab -e
```

我添加了一个 cron 作业，该作业将在每天凌晨 1 点执行，它将只执行我开发的脚本，该脚本在我的根文件夹中创建备份。

```
0 1 * * * /bin/sh /root/cronjobs/full-db-backup.sh
```

最后，我将脚本复制到集群中的每个节点，并向其中添加相同的 cronjob。

![](img/4d221d7c3c6bdf311727753ea3120fdf.png)

使用 Imgflip [Meme 生成器](https://imgflip.com/memegenerator)创建

# 结束语

我知道这不是最好的解决方案，但目前这种快速的方法已经足够了。现在我可以开始学习一些关于 ANSIBLE 的知识，以及如何在剧本中自动化这个脚本。此外，我会创建一些将我的转储上传到亚马逊。并在上传前加密。但是现在我有时间做这个**因为我很安全**！

尽管如此，我希望这篇文章对您有所帮助！如果你也有一个有趣的备份策略可以分享，欢迎在这里发表评论。**快乐备份！**

# 这篇博客到此结束。我很想听听你的想法和想法🤗请把它们记在下面👇👇👇

# ✍️作者

# 保罗·克努特

👨🏻‍💻🤓🏋️‍🏸🎾🚀

丈夫，两个孩子的父亲，极客，终身学习者，技术爱好者和软件工程师

与此稍有不同的版本最初发表在[https://www . paulsblog . dev/every one-needs-backups-a-lesson-learned-the-hard-way/](https://www.paulsblog.dev/everybody-needs-backups-a-lesson-learned-the-hard-way/)

# 打招呼🙌开启:

[推特](https://www.twitter.com/paulknulst)， [LinkedIn](https://www.linkedin.com/in/paulknulst/) ， [GitHub](https://github.com/paulknulst) ，[个人博客](https://blog.knulst.de/)