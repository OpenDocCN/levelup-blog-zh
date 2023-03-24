# 编写更安全的 Shell 脚本的 9 个技巧

> 原文：<https://levelup.gitconnected.com/9-tips-for-writing-safer-shell-scripts-b0c185da9bae>

## 改进和保护我们脚本的简单方法

![](img/527af3627d76db5db59a50d194d94ab7.png)

奥斯卡·西尔万在 [Unsplash](https://unsplash.com/s/photos/shells?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

多亏了 WSL，Shell 脚本是一个在所有平台上都可用的强大工具，甚至是 Windows。但是很容易犯错误。这里有一些改善我们的脚本和避免许多问题的技巧。

```
**TABLE OF CONTENTS**#1: [Better Shell Options](#379c)
#2: [It's a TRAP](#1cc6)
#3: [Check Requirements](#9b7b)
#4: [Temporary Files & Directories](#2f63)
#5: [Quoting (almost) everything](#729e)
#6: [Linting with ShellCheck](#3831)
#7: [Doin' it in Style](#751d)
#8: [Targeting the Right Shell](#610f)
#9: [Don't Use Shell Script](#cf97)
```

# #1:更好的外壳选项

所有 shells 都有可配置的选项，可用于启用行为。其中许多被认为比 shell 缺省值更安全。

## 出错时失败

一个绝对不需要动脑筋的选择是`set -e`。

使用此选项，任何返回非零状态的命令都将退出整个脚本，并且不再执行任何命令。

默认情况下，不管出现任何错误，shell 脚本都会完全运行:

```
***<< INPUT >>***#!/usr/bin/env bash# Non-existing command
non-existent
echo "Will still be echoed" ***<< OUTPUT >>***line 4: non-existent: command not found
Will still be echoed
```

通过设置`-e`我们可以防止`echo`:

```
***<< INPUT >>***#!/usr/bin/env bash
set -e# Non-existing command
non-existent
echo "Won't still be echoed" ***<< OUTPUT >>***line 5: non-existent: command not found
```

为了使它实际上更有用，有一些例外:

*   `||`命令列表:将对整个列表进行评估，如有必要，随后将失败:

```
# The command list will evaluate to true, so
# we can use set -e, and circumvent it, if necessary
non-existent || true
```

*   测试条件允许失败，如`if`、`while`、`until`:

```
***<< INPUT >>***if non-existent; then
    echo "success"
else
    echo "failure"
fi
echo "after the failure"***<< OUTPUT >>***failure
after the failure
```

## 未设置变量时失败

再也不要被未设置的变量绊倒了！

通过使用`set -u`，脚本将使用一个未设置的变量退出:

```
**<< INPUT >>**#!/usr/bin/env bash
set -u# Non-existing variable
echo "${NOT_SET}"
echo "Won't be echoed" **<< OUTPUT >>**line 5: NOT_SET: unbound variable
```

当然，规则也有一些例外:

*   特殊参数`@`和`*`仍然允许。
*   可以设置默认值:

```
ACTUAL_PATH=${MAYBE_SET_PATH:-~/home/$USER}
```

不过，有一点需要注意。如果为空，则空数组被认为是未设置的。

## 更安全的管道

默认情况下，管道的退出代码由最后一个命令确定，而不考虑任何先前的非零状态代码:

```
**<< INPUT >>**#!/usr/bin/env bashnon-existent | echo -e "last command"
echo $? **<< OUTPUT >>**last command
bash: non-existent: command not found
0
```

使用`set -o pipefail`，如果所有部件都成功退出，管道仅返回零状态。

这不会影响管道的所有部分都将被执行。但是现在我们可以用管道来使用`set -e`。

## 最好是空球

文件名扩展，又称[](https://en.wikipedia.org/wiki/Glob_(programming))*，可以是很多 bug 的根源。*

*一个特别的*有问题的*缺省是对*空扩展*的处理。*

*如果通过扩展 glob 没有找到文件，默认情况下，它将“按原样”(passglob)传递，而不是一个空变量:*

```
***<< INPUT >>**#!/usr/bin/env bashfor f in *.log; do
    echo "$f"
done**<< OUTPUT if no log files found >>***.log*
```

*这个行为可以被`shopt -s nullglob`禁用，这在一些非 bash shells 中已经是默认的了:*

```
***<< INPUT >>**#!/usr/bin/env bashshopt -s nullglobfor f in *.log; do
    echo "$f"
done**<< NO OUTPUT if no log files found >>***
```

## *禁用全球绑定*

*选项`set -f`完全禁用文件名扩展。如果我们的脚本不需要 globing 并防止任何意外的扩展，这是一个好主意。*

## *调试输出*

*选项`set -x`启用跟踪模式，在实际执行之前将每个命令打印到`stdout`。*

*请记住，所有选项也可以从外部设置:*

```
*bash -x my-script.sh*
```

# *#2:这是个陷阱*

*我们的脚本通常不是无状态的，这就产生了某种清理的需要。尤其是在错误提前退出的情况下，我们需要一种得到通知的方式。*

*通过使用内置的`trap`功能，我们可以为特定的[信号](https://man7.org/linux/man-pages/man7/signal.7.html)注册要执行的命令:*

```
*# trap <command> <signals>*
```

*我们可以直接使用函数或命令:*

```
*function cleanup() {
    # ...
}trap cleanup EXITtrap 'rm command.lock' ERR*
```

*可以通过使用`$LINENO`来改进错误处理，从而真正知道问题出在哪里。*

## *资源*

*   *[Bash Trap 命令](https://www.linuxjournal.com/content/bash-trap-command) (Linux 日志)*

# *#3:尽早检查需求*

*如果我们的脚本依赖于默认安装中通常找不到的外部程序，我们应该首先检查它们是否存在。或者我们在后来的脚本中遇到了问题:*

```
*# from one of my internal scripts
REQUIREMENTS=(jq ssh sed nc column)
for APP in "${REQUIREMENTS[@]}"; do
    command -v "$APP" > /dev/null 2>&1
    if [[ $? -ne 0 ]]; then
      >&2 echo "Required '$APP' is not installed"
      exit 1
    fi
done*
```

*这个小片段有助于确保所有需求都是可用的，或者在第一个不可用的命令出现时退出。*

# *#4:临时文件和目录*

*需要临时文件的原因有很多:下载、原子操作等。*

*临时文件或目录的随机名称是强制性的，否则我们可能会不小心覆盖某些内容。shell 帮助我们使用`mktemp`，在`/tmp`中创建文件和目录:*

```
*# Random filename
mktemp# Custom filename (X = random char)
mktemp -t foo.XXXXXX# Random directory
mktemp -d# Custom directory (X = random char)
mktemp -d -t foo.XXXXXX*
```

*该命令创建文件或目录，而不仅仅是返回一个随机的名称/路径。它有一个`--dry-run / -u`选项，但被认为不安全。*

*还有更多选项可以定制生成的文件名/路径，查看其[手册页](https://www.gnu.org/software/autogen/mktemp.html)。*

# *#5:引用(几乎)所有内容*

*始终使用引号。引用太多总比引用不够好。*

*如前所示，自动参数扩展可能是许多 bug 的来源。为了保留字符串的字面意义，我们需要引用它。*

*如果一个字符串包含空格或星号，它就是一个定时炸弹:*

```
*FILENAME="This contains spaces"
touch $FILENAME
# Creates 3 files:
# - This
# - contains
# - spaces*
```

*通过在展开变量时引用变量，我们可以确保结果将作为单个参数传递:*

```
*FILENAME="This contains spaces"
touch "$FILENAME"
# Creates 1 file:
# - This contains spaces*
```

# *#6:林挺与谢尔克斯*

*很容易将我们的脚本与 350 种不同的规则进行对比！*

*[https://www.shellcheck.net/](https://www.shellcheck.net/)*

*这里有一个例子:*

```
***<< INPUT >>**#!/bin/sh
## Example: a typical script with several problems
for f in $(ls *.m3u); do
    grep -qi hq.*mp3 $f \
    && echo -e 'Playlist $f contains a HQ file in mp3 format'
done **<< OUTPUT >>****Line 3:**
for f in $(ls *.m3u); do
         **^-- SC2045**: Iterating over ls output is fragile. Use globs.
              **^-- SC2035**: Use ./*glob* or -- *glob* so names with dashes won't become options.

**Line 4:**
  grep -qi hq.*mp3 $f \
           **^-- SC2062**: Quote the grep pattern so the shell won't interpret it.
                   **^-- SC2086**: Double quote to prevent globbing and word splitting.Did you mean: (apply this, apply all SC2086)
  grep -qi hq.*mp3 "$f" \

**Line 5:**
    && echo -e 'Playlist $f contains a HQ file in mp3 format'
            **^-- SC2039**: In POSIX sh, echo flags are undefined.
               **^-- SC2016**: Expressions don't expand in single quotes, use double quotes for that.*
```

*ShellCheck 突出显示了检测到的问题，向我们展示了到底是哪里出了问题，所以可以很容易地修复它。它可以在大多数 Linux 发行版的仓库中获得，也可以集成到许多编辑器中，比如 [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=timonwong.shellcheck) 。*

# *#7:做得有格调*

*[我在](https://medium.com/better-programming/habit-driven-development-and-finding-your-own-style-32786e1eb8c8)之前已经讨论过为我们的首选语言使用风格指南，以建立一个良好的基线。*

*提到最多的 shell 脚本样式指南是 Google 的“ [Shell Style Guide](https://google.github.io/styleguide/shellguide.html) ”。*

*这是一个广泛的阅读，但值得。但是像往常一样，不要强求不适合的。例如，我强烈支持使用 4 个空格和大约 110 个空格的行长度。没有时尚指南会让我改变主意。但如果项目需要，我愿意适应。*

# *#8:瞄准正确的外壳*

*有多种 shells 可供使用，有不同的选项。*

*尤其是在容器时代，我们遇到一个成熟的 bash shell 已经不再是一件理所当然的事情。*

*这就是为什么我们应该努力选择正确的 shell 类型，而不要过于依赖特定于 shell 的行为。至少如果我们不能绝对确定我们正在哪个环境中运行。*

> *[Bash、Zsh 和其他 Linux Shells 有什么区别？](https://www.howtogeek.com/68563/htg-explains-what-are-the-differences-between-linux-shells/)(如何极客)*

*如果不确定，一个 [*shebang*](https://en.wikipedia.org/wiki/Shebang_(Unix)) 告诉正在执行的 shell 脚本应该如何运行:*

```
*#!/bin/bash*
```

*如果`/bin`里没有`bash`而是有`/usr/bin`呢？我们可以利用`env`通过返回`$PATH`中的第一个事件来获得正确的位置:*

```
*#!/usr/bin/env bash*
```

# *#9:不要使用外壳脚本*

*作为开发人员的一个重要方面是了解语言和工具的局限性。不是所有东西都应该是 shell 脚本。许多高级语言一开始就提供了更安全、更简洁的环境。*

*多亏了 shebangs，我们可以在脚本中使用许多不同的语言，只要有合适的解释器:*

*   *[NodeJS](https://kevingimbel.de/blog/2017/01/writing-nodejs-cli-tools/)*
*   *[巨蟒](https://www.python.org/dev/peps/pep-0394/)*
*   *[Java 11+](https://openjdk.java.net/jeps/330)*
*   *[戈朗](https://gist.github.com/posener/73ffd326d88483df6b1cb66e8ed1e0bd)*
*   *[Lua](http://manpages.ubuntu.com/manpages/bionic/man1/lua-any.1.html)*
*   *还有更多，只需谷歌" <language>shebang "</language>*

*或者我们可以一直使用“编译成单二进制”风格的语言，比如 [Golang](https://golang.org/) 或 [Rust](https://www.rust-lang.org/) 。它们还支持交叉编译，因此很容易不仅仅针对我们自己的平台。*

# *结论*

*Shell 脚本非常棒。从自动化一个微小的任务，到像 [bashtop](https://github.com/aristocratos/bashtop) 这样成熟的 TUI 应用，几乎一切皆有可能。*

*但是与高级语言相比，语法有点不同寻常。错误很容易被引入，即使只是一个打字错误。*

*设置正确的选项和林挺我们的脚本将有助于首先避免问题的共同来源。它们易于使用并集成到我们的工作流中，不应该因为方便而被忽略，尤其是如果其他人不得不使用我们的脚本。*

*对于更好的 shell 脚本，您最喜欢的技巧和诀窍是什么？*

## *资源*

*   *[Bash 参考手册](https://www.gnu.org/software/bash/manual/bash.html)(Gnu.org)*
*   *[bash 中安全做事的方法](https://github.com/anordal/shellharden/blob/master/how_to_do_things_safely_in_bash.md)*
*   *[外壳风格指南](https://google.github.io/styleguide/shellguide.html)(谷歌)*
*   *[外壳检查](https://www.shellcheck.net/)*