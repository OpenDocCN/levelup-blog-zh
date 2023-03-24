# 这是必须的，亲爱的华生

> 原文：<https://levelup.gitconnected.com/its-sed-ementary-dear-watson-c6a0a95959e7>

![](img/84dd7c080fbd5a4f30ce85bce5200e86.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[clment Falize](https://unsplash.com/@centelm?utm_source=medium&utm_medium=referral)拍摄

## echo $title | sed -e "s/Sed-/El "

## 这个 **sed** 程序员在说什么？你如何使用它？

inux 是一个很棒的工具。如果你以前从未在里面工作过，为什么不去看看呢？你可以不用双引导或者用 Docker 设置一个虚拟机器(它很像一个虚拟机，但是不同)就能做到。我写了一篇关于它的教程，你可以在这里阅读。

如果你以前使用过 Linux，并且已经找到了命令行的方法，你可能听说过一个叫做`sed`的小工具。`sed`代表**S**stream**Ed**itor，对于输入流的文本转换非常有用。

让我们探索以下场景:

> 您的供应商刚刚向您发送了一个压缩的资产文件，其中包含一个引用压缩内容和引用文件的文本文件(因此您可以在您的站点内部重新构建资产)。如果文件是用 Windows 发送的，但是您的构建脚本只运行 Linux 文件，则可能会出现“\n\r”回车符问题。你会如何解决这个问题？

另一个例子:

> 您注意到将要发送给客户的 CSV 文件插入了错误的时间。我们正在讨论，有 400 多张纸都写着错误的日期。这将打乱客户的计算。你怎么能把它们都更新并重写更新后的正确日期呢？

我们该怎么做？

> SED

![](img/69e58310e56da2102818f74a14fdafeb.png)

由[韦斯利·廷吉](https://unsplash.com/@wesleyphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们拿上面的例子来分解一下:

## 拆下车窗支架-返回

要删除 Windows 回车，只需构建下面的`sed`命令:

`$ sed "s?\r$?\n?" <<< /path/to/file.txt`

或者您可以将 shell 回显到 stdout 中:

`$ cat /path/to/file.txt | sed "s?\r$?\n?"`

这个命令就是命令:`**s**ubstitute [?:delimiter] [\r$: regex] [?] [\n: replacement] [?]`。分隔符可以是跟在`s`后面的任何字符。可以使用的好字符是通常不出现在文件名或文件中的字符。一些例子是:

*   !
*   @
*   #
*   $
*   |
*   _
*   ‘’(是的，甚至空格)

明智地选择你的武器。

## 更新多个 CSV 文件

为了解决需要更新的多个 CSV 文件的问题，我们可以做得更好一些，这样我们就可以正确地格式化。要更新多个文件，可以将它们作为空格分隔的列表附加到命令中。

`$ date=`$(date -R)` sed "s|[0-9]{4}/[0-9]{2}/[0-9]{2}|${date}|g" file1.csv file2.csv ...`

首先，我们设置`date`变量以 RFC 5322 格式输出日期和时间。然后我们运行`sed`命令，替换所有匹配“0000/00/00”的字符串，并用我们之前设置的`date`变量替换它们。最后，我们对在脚本定义后以空格分隔的方式插入的所有文件运行此操作。

就这么简单！

# 资源

[https://linux.die.net/man/1/sed](https://linux.die.net/man/1/sed)
[https://pubs . open group . org/online pubs/009695399/utilities/sed . html](https://pubs.opengroup.org/onlinepubs/009695399/utilities/sed.html)
[https://UNIX . stack exchange . com/questions/13711/differences-between-sed-on-MAC-OS x-and-other-standard-sed](https://unix.stackexchange.com/questions/13711/differences-between-sed-on-mac-osx-and-other-standard-sed)

![](img/72a495b48064de5009d1fe20865d85f3.png)

由[目标](https://unsplash.com/@arget?utm_source=medium&utm_medium=referral)在[未飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

你还在等什么？出去吧。开始编写一些很酷的项目，扩展你的知识面。`Sed`是继今天之后你工具箱中的又一个工具，这是一件值得骄傲的事情。

希望你喜欢这本书。我以前曾不时地使用过`sed`，但它是一个我会继续使用的工具，因为它可以在如此多的*nix 系统上使用。也许在接下来的一两个项目中，你会学到你在这里学到的东西。不管怎样，希望你玩得开心！

祝一切顺利，保持安全——斯潘塞