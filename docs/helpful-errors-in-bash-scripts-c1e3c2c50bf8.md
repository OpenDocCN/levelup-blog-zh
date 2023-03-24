# 如何在 Bash 脚本中提供有用的错误

> 原文：<https://levelup.gitconnected.com/helpful-errors-in-bash-scripts-c1e3c2c50bf8>

![](img/87f0287bedc69be0be45032d63dc8358.png)

一个有益的错误，没有有益的错误(图片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[specphops](https://unsplash.com/@specphotops?utm_source=medium&utm_medium=referral)拍摄)

在我之前的帖子中，我写了关于[编写不出意料的 bash 脚本](https://medium.com/@timothyjones_88921/top-tips-for-writing-unsurprising-bash-scripts-9b9f4f0cc30e)。最后一条准则是不要编写复杂的程序——尽量使用不超过一两个函数，并尽可能少使用分支逻辑。像所有的准则一样，有时它们注定要被打破。

错误处理是我最常违反这条准则的地方。这篇文章是关于为什么，我想推荐一些新的指导方针。

![](img/b40af10e8ad0f1580dd1f0780e4f65fd.png)

巴博萨船长也有一些关于指导方针的规定

如果您的脚本因为一个未定义的变量而失败，那就不太好了:

```
$ ./test.sh
./test.sh: line 3: SOME_FILENAME: unbound variable
```

作为脚本的用户，这不是一个令人愉快的界面。如果脚本打印出有用的错误就更好了。类似于:

```
$ ./test.sh
Error: The required environment variable SOME_FILENAME is empty
  - set this to the file under test
```

理想情况下，难看的错误只发生在脚本有问题的时候。我们希望给脚本的未来用户最好的机会来正确运行它。

> 理想情况下，难看的错误只发生在脚本有问题的时候。

Bash 脚本通常带有关于参数和环境的假设。如果这些假设不满足，我们希望脚本能很好地出错。

![](img/023fb1061abcb50057eaca85c1598c88.png)

这个错误假设路径在哪里是显而易见的(照片由 [Mark Duffel](https://unsplash.com/@2mduffel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# 如何编写好的错误检查

在脚本中编写理智防护打破了“不要分支太多”的准则。但是，也可以使用其他一些准则来代替:

*   在执行任何事情之前，首先检查你的前提条件
*   不满足前提条件时，立即退出脚本
*   尽量不要嵌套错误检查

我将进一步阐述基本原理，然后以一些常见检查的代码示例结束。

## 首先检查先决条件，如果不满足就失败

前提条件是设置环境变量(具有有效的内容)，以及所需的二进制文件存在。

上面的前两个要点是相关的——如果我们在任何执行之前进行前提条件检查，并且如果前提条件没有得到满足就立即使脚本失败，那么脚本执行就不会在中途失败。要么执行脚本，要么不执行任何工作负载(前提条件检查除外)。

这很重要，因为它有助于未来的用户(以及脚本作者)思考发生了什么。原子脚本执行意味着没有人需要考虑如何回滚或清理部分脚本执行。

## 尽量不要嵌套错误检查

我们这样做是为了使代码仍然可读，即使它有几个分支。如果每个检查都以一个`exit 1`结束，那么脚本中只有一条“快乐的路径”,这有助于提高可读性。

顶层错误检查的另一个优点是它们必须保持简单。保持它们的简单能让未来的读者通过快速浏览来发现关于环境的假设。

# 一些代码示例

![](img/b63cd315e5bc276c4710aa4dcf59da4d.png)

我不知道这个人是谁，但我支持他的信息(照片由[希泰什·乔杜里](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

上述指南不要求您使用任何特定的代码。然而，有两个可重用的函数可以检查两个常见的前提条件:必需的环境变量和必需的二进制文件。

## 需要环境变量

下面是我用来检查环境变量是否存在的方法:

```
# Check to see that a required environment variable is set.  
# Use it without the $, as in:
#   require_env_var VARIABLE_NAME  
# or   
#   require_env_var VARIABLE_NAME "Some description of the variable"function require_env_var {
  var_name="${1:-}"
  if [ -z "${!var_name:-}" ]; then
    echo "[X] The required env variable ${var_name} is empty"
    if [ ! -z "${2:-}" ]; then        
       echo "  - $2"     
    fi
    exit 1
  fi
}
```

这个函数有两个参数，一个是变量名，另一个(可选)是要打印的有用的用法字符串。你可以这样使用它:

```
require_env_var SOME_VARIABLE# orrequire_env_var SOME_VARIABLE "set this to the file under test"
```

## 需要二进制文件

下面是我用来检查所需的二进制文件是否存在的方法:

```
# Check to see that we have a required binary on the path
function require_binary {
  if [ -z "${1:-}" ]; then
    echo "${FUNCNAME[0]} requires an argument"
    exit 1
  fi if ! [ -x "$(command -v "$1")" ]; then
    echo "The required executable '$1' is not on the path."
    exit 1
  fi
}
```

同样，你可以这样使用它:

```
require_binary git
require_binary npm
require_binary grep
```

![](img/d1daae5e92d25fc131725d8ab2af07f3.png)

好吧，我跟你说实话。这只是为了打破文本(图片由 [Alex Grodkiewicz](https://unsplash.com/@morgasetrakand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

## 包括这些功能

假设您有几个想要使用这些函数的脚本。我已经把它们放在一个要点中，你可以直接放入你的项目中。您可以像这样包含它:

```
#!/bin/bash -eu# Figure out where the script is running
__SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")"; pwd)" # Include the robust-bash functions
. "$__SCRIPT_DIR"/lib/robust-bash.sh# Make sure the user has greprequire_binary grep# Make sure the environment variables are set correctlyrequire_env_var SOME_FILENAME "set this to the file under test"
```

上面包含了一个 bash 样板文件:

```
__SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")"; pwd)"
```

这指出了脚本运行的位置，除非通过符号链接调用(如果您需要处理通过符号链接调用的脚本，请参见[这个极好的要点](https://gist.github.com/TheMengzor/968e5ea87e99d9c41782))。

## 摘要

帮助您从编写良好的 bash 脚本中打印出有用的错误消息的一些准则是:

*   在执行任何事情之前，首先检查你的前提条件
*   不满足前提条件时，立即退出脚本
*   尽量不要嵌套错误检查

如果您的脚本在没有达到预期时打印出有用的错误消息，那么用户会更高兴(并且不会浪费太多时间去判断您的脚本是否有错误)。在我看来，为了给用户带来愉快的健康检查和错误消息，增加脚本的复杂性(一点点)是值得的。