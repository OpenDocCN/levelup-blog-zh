# 在 Go 中使用环境变量

> 原文：<https://levelup.gitconnected.com/using-environment-variables-in-go-4c315a03a6c3>

## 学习如何利用环境变量来存储 Go 程序之外的敏感信息

![](img/f02da8d89ea83eda373d5cae9c1f525b.png)

简·kopřiva 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

环境变量是在操作系统中定义的键值对数据。运行在系统上的应用程序可以访问这些键/值对。将环境变量视为应用程序中的变量或常量，只不过它们是在应用程序之外定义的，并且也可以被其他应用程序访问。

在本文中，我将向您展示如何在 Go 应用程序中利用环境变量。

# 环境变量的使用

使用环境变量的一个主要原因是您不希望在代码中硬编码您的敏感信息(如密码或 API 访问密钥)。相反，您希望在环境变量中设置所有这些敏感信息，以便未经授权的人员看不到这些信息，同时也便于您将应用程序从开发服务器移植到部署服务器。

在我们了解如何使用环境变量之前，让我们先了解一下它们在 Windows 和 macOS 中是如何工作的。

## Windows 操作系统

在 Windows 中，使用`set`命令查看当前定义的环境变量列表。

```
**set**
```

如果您想设置一个新的环境变量，使用`set`命令并指定环境变量及其值，如下所示:

```
**set** MYDB_PASSWORD=topsecret
```

要检索环境变量，使用`echo`命令，并将环境变量放在一对`%`中:

```
**echo** **%**MYDB_PASSWORD**%**
topsecret
```

## 马科斯

在 macOS 中，使用`env`命令查看当前定义的环境变量列表。如果您想设置一个新的环境变量，使用`export`命令并指定环境变量及其值，如下所示:

```
**export** MYDB_PASSWORD=topsecret
```

要检索一个环境变量，使用带有前缀为`$`的环境变量的`echo`命令:

```
**echo** **$**MYDB_PASSWORD
topsecret
```

# 列出 Go 中的所有环境变量

在 Go 中，你可以使用`os`包来访问环境变量。首先，使用`Environ`函数以键/值对的形式返回所有的环境变量(作为一段字符串返回)。下面的代码片段允许您遍历结果并拆分每个键/值对:

```
package mainimport (
    "fmt"
    "os"
    "strings"
)func main() {
    for _, e := range os.Environ() {
        pair := strings.SplitN(e, "=", 2) 
        fmt.Printf("%s\n", pair[0])
    }
}
```

当程序运行时，您应该会得到类似如下的内容:

```
$ **go run main.go**
USER
__CFBundleIdentifier
COMMAND_MODE
LOGNAME
PATH
SSH_AUTH_SOCK
SHELL
HOME
__CF_USER_TEXT_ENCODING
TMPDIR
XPC_SERVICE_NAME
XPC_FLAGS
ORIGINAL_XDG_CURRENT_DESKTOP
SHLVL
PWD
OLDPWD
HOMEBREW_PREFIX
HOMEBREW_CELLAR
HOMEBREW_REPOSITORY
HOMEBREW_SHELLENV_PREFIX
MANPATH
INFOPATH
CONDA_EXE
CONDA_PYTHON_EXE
CONDA_SHLVL
CONDA_PREFIX
CONDA_DEFAULT_ENV
CONDA_PROMPT_MODIFIER
NVM_DIR
NVM_CD_FLAGS
NVM_BIN
NVM_INC
TERM_PROGRAM
TERM_PROGRAM_VERSION
LANG
COLORTERM
VSCODE_GIT_IPC_HANDLE
GIT_ASKPASS
VSCODE_GIT_ASKPASS_NODE
VSCODE_GIT_ASKPASS_MAIN
TERM
_CE_M
_CE_CONDA
```

> 请注意，运行上述程序的每台机器的环境变量列表会有所不同。

如果您想打印环境变量及其相应的值，只需访问拆分后的第二个元素:

```
 fmt.Printf("%s: **%s**\n", pair[0], **pair[1]**)
```

# 设置和获取环境变量

以编程方式，在 Go 程序中，您还可以创建自己的环境变量，以便可以在应用程序中的任何地方访问它的值。为此，使用`Setenv`函数，向其传递环境变量及其值:

```
 err := os.**Setenv("DATABASE_PWD", "topsecret")**
```

如果无法创建环境变量，`Setenv`函数将返回一个错误。

