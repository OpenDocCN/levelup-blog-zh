# bash 命令重定向、链接和嵌套指南

> 原文：<https://levelup.gitconnected.com/a-guide-to-bash-commands-redirecting-chaining-and-nesting-f0c752c62eca>

![](img/58dd270fefad81040aae3fc13eb4d41f.png)

一旦你对如何使用 shell 命令有了一些基本的实践知识，你就开始倾向于在执行任务时节省一些时间。无论是在终端中还是使用像 [jenkins](https://jenkins.io/) 这样的自动化工具。在这篇文章中(或者这里的[视频版本](https://youtu.be/UXM8KNTC3aY))，我们将讨论一些信号，也就是 ***开关*** ，它们将帮助你做到这一点。

# 用&&和||链接命令

有时，您需要使用一行执行多个命令，而不是等待每个命令完成后再键入下一个命令。在这种情况下，你需要 **& &** 开关。它执行由该开关链接的所有命令:

```
$ sudo apt update && sudo apt upgrade && sudo apt install tree
```

您可能还需要知道链接的命令将按照编写的顺序工作。除非其中一个命令返回错误，否则它不会停止。

使用 **||** 有点与此相反。只有当第一个命令失败时，它才执行第二个命令。

# 使用>、>>和<

Redirecting is another useful tool for channeling a certain command output into a file. For instance, you may know the following command:

```
$ cp test.txt test2.txt
```

The command above copies a file test.txt to another file test2.txt. You can perform this command using one of our new tools:

```
$ cat test.txt > test2.txt
```

If you know the basics of shell commands, you probably know how *cat* 进行重定向有效。然而，>符号不是将文件的输出打印到屏幕上，而是将输出重定向到一个新文件。如果文件已经创建，并且其中有一些您不想覆盖的数据，那么您可以使用以下命令追加数据:

```
$ cat test3.txt >> test2.txt
```

所以基本上 **>** 的意思是创建和写入，而 **> >** 的意思只是追加。太好了！现在只剩下一个与我们之前讨论的相反的东西，那就是输入开关(<)。考虑以下 git 命令:

```
$ git commit -m 'test commit'
```

现在，让我们根据目前所学的内容再练习一会儿命令:

```
$ echo "test commit" > commit.txt && git commit < commit.txt
```

第一个命令只是创建一个名为 commit.txt 的新文件，并在其中写入“test commit”。第二个命令执行 git 命令 git commit，该命令期待一条消息，我们可以通过刚刚创建的文件传递这条消息。

> 请注意，重定向比链接具有更高的优先级。因此，您可以这样阅读上面的命令:

```
$ ( echo "test commit" > commit.txt ) && ( git commit < commit.txt )
```

# 管道带有|

使用管道可能对自动化非常有帮助。例如，很多时候当你在 Debian/Ubuntu 上安装一个包，在安装过程中需要你用 y/n 字母确认。所以你可以像下面这样传递一个强制的“y”字母:

```
$ echo "y" | sudo apt install tree
```

在这种情况下，当终端希望用户输入任何内容时，它会自动将通过管道 **|** 传递的内容分配给终端，然后继续执行命令。

在执行第二个命令时，它不一定总是 *stdin* 或用户输入。您可以将第一个命令的输出作为第二个命令中的参数传递，如下所示:

```
$ ls / | grep 'index.php'
```

在这里，你传递一个根目录下的文件和目录列表，然后在其中搜索一个名为 index.php*的文件。*

# 使用$(command)和` command `的嵌套命令

您可以在命令中嵌套命令，以便命令的输出可以是外部命令中的参数。

```
$ ls | cat $(grep -E ‘*.txt’ $1)
```

上面的命令将把所有的内容打印到屏幕上。txt 文件存在于当前目录中。如果当前目录包含 test1.txt 和 test2.txt，它相当于下面的命令:

```
$ cat test1.txt test2.txt
```

所有前面的例子都是非常基础的，但是如果您每天都使用 bash 命令，特别是如果您执行许多 DevOps 任务，您会发现使用这些工具的非常有趣的方法。