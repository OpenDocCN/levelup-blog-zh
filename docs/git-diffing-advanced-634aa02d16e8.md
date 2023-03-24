# Git 差分高级

> 原文：<https://levelup.gitconnected.com/git-diffing-advanced-634aa02d16e8>

![](img/5f6c4c4ffb9710e17646ecfd3f1814fd.png)

迪特尔马·贝克尔在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

版本控制的主要目的是使您能够管理对相同文件所做的更改。Git 提供了一个命令 **diff** ，它比较两个输入数据集并输出它们之间的变化。`git diff`是一个多用途的 Git 命令，当它被执行时，在 Git 数据源上运行一个 diff 函数。这些数据源可以是提交、分支、文件等等。

`git diff`将显示自上次提交以来任何未提交的更改。差异将以补丁格式显示。修补格式意味着显示两个版本之间不同(添加或删除)的行。

# 阅读差异:输出

让我们比较两个项目:项目# 1 和项目#2。在大多数情况下，#1 和#2 可能是同一个文件，但也可能是提交、分支等。现在我们将使用文件比较来理解该命令。

```
diff --git a/example_diff.txt b/example_diff.txt
index 532ce54..0f42eb5 100644
--- a/example_diff.txt
+++ b/example_diff.txt
@@ -1 +1 @@
-examplediff
+example diff
```

## 比较输入

```
diff --git a/example_diff.txt b/example_diff.txt
```

这一行显示 diff 的输入源。我们可以看到`a/diff_test.txt`和`b/diff_test.txt`已经被传递给了 diff。

## [计]元数据

```
index 532ce54..0f42eb5 100644
```

这一行显示了一些内部 Git 元数据。你很可能不需要这些信息。这个输出中的数字对应于 Git 对象版本散列标识符。

## 变更标记

```
--- a/example_diff.txt
+++ b/example_diff.txt
```

这些线是为每个 diff 输入源分配符号的图例。在这种情况下，来自`a/diff_test.txt`的变化标有`---`，来自`b/diff_test.txt`的变化标有`+++`符号。

## 差异块

剩下的 diff 输出是 diff“块”的列表。差异仅显示文件中有更改的部分。在我们当前的例子中，我们只有一个块，因为我们正在处理一个简单的场景。组块有自己的粒度输出语义。

```
@@ -1 +1 @@
-examplediff
+example diff
```

第一行是块头。每个块都有一个包含在`@@`符号中的头。文件头的内容是对文件所做更改的总结。在我们简化的例子中，我们有-1 +1，意味着第一行已经改变。在更实际的 diff 中，您会看到一个标题，如下所示:

```
@@ -40,6 +62,15 @@
```

在这个标题示例中，从第 40 行开始提取了 6 行。此外，从第 62 行开始添加了 15 行。

diff 块的剩余内容显示最近的更改。每一个被修改的行前面都有一个`+`或`-`符号，表示修改来自哪个版本的 diff 输入。正如我们之前所讨论的，`-`表示从`a/example_diff.txt`开始的变化，`+`表示从`b/example_diff.txt`开始的变化。

# 有用的命令

`git diff --name-only`将给出被修改的文件名的快速摘要。

```
$ git diff --name onlyexample_diff.txt
```

`git diff --name-status`将快速总结已修改的文件名以及状态信息。

```
$ git diff --name-statusM       example_diff.txt
```

`git diff --word-diff`将在*字*粒度级别显示差异。单词被分成记号，其中空格被用作分隔符。
`[- -]`中的项目代表删除的项目，`{+ +}`中的项目代表增加的项目。

```
$ git diff --word-diffdiff --git a/example_diff.txt b/example_diff.txt
index 532ce54..0f42eb5 100644
--- a/example_diff.txt
+++ b/example_diff.txt
@@ -1 +1 @@
[-examplediff-]{+example diff+}
```

`git diff --ignore-all-space`比较时忽略所有空格。

```
$ git diff --ignore-all-spacediff --git a/example_diff.txt b/example_diff.txt
index 532ce54..7d13e83 100644
--- a/example_diff.txt
+++ b/example_diff.txt
@@ -1 +1,2 @@
 example diff
+new line
```

`git diff --ignore-space-at-eol`忽略行尾的空白变化。

```
$ git diff --ignore-space-at-eoldiff --git a/example_diff.txt b/example_diff.txt
index 532ce54..0f42eb5 100644
--- a/example_diff.txt
+++ b/example_diff.txt
@@ -1 +1 @@
-examplediff
+example diff
```

`git diff --ignore-blank-lines`忽略所有行都是空白的更改。

```
$ git diff --ignore-blank-linesdiff --git a/example_diff.txt b/example_diff.txt
index 532ce54..0f42eb5 100644
--- a/example_diff.txt
+++ b/example_diff.txt
@@ -1 +1 @@
-examplediff
+example diff
```

`git diff --[staged|cached]`根据本地存储库区分临时区域。暂存和缓存是同义词，可以互换使用。

```
$ git diff --stageddiff --git a/example_diff.txt b/example_diff.txt
new file mode 100644
index 0000000..532ce54
--- /dev/null
+++ b/example_diff.txt
@@ -0,0 +1 @@
+examplediff
```

`git diff <ident>`也可以指定一个版本进行比较。`<ident>`将是 SHA 提交、对它的引用或符号名。

```
$ git diff HEADdiff --git a/example_diff.txt b/example_diff.txt
new file mode 100644
index 0000000..532ce54
--- /dev/null
+++ b/example_diff.txt
@@ -0,0 +1 @@
+examplediff
```

`git diff <ident1> <ident2>`将显示用`<ident1>`和`<ident2>`指定的两次提交之间的差异。`<ident>`将是 SHA 提交、对它的引用或符号名。

`git diff <ident1> <ident2> -- <filename>`与先前相同，但将显示仅与`<filename>`相关的变更。

`git diff --raw`将以原始格式显示差异。这与`git st -s`类似，但会用不同的特定数据来修饰它。

```
$ git diff --raw:100644 100644 532ce54... 0000000... M  example_diff.txt
```

# 用于区分的 GUI 工具

在终端/命令行中运行`git diff`将显示补丁格式的变化。但是有时候使用视觉界面更方便。为此 git 提供了一个特殊的命令`git difftool`。通过调用这个命令，git 用适当的参数启动所需的工具。

您可以使用的一些最常用的比较工具(在撰写本文时)有:

*   KDiff3
*   P4Merge
*   无与伦比
*   千变万化
*   代码比较
*   Vimdiff
*   WinMerge
*   合并

要配置特定的工具，可以使用下面的
`git config --global diff.tool <tool>`
，其中`<tool>`代表工具的简单名称(例如 kdiff3、meld、…)

一个恼人的事情是可以抑制的提示信息，每个文件被区分。消息看起来是这样的:*“查看(1/3): 'file.txt '启动' kdiff3' [Y/n]: "* 。这可以通过在运行 difftool 时添加`--no-prompt`选项或将`difftool.prompt`配置值设为 false
`git config --global difftool.prompt false`来抑制。

有些情况下，用户会将 diff 工具安装在默认位置以外的目录中。假设您已经在 Windows 上的以下路径安装了 kdiff 3*C:/apps/kdiff 3。* Git 不知道如何使用安装在这里的应用程序，所以我们现在应该帮助 Git 并通过`git config --global difftool.kdiff3.path <path>`为 difftool 设置路径值