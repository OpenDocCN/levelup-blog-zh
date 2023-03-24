# 在命令或脚本前添加时间戳

> 原文：<https://levelup.gitconnected.com/prefixing-your-commands-or-scripts-with-a-timestamp-1eb408e92a1c>

![](img/4d82ab28818f8c0830b96511411f5a36.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当您运行一个长时间运行的命令或脚本时，让它的所有输出以时间戳为前缀可能会有所帮助。例如，我们想改变这一点:

```
foo
bar
baz
```

对此:

```
[2020-12-13 12:20:38] foo
[2020-12-13 12:21:32] bar
[2020-12-13 12:22:20] baz
```

这是你可以做到的。

# 管道至 Awk

关键是将输出传输到另一个工具，该工具从标准输入中读取所有内容，并打印相同的内容，但添加了前缀。

我们用什么工具？Awk 是一个完美的候选，因为它几乎安装在任何地方。Awk 是一个处理文本流的工具。

# 单个命令中的用法

下面是一个包含单个命令的示例:

```
command | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
```

但是，请注意，上面只是将时间戳前缀添加到写入标准输出的文本中。如果您希望写入标准错误的文本也带有时间戳前缀，那么请确保将标准错误重定向到标准输出，如下所示:

```
command 2>&1 | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
```

神奇的部分是`2>&1`。这意味着:将文件描述符 2(标准错误)重定向到文件描述符 1(标准输出)。

# Bash 脚本中的用法

Bash 脚本通常调用多个命令。您不希望手动将每个命令都传送到 awk:脚本会很快转向`awk`。🙃

```
*#!/bin/bash*
set -e
set -o pipefail*# Yeah, no...*
apt-get update 2>&1 | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
apt-get install -y foo 2>&1 | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
systemctl restart foo.service 2>&1 | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
```

我们能做得更好吗？是的:您可以告诉 Bash，它自己的所有输出(包括 Bash 运行的命令的所有输出)都应该被重定向到另一个命令:

```
exec **>** **>(**awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'**)** 2>&1
```

这是什么诡异的魔法？！？！

*   一个赤裸裸的`exec`语句，没有给出进一步的命令，意味着:我们不想实际执行任何东西，我们只想操纵当前 Bash 进程中的一些文件描述符(管道)。
*   `>`表示:将标准输出(现在包括标准错误)重定向到给定的文件。
*   `>(some command)`是一个[过程的替代](https://www.linuxjournal.com/content/shell-process-redirection)。这意味着:创建一个新管道，然后使用连接到该管道的标准输入运行`some command`，然后向该管道返回一个文件名。
*   所以`> >(some command)`的意思是:将标准输出重定向到由流程替换创建的管道。
*   `2>&1`的意思是:在当前的 Bash 进程中，将标准错误重定向到标准输出。

由进程替换创建的管道是临时的，并且在该行结束时立即关闭。在这里你可以看到它的作用:

```
$ echo The named pipe filename is >(true)
The named pipe filename is /dev/fd/63
```

但是，如果您尝试在此后立即访问它，您会发现它已经消失了:

```
$ ls /dev/fd/63
ls: /dev/fd/63: Bad file descriptor
```

因此，将所有这些放在一起，上面这个尴尬的脚本就变成了:

```
*#!/bin/bash*
set -eexec **>** **>(**awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'**)** 2>&1apt-get update
apt-get install -y foo
systemctl restart foo.service
```

# 警告:这是 Bash，不是 sh

`sh`不是迎头痛击。`sh`是一个历史比 Bash 还老的 shell。尽管`/bin/sh`可能指向与 Bash 相同的可执行文件，但是当 Bash 作为`sh`运行时，它将以一种“遗留模式”执行，并禁用一些特性。因此，如果您的 shell 脚本使用了这篇博客中描述的技巧，请确保您的 shebang 行是`#!/bin/bash`而不是`#!/bin/sh`。

# 警告:块缓冲

如果命令的输出被重定向到管道，命令的行为会有一点不同。

通常情况下，命令使用*行缓冲*，这意味着每当它们打印完一个完整的行，该行将被写入终端。

但是当输出被重定向到不是终端的东西时，那么命令将使用*块缓冲*，这意味着它们实际上并没有写出它们想要打印的内容；直到达到阈值，或者直到命令退出。这个阈值大约为 16 KB。您可以在这里看到块缓冲的作用:

```
python -c 'import time; print("x" * (1024 * 15)); time.sleep(10)' | cat
```

这个 Python 命令将 15 KB 的数据打印到标准输出，然后休眠 10 秒钟。输出通过管道传输到 cat。请注意，在 10 秒钟的睡眠期间，不会打印任何内容。只有在 10 秒钟后，它才会打印一些东西。

如果您将`15`增加到`16`，那么您将立即看到输出。

如果这种行为对您来说有问题，那么有各种方法来强制行缓冲:

# Linux，FreeBSD

在 Linux 和 FreeBSD 上，可以使用 [stdbuf](https://linux.die.net/man/1/stdbuf) 。将它作为任何要强制行缓冲的命令的前缀:

```
stdbuf -oL python -c 'import time; print("x" * (1024 * 15)); time.sleep(10)' | cat
```

# 马科斯

在 macOS 上，可以从 Homebrew 安装 stdbuf。这是《古兰经》的一部分。

```
brew install coreutils
/usr/local/opt/coreutils/libexec/gnubin/stdbuf -oL python -c 'import time; print("x" * (1024 * 15)); time.sleep(10)' | cat
```

# 计算机编程语言

对于 Python 脚本，您可以设置环境变量`PYTHONUNBUFFERED=1`，它告诉 Python 完全禁用任何类型的输出缓冲。

# Awk 替代方案的注意事项

Awk 有多种实现，具有不同的特性。在 Linux 上，您通常会使用 GNU 实现。在 macOS 上，您将使用 BSD 实现。如果您打算编写运行在多个操作系统上的 shell 脚本，那么一定要在您打算支持的所有平台上进行测试。

事实上，macOS 上的 Awk 实现并不支持`strftime`功能！

```
$ echo hi | awk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
/usr/bin/awk: calling undefined function strftime
 input record number 1, file
 source line number 1
```

您可以使用 Perl 作为替代。Perl 也被广泛安装。

```
$ echo hi | perl -pe 'use POSIX strftime; print strftime "[%Y-%m-%d %H:%M:%S%z] ", localtime; STDOUT->flush()'
[2020-04-27 15:13:33+0200] hi
```

或者你可以明确地安装 GNU 实现并使用它。GNU 实现也被称为`gawk`:

```
$ brew install gawk
$ echo hi | gawk '{ print strftime("[%Y-%m-%d %H:%M:%S]"), $0 }'
[2020-04-27 15:15:51] hi
```

# 关于 Awk 的更多信息

Awk 惊人地强大，却又简洁！这里有一个 [Awk 教程](http://www.grymoire.com/Unix/Awk.html)。更多深入的信息，请查看 [GNU Awk 用户指南](https://www.gnu.org/software/gawk/manual/gawk.html)。

祝你好运，不要害怕被`awk`病房。🙃

*原载于 2020 年 4 月 26 日 https://www.joyfulbikeshedding.com**[*。*](https://www.joyfulbikeshedding.com/blog/2020-04-26-prefixing-your-commands-or-scripts-with-a-timestamp.html)*