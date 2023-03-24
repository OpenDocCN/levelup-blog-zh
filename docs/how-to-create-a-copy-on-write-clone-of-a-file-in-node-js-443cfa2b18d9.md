# 如何在 Node.js 中创建文件的写时复制克隆

> 原文：<https://levelup.gitconnected.com/how-to-create-a-copy-on-write-clone-of-a-file-in-node-js-443cfa2b18d9>

在过去的 35 年中，Linux/Unix 文件系统语义的进步令人耳目一新。1984 年，我在运行 4.2BSD 的 Vax 上第一次使用了符号链接，这是对多年前就存在的硬链接的重大改进。前几天，我了解到 Mac OS X 和 Linux 都支持一种新的链接，即 reflink，它是某些文件系统的一种写时复制形式。

![](img/2c5eb8d5fad6db6414c3ac6384f2f424.png)

如果你和我一样不太清楚*写时复制*的意思，让我们先来探讨一下。我依稀记得 CoW 被用于操作系统内存管理系统——它与共享内存段一起使用。

> 写时复制(COW 或 CoW)是计算机编程中使用的一种资源管理技术，用于在可修改的资源上有效地实现“复制”或“拷贝”操作。如果资源被复制但没有被修改，则没有必要创建新的资源；资源可以在副本和原件之间共享。修改仍然必须创建一个拷贝，因此采用了这种技术:拷贝操作推迟到第一次写入时进行。通过以这种方式共享资源，可以显著减少未修改副本的资源消耗，同时给资源修改操作增加少量开销。来源:
> 
> [维基百科](https://en.wikipedia.org/wiki/Copy-on-write)

这意味着你有一个资源，比如共享内存段，或者文件系统中的一个文件。不是复制资源，而是制作一个副本，这可能非常快。副本被设置成使得对资源的任何修改都会导致被修改的部分成为副本，并且被修改的部分就是被复制的部分。

也许这种解释的尝试仍然模糊不清，所以让我们尝试一些明确的例子。

为了在 Node.js 中创建一个`reflink`克隆，使用带有特定选项的`fs.copyFile`函数。这方面的源代码在本文的底部。为了理解这是怎么回事，我们必须先看一些背景材料。

创建文件的`reflink`副本:

```
$ time cp --reflink wikidatawiki-stub-articles.xml w.xmlreal	0m0.008s
user	0m0.004s
sys	0m0.000s$ ls -l wikidatawiki-stub-articles.xml w.xml 
-rw-rw-r-- 1 david david 39478588202 Jul 21 22:00 wikidatawiki-stub-articles.xml
-rw-rw-r-- 1 david david 39478588202 Jul 25 22:00 w.xml
```

这个文件有 39 千兆字节。这是在一个 Linux 机器上，带有一个启用了 reflinks 选项的 XFS 文件系统。关于设置的信息[如何在 Ubuntu 上格式化带有 XFS 文件系统和引用链接支持的驱动器](https://techsparx.com/linux/disks/ubuntu-xfs.html)

在 Mac OS X 上，命令将改为`cp -c`来使用`clonefile`系统调用。

克隆这个 39 千兆字节的文件只需要几分之一秒的时间。

想想看，39gb 的数据在一瞬间被复制。通过多次复制这个文件，我知道一个 39gb 的正常文件副本需要 15 分钟。十五分钟对几分之一秒是一个巨大的加速。

因为编辑一个 39gb 的 XML 文件是不实际的，所以让我们用一个更小的文件来演示写时复制。

```
$ ls -l sample.text 
-rw-rw-r-- 1 david david 1393 Jul 25 22:08 sample.text
```

这是一个带有 Lorem Ipsum 文本的文件。

```
$ cp --reflink sample.text sample-dup.text
$ vi sample-dup.text $ ls -l sample*
-rw-rw-r-- 1 david david 1403 Jul 25 22:09 sample-dup.text
-rw-rw-r-- 1 david david 1393 Jul 25 22:08 sample.text$ diff -u sample.text sample-dup.text 
--- sample.text	2019-07-25 22:08:43.809680598 -0700
+++ sample-dup.text	2019-07-25 22:09:24.073194632 -0700
@@ -3,3 +3,5 @@

 Etiam tempor orci eu lobortis elementum nibh tellus molestie. Neque egestas congue quisque egestas. Egestas integer eget aliquet nibh praesent tristique. Vulputate mi sit amet mauris. Sodales neque sodales ut etiam sit. Dignissim suspendisse in est ante in. Volutpat commodo sed egestas egestas. Felis donec et odio pellentesque diam. Pharetra vel turpis nunc eget lorem dolor sed viverra. Porta nibh venenatis cras sed felis eget. Aliquam ultrices sagittis orci a. Dignissim diam quis enim lobortis. Aliquet porttitor lacus luctus accumsan. Dignissim convallis aenean et tortor at risus viverra adipiscing at.

+MODIFIED
+
```

我们创建了该文件的一个`reflink`副本，然后编辑该副本。我们看到这两个文件大小不同，并且`diff`显示了它们之间的差异。因此`sample-dup.text`现在是`sample.text`的副本。

如果这些文件被硬链接或符号链接，创建链接会很快，但是编辑文件会修改链接。

```
$ cp sample.text sample2.text
$ ln sample2.text sample2-link.text
$ ls -l sample2*
-rw-rw-r-- 2 david david 1393 Jul 25 22:50 sample2-link.text
-rw-rw-r-- 2 david david 1393 Jul 25 22:50 sample2.text
$ vi sample2-link.text 
$ ls -l sample2*
-rw-rw-r-- 2 david david 1402 Jul 25 22:50 sample2-link.text
-rw-rw-r-- 2 david david 1402 Jul 25 22:50 sample2.text
$ diff -u sample2*
```

为了演示显而易见的内容，我们制作了该文件的副本。然后我们做了一个硬链接到副本，并编辑硬链接的副本。当然，因为这就是硬链接的工作方式，所以对两个文件的修改都会显示出来。

# 这意味着什么？

我们演示的是，使用 reflinks 可以非常快速地创建文件的副本，并且消耗的磁盘空间可以忽略不计。克隆的副本继续消耗可以忽略的磁盘空间，直到您修改克隆的副本，此时它成为文件的常规副本。

也许这看起来很神秘，但是考虑一个可能的用例。

用这种能力来管理一个软件开发项目。代替源代码管理系统(Git、Mercurial、CVS 等),你将拥有并行目录。使用我们在 Linux 上使用的 GNU cp 程序，你可以非常快速地克隆一个完整的目录结构。项目团队可以使用一系列并行目录，每个目录都使用写时复制副本来管理源代码树。

```
$ git clone https://github.com/nodejs/node.git
Cloning into 'node'...
remote: Enumerating objects: 8, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 521860 (delta 1), reused 3 (delta 1), pack-reused 521852
Receiving objects: 100% (521860/521860), 462.85 MiB | 1013.00 KiB/s, done.
Resolving deltas: 100% (387209/387209), done.
Checking out files: 100% (31537/31537), done.$ du -sk node
913584	node$ find node -type f -print | wc -l
31561$ time cp --archive --reflink node node-dupreal	0m1.860s
user	0m0.317s
sys	0m1.306s
```

以 Node.js Git 库为例，913 兆的源代码，31000 多个文件，整个目录结构的`reflink`克隆需要 1.8 秒。在克隆中，我们可以编辑任何文件，原始文件不会被更改。

理论上，我们可以用它来处理源代码树的修改。当然，我不认为软件工程师会放弃像 Git 这样的配置管理系统。相反，这个特殊的例子可能不太好，但也许有其他更有说服力的用例。

例如，如果每次编辑文件时，文字处理或图像处理程序都创建了一个写入时复制的克隆，那会怎样？该程序可以有一个用户界面来浏览克隆，因此如果需要，您可以恢复到文件的早期版本。

# 操作系统支持

并非每个操作系统都有`reflinks` / `clonefile`功能。

在 Mac OS X 上，该功能需要 APFS 文件系统。

在 Linux 上，它需要 XFS、BTRFS 和一两个其他文件系统。

在 Windows 上——我不知道。

# 在 Node.js 中实现 reflinks

```
const fs = require("fs");
const process = require('process');fs.copyFile(
  process.argv[2],
  process.argv[3],
  fs.constants.COPYFILE_FICLONE,
  (err) => {
    if (err) {
      // TODO: handle error
      console.error(err);
    }
  }
);
```

在生产代码中，这当然应该在一个 async/await 函数中。关键是将`copyFile`与`fs.constants.COPYFILE_FICLONE`常量一起使用。

时机是:

```
$ time node reflink.js wikidatawiki-stub-articles.xml foo.xmlreal	0m0.047s
user	0m0.036s
sys	0m0.012s
```

或者……和`cp --reflink`要求的时间差不多。

*本文原载于* [*TechSparx*](https://techsparx.com/nodejs/howto/copy-on-write.html)