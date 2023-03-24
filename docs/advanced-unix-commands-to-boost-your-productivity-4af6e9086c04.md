# 提高生产力的高级 Unix 命令

> 原文：<https://levelup.gitconnected.com/advanced-unix-commands-to-boost-your-productivity-4af6e9086c04>

## 你学得越多，你就变得越好

我是硅谷的一名工程师，有超过 12 年的工作经验。我喜欢 Unix 命令。我在日常生活中一直使用 Unix 命令。忘记一些实用的 Unix 命令，确实帮助我解决了许多问题，甚至没有意识到这一点。

从开发人员到 QA 到系统管理员，每个人都应该很好地掌握 UNIX 命令。它肯定会让你的生活变得相当容易。

![](img/2b870c416c5458c3884fe479c336972b.png)

照片由 [z 于](https://unsplash.com/@yuzhang?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/screen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

因此，这里有几个高级 Unix 命令可以提高您的工作效率

# 视觉识别系统

它是“*虚拟编辑器*的简称。vi' 是我最喜欢的命令之一。我使用‘VI’有多种用途，从查看文件到编辑文件。我见过很多对 vi 不满意的人。以下是我听到的一些借口:

> 我会错误地更新文件
> 
> 这太复杂了，不能简单地改变

对于初学者来说这可能是真的，但是对 vi 有一些了解将确保你不会搞砸。

以下是我在第六季中使用的一些技巧

> 转到特定的 n 行

```
:n
```

> 走到最后一行

```
:$
```

> 将文件中所有出现的单词“genius”替换为“smart”

```
%s/genius/smart/g
```

> 打开 vi 并自动移动到文件“许可证”中第一次出现的单词“工作”

```
vi LICENSE +/work
```

正如我前面提到的，我有时使用‘VI’只是为了查看文件，因为 VI’不会阻塞终端，‘**cat’**会。这样的窍门有很多，你用得越多，就越舒服。

# **找到**

顾名思义，它主要用于查找文件或目录。想象一下，你登录到一个服务器，需要找到一个文件。如果您知道如何使用 find 命令，您将找到该文件(当然，如果它存在的话)。您也可以使用' **locate'** 来搜索文件，但是查找是在线的，而不是定位。与' **locate** '相比，' **find** '还有一堆其他的优点。更多信息请参考此[文件](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other)。

下面是“查找”命令的样子

```
find [where to start searching] [-options] [expression]
```

> 在当前目录中找到名为 LICENSE 的文件

```
find . name LICENSE *// replace . with folder to specify the directory to search*
```

> 在以 md 结尾的文件中搜索文本“数据”

```
find ./ -type f -name "*.md" -exec grep 'data'  {} \;
```

> 仅在当前目录和 1 个子目录级别中找到文件“许可证”

```
find . -maxdepth 2 -name LICENSE
```

您可以使用 find 来查找任何文件或目录。如果文件存在，只要您知道如何查找，find 就会找到它。

# awk

这是我最喜欢的命令之一。没有什么是‘awk’做不到的。它是一种脚本语言，被认为是 Linux 环境中最强大的命令。

下面是 awk 语法的样子

```
awk options 'selection _criteria' input-file
```

让我们来看看我在日常工作中使用的更高级的例子

> 对文件中的列进行汇总

```
awk -F'\t' '{sum=sum+$1} END {print sum}'
```

> 分组依据和总和

```
awk '{arr[$1]+=$2} END {for (i in arr) {print i,arr[i]}}'
```

> 过滤文件中的数据

```
awk '{ **if**($1 == "data" ) print $0}' LICENCE
```

awk 的可能性是无限的。掌握这一点将有助于你很容易地解决问题。你也可以向你的同龄人炫耀你的知识。

# 别名

如果有你经常使用的命令，你想创建一个快捷方式，'**别名'【T1]是你的朋友。假设您运行一个' **rm'** 命令，并且您不想犯错误，创建一个别名，并且在将来忘记语法。我刚刚检查了我的机器，我现在有 29 个别名。最常见的是*宋承宪进入不同的主机***

以下是如何使用'**别名'**

```
alias hi='echo "Hello, this is alias helping you"'hi // run hiHello, this is alias helping you
```

如果希望每次创建新终端时都运行别名，请将它们添加到。 **bash_profile** 文件。那是额外的小费。

# netstat

如果您的工作需要获取端口信息和其他网络相关信息，这是适合您的命令。这个命令将帮助您获得有关网络连接、路由表、接口统计、伪装连接、多播成员的信息

> 列出所有端口

```
netstat -aActive Internet connections (including servers)
Proto Recv-Q Send-Q  Local Address Foreign Address (state)tcp4 0 0  11.111.111.111.11111   ec2-2-222-222-22.https ESTABLISHED
...
```

> 显示所有端口的统计数据

```
netstat -stcp:0 packet sent
```

> 监听 IP:端口的服务

```
netstat -an | grep ‘LISTEN’
```

这些是我经常使用的 Unix 命令，在我的日常工作中帮助了我。还有很多很多。请在评论区告诉我你最喜欢和最常用的命令。

[](https://medium.com/swlh/how-to-excel-in-programming-19d3dad8a0e8) [## 提高编程水平的良好实践

### 这个问题我被问了很多次——“我是一个好的程序员吗？”我总是说“看情况”。编程是一种…

medium.com](https://medium.com/swlh/how-to-excel-in-programming-19d3dad8a0e8) 

感谢阅读这篇文章。