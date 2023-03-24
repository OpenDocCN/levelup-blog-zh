# 比“ldflags”更好的向 Go 二进制文件添加构建版本的方法

> 原文：<https://levelup.gitconnected.com/a-better-way-than-ldflags-to-add-a-build-version-to-your-go-binaries-2258ce419d2d>

![](img/93e13a74d2787dff9b82eee1b1f3f9df.png)

像许多使用 Go 的人一样，[我](https://www.linkedin.com/in/andrew-hayes-belfast/)使用“ldflags”将版本号添加到我的二进制文件中。它工作得很好，每次构建二进制文件时，它都会从“git describe”中取出版本，并将该版本添加到二进制文件的变量中。然后，当测试和发现一个错误时，我会知道我在哪个版本的二进制文件中发现了它，一切正常。直到我决定优化我的文档，它停止工作。更糟糕的是，我没有注意到它不再工作了，当一个 bug 被发现时，我不知道它是在哪个版本上被发现的。

# 优化 Dockerfile 文件时,“ldflags”是如何停止工作的？

在最初的开发过程中，我让一些 Make targets 在本地测试和构建 Go 文件。然后，当我为项目创建容器时，我只是从 docker 文件中调用这些相同的 Make 目标。它工作得很好，所以它保持了一段时间。直到我对 docker 构建花费的时间比我想要的稍长感到恼火，所以我决定进行优化。为此，我从 docker 文件中取出 Make 目标，并将每个步骤添加为“运行”命令，这样 docker 可以缓存步骤并节省时间。然而，这是我的毁灭。当在 docker 文件中执行“go build”步骤时，我愚蠢地完全忘记了包括“ldflags”部分。最初的 Make 目标是这样的:

```
go build -v -ldflags="-X 'main.version=`git describe --tags --abbre
v=0`'" -o ./bin/my_binary
```

Dockerfile 文件中的新命令很简单:

```
go build -v -o ./bin/my_binary
```

这两者之间的差异有两点需要注意。首先，“main.version”变量不再用“git describe”中的版本填充。第二，Dockerfile 文件中的命令是完全有效的，运行时不会出现问题，没有任何迹象表明第一个问题正在发生。

# 解决方案:使用 go:generate 和 go:embed！

go:generate 和 go:embed 是 go 工具链中相对较新的工具。 [go:generate](https://go.dev/blog/generate) 是一种在构建期间在代码中运行命令的方式，因此您可以在需要时生成代码等。 [go:embed](https://pkg.go.dev/embed#pkg-overview) 是一种在 go 二进制文件中嵌入外部文件的方法。如果我们把这两者结合起来，我们就有了解决问题的方法！

如果您还没有看到旧的“ldflags”技术的大量其他文章，它看起来像下面这样。你有你的 go 文件:

```
package main
import (
    "fmt"
)var version = ""func main() {
    fmt.Println("build version: ", version)
}
```

然后在构建时包含“ldflags”参数来设置“version”变量。示例:

```
go build -v -ldflags="-X 'main.version=`git describe --tags --abbre
v=0`'"
```

在本例中，它将把“version”变量设置为“git describe”的输出。然而，正如我在上面所描述的，没有任何东西可以确保您已经完成了这一点，并且在没有“ldflags”参数和“version”变量将保持设置为空字符串的情况下，它将非常愉快地构建。

更好的方法是使用 go:generate 和 go:embed！首先，我们需要将 git describe 命令移动到一个 bash 脚本中(我将我的脚本命名为 get_version.sh):

```
#!/bin/bash
git describe --tags --abbrev=0 > version.txt
```

这会将版本写入名为“version.txt”的文件中。我们还可以将“version.txt”添加到。gitignore 文件，这样它就永远不会被签入。

然后更新 go 文件以包含 go:generate 和 go:embed:

```
package mainimport (
  "fmt" _ "embed"
)//go:generate bash get_version.sh
//go:embed version.txt
var version stringfunc main() {
  fmt.Println("build version: ", version)
}
```

要构建它，您只需运行:

```
go generate
go build -v
```

这将生成“version.txt”文件并将其嵌入到二进制文件中。在这一点上，它与“ldflags”技术非常相似。然而，使用这种新技术，如果您不小心省略了“go generate”步骤，它将会失败。

```
$ go build
main.go:10:12: pattern version.txt: no matching files found
```

就像 Go 的类型安全一样，编译器会帮助你捕捉你可能错过的愚蠢错误。