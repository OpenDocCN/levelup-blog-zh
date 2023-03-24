# 如何用 Bash 脚本替换文本

> 原文：<https://levelup.gitconnected.com/bash-script-to-replace-text-904f1ba05bc>

## 替换行和文件中的字符串和特殊字符

![](img/f7829573f2a3ff0f1c4067df3fc46118.png)

Gabriel Heinzer 在 [Unsplash](https://unsplash.com/s/photos/commandline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我将提供一个解决方案，并提供一些如何使用它的附加环境。我的希望是，这将为初始情况提供一个简洁而直接的解决方案，以及一些异常值。

现在，让我们直接进入脚本。

# 基本用例

假设您有一个如下所示的`.txt`文件。

```
hello there
how is life?
today is over
don't be sad
tomorrow is a new day
```

想象一下，你正试图用*明天将开始*来定位并替换*今天已经结束*这句话。你可以用下面的代码来实现。

```
#!/usr/bin/env bashYOUR_FILE='testing.txt'
TEXT="today is over"sed -i "" "s/$TEXT/tomorrow will begin/" $YOUR_FILE
```

我们来分析一下。

*   `**$YOUR_FILE**`:你要读的行的文件。
*   `**$TEXT**`:您试图替换的文本块。
*   `**-i**`:基本上意味着它会就地改变文件。
*   `**“”**`**:`-i`后的**双引号是 ***只针对 Mac*** 。在 Mac 上,`-i`选项需要一个用于备份的扩展。如果不想备份，可以只使用空引号。
*   `**s**` **:** 替代命令。
*   `**$line**`:要更换的线。
*   `**tomorrow will begin**`:用于替换`$TEXT`。

上述命令将找到所有出现`$TEXT`的行。所以如果你的`.txt`文件看起来像这样…

```
hello there
today is over
how is life?
today is over
don't be sad
today is over
tomorrow is a new day
```

…运行上述命令后，它将看起来像这样…

```
hello there
**tomorrow will begin**
how is life?
**tomorrow will begin**
don't be sad
**tomorrow will begin**
tomorrow is a new day
```

现在，如果我们在*相同的*行上有多个`today is over`实例会怎样？

# 全球使用案例

上面的命令有效，但是每行只对*有效一次。这是因为我们没有在命令中提供一个**全局**标签。如果您的`.txt`文件看起来像这样…*

```
hello there
today is over today is over today is over
how is life?
don't be sad
tomorrow is a new day
```

…上面的命令将使它像这样工作…

```
hello there
**tomorrow will begin** today is over today is over
how is life?
don't be sad
tomorrow is a new day
```

如果想要获得所有实例，可以在 sed 命令的末尾追加`/g`。像这样:

```
sed -i "" "s/$TEXT/tomorrow will begin**/g**" $YOUR_FILE
```

这将确保**的所有**实例都被替换，不管一行中有多少个。噪音。

但是如果我们只想得到第一个实例呢？还是只有前两个例子？

我们现在知道了如何获得所有的`$TEXT`实例，但是如果`$TEXT`不是如此简单呢？如果我们的`.txt`文件有这样的内容会怎么样…

```
hello there
how is life?
today is over
don't be sad! Just say, "life is good!"
tomorrow is a new day
```

# 特殊字符大小写

我们这里有一个“特殊字符”的实例。这些字符在 Bash 中没有字面意义。相反，他们自己执行一个特殊的指令。

你可以在这里找到特殊字符及其含义的列表[。](https://www.oreilly.com/library/view/learning-the-bash/1565923472/ch01s09.html)

在第四行，你可以看到我们有一些特殊的字符，如撇号(或单引号)，感叹号，逗号， ***和*** 一些文本在 *双引号*里面有一个感叹号**。**

为了处理特殊字符，使它们不被视为操作符，您需要使用所谓的反斜杠转义。

参见下面的代码。

```
#!/usr/bin/env bashYOUR_FILE='testing.txt'
TEXT="don't be sad! Just say, "life is good!""sed -i "" "s/$TEXT/tomorrow will begin/" $YOUR_FILE
```

如果您要运行这个脚本，您不会找到第四行并用`tomorrow will begin`替换它，而是会给您一个错误:

```
./test.sh: line 4: is: command not found
sed: first RE may not be empty
```

为了让它工作，你需要用反斜杠“转义”双引号。见下文。

```
LINE="don't be sad! Just say, \"life is good!\""
```

如果你再次重新运行你的脚本，它应该工作！现在，你可能会问自己，其他角色呢？为什么他们不需要被逃脱？

在当前的用例中，它们不需要转义，因为整行都用引号括起来了。引用，是转义特殊字符的另一种方式。但是，正如你在上面看到的，我们需要使用反斜杠转义，因为我们在双引号里面有双引号。

在 Bash 中如何替换文本？我错过了什么？请在评论中告诉我！

[***升级您的免费媒体会员资格***](https://matt-croak.medium.com/membership) *并接收来自各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

你也可以通过电子邮件 *订阅，每当我发布新内容时，你都会收到通知！*

# 参考

 [## bash guide/特殊字符

### 一些字符被 Bash 评估为具有非字面意义。相反，这些角色执行一个特殊的…

mywiki.wooledge.org](https://mywiki.wooledge.org/BashGuide/SpecialCharacters#:~:text=Special%20characters,or%20%22meta%2Dcharacters%22.) [](https://www.oreilly.com/library/view/learning-the-bash/1565923472/ch01s09.html) [## 学习 bash Shell，第二版

### 表 1.6 仅给出了 shell 命令行中所有特殊字符的含义。其他角色有特殊的…

www.oreilly.com](https://www.oreilly.com/library/view/learning-the-bash/1565923472/ch01s09.html) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)