# Git:交互式登台

> 原文：<https://levelup.gitconnected.com/git-interactive-staging-b86cdef7a275>

![](img/584d24b4a50b4e9fcfb2fb1425eed17f.png)

[美元吉尔](https://unsplash.com/@dollargill?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

有时简单地用`git add .`或`git commit -am`相加是不够的。您可能希望将更改划分到几个提交中，或者您还没有准备好添加所有内容。用户可以使用一种不同的暂存(添加)和提交功能:交互式暂存。此命令可以帮助您设计提交，使其仅包含文件的某些组合和/或部分。

`--interactive`(或`-i`)是`--patch`的老大哥。[补丁](https://medium.com/@milan.brankovic/git-partial-staging-86eb01ea6b7d)只能让你决定文件中的个体。`--interactive`进入交互模式，更强大一点。当您进入交互模式时，您将看到一个命令行界面，其中列出了各种文件和可用的分段函数，并为其分配了字母或数字。然后，您可以通过在交互式提示符下输入相应的选项来选择内容和执行操作。

让我们来看一个使用交互模式的例子。在项目中进行了一些更改，修改了一个文件并添加了一个新文件。执行`git add -i`命令可以显示索引的状态:

```
 staged     unstaged path
  1:    unchanged       +54/-7 file.txt

*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

如您所见，我们可以使用很多命令:

*   `status`给出一个状态输出，显示每个文件中添加和删除了多少行(分为阶段化和非阶段化变更)
*   `update`允许您将完整的文件存放到索引中
*   `revert`允许您从索引中分离变更，再次操作整个文件
*   `add untracked`允许您添加未跟踪的文件
*   `patch`的功能与`--patch`相同
*   `diff`向您展示指数和头部之间的差异

`quit`和`help`是不言自明的。

请注意，提示询问您希望下一步做什么。输入数字或命令的第一个字母，您将继续操作。

## 状态

键入`s`将简要显示您在暂存区中拥有的内容:

```
 staged     unstaged path
  1:    unchanged       +54/-7 file.txt

*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

编号(`1`)只是一个标识符，可以作为暂存区中项目的参考，以便进一步使用该接口。`+/-`后面的数字代表文件名为`file.txt`的文件中增加/删除的行数。

请注意，此处看不到新文件(因为它尚未转移)。

## 更新

使用`update`命令，所有在所需文件中所做的更改将被添加。选择该命令后，我们将看到以下输出:

```
 staged     unstaged path
  1:    unchanged       +54/-7 file.txt
Update>>
```

选择 1 后，接口将通过在引用号前放置`*`来通知我们文件已准备提交。通过按回车键，我们将返回到主提示符。如果看现在的状态，可以看到`file.txt`已经上演妥当。

```
 staged     unstaged path
  1:       +54/-7      nothing file.txt

*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

## 归还

如果您决定要取消一组变更，您可以使用`revert`命令来完成。

```
 staged     unstaged path
  1:       +54/-7      nothing file.txt
Revert>>
```

让我们现在检查状态

```
 staged     unstaged path
  1:       unchanged       +54/-7 file.txt*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

## 添加未跟踪的

正如我们之前看到的，新文件在命令输出中是不可见的。现在就来`add untracked`吧。

```
 1: file1.txt
Add untracked>>
```

让我们现在检查状态

```
 staged     unstaged path
  1:       unchanged       +54/-7 file.txt
  2:        +1/-0         nothing file1.txt

*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

## 修补

该命令与`git add -p`相同。点击查看详情[。](https://medium.com/@milan.brankovic/git-partial-staging-86eb01ea6b7d)

## 差速器

要检查差异(如果有的话)，`diff`会提供。

```
 staged     unstaged path
  1:        +1/-0        +2/-0 file1.txt
Review diff>>
```

然后呢

```
diff --git a/file1.txt b/file1.txt
new file mode 100644
index 00000000..e69de29b
*** Commands ***
  1: **s**tatus      2: **u**pdate      3: **r**evert      4: **a**dd untracked
  5: **p**atch       6: **d**iff        7: **q**uit        8: **h**elp
What now>
```

每个命令都允许您选择要处理的文件。您可以通过输入文件编号(或高亮显示的字符)并按 enter 键来选择多个文件。您可以一次输入多个数字/字符，用空格分隔，或者单独添加。选定的文件总是在其行前标有`*`。