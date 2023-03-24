# 我如何使用 pure Go 构建一个版本控制系统(VCS)🚀

> 原文：<https://levelup.gitconnected.com/how-was-i-build-a-version-control-system-vcs-using-pure-go-83ec8ec5d4f4>

> 与软件创建相关的每一个工件都应该在版本控制之下。[1]
> 
> VCS 是一种跟踪文件随时间的修订(版本)的系统。[2]

# 演示

[https://asciinema.org/a/487303](https://asciinema.org/a/487303)

# **源代码**

[https://github.com/Abdulsametileri/vX](https://github.com/Abdulsametileri/vX)

# **动机**

当我阅读一本漂亮的书[3]来更好地理解[事件驱动系统](https://en.wikipedia.org/wiki/Event-driven_architecture)和[事件源](https://microservices.io/patterns/data/event-sourcing.html)的想法时，我看到了一个很好的例子

打个比方，我想象你正在构建一个像 SVN 或 Git 一样的版本控制系统。当用户第一次提交文件时，系统会将整个文件保存到磁盘。随后的提交反映了对该文件的更改，可能只保存增量，也就是说，只保存添加、更改或删除的行。然后，当用户签出某个版本时，系统打开版本 0 文件并应用所有后续增量，以便导出用户要求的版本。”[3]

我只是想用这个策略实现一个 VCS 系统。于是我开始了一个叫做 [**vX**](https://github.com/Abdulsametileri/vX) 的实验性爱好项目。所有这些只是我三天的努力。😅

# 体系结构

首先，所有相关文件都在`.vx`文件夹内。*(Go 工具会忽略任何名称以“_”或“.”开头的目录或文件)*

```
.vx
├── checkout
│ ├── v1
│ └── v2
├── commit
│ ├── v1
│ └── v2
├── staging-area.txt
└── status.txt
```

`v1, v2, .., vN` 是提交版本。我将在这些文件夹的下一节中详细介绍。

`checkout`是一个文件夹，包含了所有文件合并指定提交版本的结果。例如，`checkout/v2`包括`commit/v1` + `commit/v2`的组合。

创建一个签出目录是合理的，因为“*在 UNIX 世界中，一个好的实践是将应用程序的每个版本部署到一个新的目录中，并有一个指向当前版本的符号链接[1]”。目前，我还没有实现这个行为，但是它已经准备好了。*

`staging area`是将成为下一次提交的一部分的文件。在这种情况下，它只是一个带有**仅追加**模式的基本文本文件。因为我们在创作之后会做一些更新。例如，这个文件的一部分内容被格式化为`file path | file modification time | File Status`

```
"testdata/status.txt|2022-04-14 05:42:15|Created",
"testdata/z.go|2022-04-14 05:11:04|Created",
"README.md|2022-04-14 05:42:11|Created",
"testdata/a1.txt|2022-04-13 06:58:03|Created",
"README.md|2022-04-14 05:49:09|Updated",
```

例如，`README.md`一种在提交操作前添加两次，修改时间和状态不同的文件。所以这个文件的最新状态是`2022-04-14 05:49:09`的`Updated`。这非常类似于*事件采购的理念；也就是说，将数据库的更改表示为不可变的日志。[4].*

`status`是一个持续跟踪所有文件的文本文件。成功提交后，我清除了`staging area`的内容。所以我需要将文件持久地保存在版本控制系统下。

## 项目结构

I [遵循为任何基于](https://github.com/spf13/cobra/blob/master/user_guide.md) [Cobra 的应用](https://github.com/spf13/cobra)推荐的项目结构。

我用的是`testdata`目录。*(Go 工具会忽略任何名为* `*testdata*` *的目录，编译应用程序时会忽略这些脚本。)*

## 不支持的操作

目前，我不知道如何检测删除的文件，所以我只是跟踪创建和修改的文件。此状态基于文件系统提供的文件修改时间。

目前，为了提供签出功能，实现仅存储更改并在需要时合并它们是一项非常困难的工作，所以我将这项任务推迟到另一个版本。经过一番研究，我找到了适合这份工作的 rsync。因此，在每次提交操作时，我都将文件作为一个整体保存在临时区域。

# 命令

`init`:创建目录和文件。

`status`:读取暂存区文本文件并以适当的结构解析它们，并使用 [tablewriter](https://github.com/olekukonko/tablewriter) 显示结果。如果你仔细看，我用`io.Writer`接口创建了函数。在单元测试中，我很容易通过`bytes.Buffer`并断言。我推荐阅读这篇关于围棋界面的伟大文章。

`history`:显示所有提交。为了实现这个功能，我在每个提交目录中都保存了一个`metadata.txt`文件。在这个目录中，我存储提交消息和用`|`分隔的时间。

```
.
├── v1
│   ├── ..
│   ├── metadata.txt
└── v2
├── ...
└── metadata.txt
```

`add`:将指定的文件和目录添加到`status.txt`和`staging-area.txt`中。如前所述，为了显示一些文件的更新状态，我保存了文件`status.txt`的最新状态，所以我每次都截断并写入新数据。`staging-area.txt`是只追加数据，所以不需要做任何操作，只需追加新数据。重复数据没问题。成功提交后，我计算最新状态。

`commit`:读取`staging-area.txt`文件，用特定提交目录(v1，v2)复制，操作完成后截断`staging-area.txt`。

例如，让我们假设在 v1 提交中，用户添加了`README.md` `testdata/` ，而在 v2 提交中，用户添加了`Makefile`。因此，提交文件夹将如下所示

```
├── commit
│   ├── v1
│   │   ├── README.md
│   │   ├── metadata.txt
│   │   └── testdata
│   │       └── example
│   │           ├── a1.txt
│   │           ├── a2.txt
│   │           ├── example.go
│   │           ├── src
│   │           │   └── hello.js
│   │           └── z.go
│   └── v2
│       ├── Makefile
│       └── metadata.txt
```

`checkout` : rsync 从提交/到具有特定提交 id 的签出/目录。rsync 也为我们合并了两个相同的文件。

```
├── checkout
│   ├── v1
│   │   ├── README.md
│   │   └── testdata
│   │       └── example
│   │           ├── a1.txt
│   │           ├── a2.txt
│   │           ├── example.go
│   │           ├── src
│   │           │   └── hello.js
│   │           └── z.go
```

# 源代码

[https://github.com/Abdulsametileri/vX](https://github.com/Abdulsametileri/vX)

# 参考

[1]Andrew Glover，Paul Duvall 和 Steve Matyas 的《持续集成:提高软件质量和降低风险》

[2]谷歌的软件工程从编程中学到的经验

[3]Ben Stopford 的《设计事件驱动系统》

[4]Martin Kleppmann 的《理解流处理》