要检索特定环境变量的值，使用`Getenv`函数:

```
 fmt.Printf("Database Password: %s\n", 
        **os.Getenv("DATABASE_PWD")**)
```

如果您试图检索一个不存在的环境变量，您将得到一个空字符串。例如，由于`**DATABASE_NAME**`环境变量不存在，下面返回一个空字符串:

```
fmt.Printf("Database name: %s\n", os.Getenv("DATABASE_NAME"))
```

> 使用`Setenv`设置的环境变量将在您下次再次运行程序时消失——如果您需要使用它，您需要再次设置它，或者将它保存在一个环境文件中(参见本文后面的“从环境文件加载环境变量”一节)。

请注意，空字符串的输出不是很有帮助。这是意味着环境变量不存在，还是意味着这个环境变量的值实际上是一个空字符串？确定这一点的更好的方法是使用`LookupEnv`函数，它返回两个值——指定环境变量的值，以及一个指示环境变量是否存在的布尔结果。以下代码片段显示了如何使用该函数:

```
if v, ok := os.LookupEnv("DATABASE_NAME"); ok {
    fmt.Printf("Database name: %s\n", v)
} else {
    fmt.Println("Key does not exists")
}
```

# 通过命令行传递环境变量

很多时候，您不希望您的代码包含敏感数据。并且最好在执行时将它们传递到程序中。将这些敏感信息传递给程序的一种方法是通过命令行。对于 Go，可以在运行时使用以下格式设置环境变量:

```
$ **DATABASE_NAME=mysql** go run main.go
Database name: mysql
```

在上面的例子中，您在运行程序之前设置了`**DATABASE_NAME**`环境变量。如果要传入多个环境变量，请使用以下格式:

```
**$ DATABASE_NAME=mysql DATABASE_PWD=secret** go run main.go
```

# 从环境文件中加载环境变量

另一种获取环境变量的方法是从一个叫做*环境文件*的外部文件中获取。您可以将应用程序敏感的信息存储在环境文件中，并且您的应用程序可以从该文件中加载所需的数据。为此，我们将使用一个名为**github.com/joho/godotenv**的第三方包。

让我们看看如何使用这个包来存储我们的环境变量。首先，在您的主目录中创建一个名为 **environmentvars** 的文件:

```
$ cd ~
$ mkdir environmentvars
```

创建一个名为 **main.go** 的文件，保存在 **environmentvars** 目录下。用以下语句填充它:

```
package mainimport (
    "fmt"
    "log"
    "os" "github.com/joho/godotenv"
)func main() {
    err := godotenv.Load(".env")
    if err != nil {
        log.Fatalf("Error loading .env file")
    } databasePwd := os.Getenv("MYSQL_DATABASE_PWD")
    fmt.Printf("godotenv : %s = %s \n", "Database Password", 
               databasePwd)
}
```

创建一个名为**的新文件。env** 并将其保存在**环境变量**目录中。用以下语句填充它:

```
MYSQL_DATABASE_PWD=topsecret
```

您可以在**中存储多个环境变量。env** 文件。它们以这种格式表示:

```
*environment_variable1***=***value1
environment_variable2***=***value2
environment_variable3***=***value3*
```

在终端中键入以下命令:

```
$ **cd ~/environmentvars**
$ **go mod init myapp** 
go: creating new go.mod: module myapp
go: to add module requirements and sums:
        go mod tidy
$ **go mod tidy**
go: finding module for package github.com/joho/godotenv
go: found github.com/joho/godotenv in github.com/joho/godotenv v1.4.0
```

以上命令下载**github.com/joho/godotenv**包。现在您可以运行程序并查看输出:

```
$ **go run main.go** 
godotenv : Database Password = topsecret
```

# 摘要

在本文中，您已经了解了什么是环境变量，以及如何在 Windows 和 macOS 中使用它们。更重要的是，你学会了如何在你的围棋程序中访问它们。在您的 Go 程序中使用环境变量会使您的应用程序更加安全，并且消除了在 Go 程序中硬编码敏感信息的需要。

如果你想了解更多关于围棋编程的知识，请查阅我的围棋书籍。

![](img/9dad20a57d6ab73e4b902169e4b34aef.png)

[https://www . Amazon . com/Programming-Language-Dummies-Computer-Tech/DP/1119786193/](https://www.amazon.com/Programming-Language-Dummies-Computer-Tech/dp/1119786193/)