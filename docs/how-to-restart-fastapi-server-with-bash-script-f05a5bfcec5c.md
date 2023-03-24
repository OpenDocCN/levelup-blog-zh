# 如何用 Bash 脚本重启 FastAPI 服务器

> 原文：<https://levelup.gitconnected.com/how-to-restart-fastapi-server-with-bash-script-f05a5bfcec5c>

一个简单的窍门让你的生活更轻松

![](img/9c6bc053ececba09abd00b88b8a13bdd.png)

照片由 [Sai Kiran Anagani](https://unsplash.com/@_imkiran?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/automation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通过阅读这篇文章，您将学会创建一个 bash 脚本，该脚本可以自动执行 FastAPI 服务器(生产)的重启过程。如您所知，您可以在开发过程中使用 reload 参数重启服务器。

```
uvicorn myapp:app --reload
```

当 FastAPI(uvicon ASGI)检测到文件中的变化时，它将为您触发一次重新加载。但是，您不应该对生产服务器使用`reload`参数。这仅仅是因为它会占用你的 CPU 资源来检测本地文件的变化。

大多数时候，您会使用`nohup`和`&`参数来部署它，这允许进程继续在后台运行。因此，可能会出现您只想重新启动 FastAPI 服务器的情况:

*   更新配置文件
*   错误修复
*   添加新功能

# 手动重启

我重启 FastAPI 服务器的常用程序如下:

*   检查运行 FastAPI 服务器的进程 ID
*   终止或终止进程
*   清理 nohup 文件
*   启动 FastAPI 服务器

## 检查运行 FastAPI 服务器的进程 ID

有多种方法可以检查正在运行的 FastAPI 服务器所使用的 PID。例如，通过`netstat`命令检查。

```
netstat -tulpn
```

它将与`Local Address`一起显示`PID/Program name`列表。一个主要的缺点是，您必须记住服务器使用的端口，因为 FastAPI 服务器的`Program name`将被列为`python`。如果你在运行多个 python 应用，你只能通过`Local Address` (IP 和端口)来区分每一个。

另一种方法是依靠`ps`命令，该命令显示与特定过程相关的详细信息。可以和`grep`(全局正则表达式打印)命令配对，通过 regex 找到想要的进程。假设您的服务器使用以下命令启动。

```
nohup uvicorn myapp:app &
```

您可以使用以下命令获得服务器的 PID

```
ps aux | grep 'uvicorn myapp:app'
```

您可以指定`aux`选项来返回与用户、所有进程和终端相关的信息。如果一切正常，您应该在控制台上看到两个 PID。一个代表运行在机器上的 FastAPI 服务器，另一个代表 grep 进程。

```
grep --color=auto uvicorn myapp:app
```

事实上，您可以通过在命令中添加`grep -v grep`来排除 grep 进程。

```
ps aux | grep 'uvicorn myapp:app' | grep -v grep
```

## 终止或终止进程

一旦有了 FastAPI 服务器的 PID，就可以使用 kill -9 PID 正常终止或终止进程。假设您的 PID 是 12345，您应该如下运行它:

```
kill -9 12345
```

你可以选择用`-15`而不是`-9`优雅地杀死它，这取决于服务器的复杂性和配置。

## 清理 nohup 文件

默认情况下，该流程会将所有消息输出到一个名为`nohup.out`的文件中，除非您明确指定了自己的文件。我更喜欢每次都清除它，以便于调试和维护。您可以通过运行来轻松清除它

```
echo "" > nohup.out
```

## 启动 FastAPI 服务器

之后，您可以通过以下方式正常启动 FastAPI 服务器

```
nohup uvicorn myapp:app &
```

当您需要多次重新启动时，手动方式既耗时又令人畏惧。此外，你可能会在这个过程中犯错误。

# 通过 Bash 脚本重启

利用 bash 脚本来自动化重启过程更加可靠和简单。首先，创建一个名为`restart_server.sh`的新文件。您需要为它提供足够的权限。建议的方法是

```
chmod 755 restart_server.sh
```

在文件的顶部添加下面的 shebang。这确保了我们的文件将使用 bash shell 来解释和执行。

```
#!/bin/bash
```

接下来，您应该将目录更改为项目的根目录。强烈建议使用绝对路径，以确保脚本可以在您喜欢的任何地方调用。如果您的项目使用虚拟环境，您也应该激活它。

将以下命令添加到您的文件中。确保相应地修改路径。

```
source /home/project/myenv/bin/activate
cd /home/project/server
```

我们的脚本应该在以下任务中完美工作:

*   如果服务器尚未运行，请正常启动它
*   如果存在现有的服务器，终止进程并启动服务器

## 检查运行 FastAPI 服务器的进程 ID

为了完成它们，我们需要识别正在运行的进程并获得它们的 ID。让我们稍微修改一下上面的`ps`命令。我将使用两个额外的命令来传输它，并将其存储为一个变量。

```
PID=$(ps aux | grep 'uvicorn myapp:app' | grep -v grep | awk {'print $2'} | xargs)
```

第一个加法是`awk {‘print $2’}`，它排除了所有输出，只打印 PID 值。假设它检测到 ID 为 1234 和 1235 的两个进程。输出将是

```
1234
1235
```

为了将输出转换成命令，需要指定代表`e**X**tended **ARG**uments`的`xargs`命令。它将按如下方式转换输出:

```
1234 1235
```

一旦我们完成了，通过下面的语法将它存储在一个变量中

```
variable_name=$(your_command)
```

## 终止或终止进程+清理 nohup 文件

变量是通过美元符号前缀引用的

```
$variable_name
```

终止所有正在运行的进程的命令如下:

```
kill -9 $PID
```

在此之前，让我们将其封装在一个条件语句中，以检查是否有任何现有的进程正在运行。这种方法的优点是，我们可以向运行这个 bash 脚本的用户提供一些反馈。此外，我将在重新启动时间之前诱导 2 秒钟的等待时间，以防止端口冲突问题。`nohup`之后文件也会被清除。

```
if [ "$PID" != "" ]
then
kill -9 $PID
sleep 2
echo "" > nohup.out
echo "Restarting FastAPI server"
else
echo "No such process. Starting new FastAPI server"
fi
```

## 启动 FastAPI 服务器

最后，您可以通过以下方式正常启动服务器

```
nohup uvicorn myapp:app &
```

重启 FastAPI 服务器的完整 bash 脚本可以在下面的[要点](https://gist.github.com/wfng92/8211e05134e66285d83a8b06f1c0c7ff)中找到。

## 调用 Bash 脚本

您可以将 bash 脚本放在您喜欢的任何地方。我通常将它直接放在 FastAPI 服务器的根目录中。简单地打电话

```
./path/to/file/restart_server.sh
```

如果您直接在同一个中，您可以如下运行它:

```
./restart_server.sh
```

## 处理多个工人

如果您的服务器在多个工作线程上运行

```
nohup uvicorn myapp:app --workers 4 &
```

我们创建的脚本将无法正常工作，因为 uvicorn 使用多重处理在幕后产生了多个进程。您可以通过以下方式轻松识别它们

```
ps aux | grep 'python -c from multiprocessing'
```

但是，有一个小风险，即其他程序在同一个服务器上运行多处理。为了解决此问题，您可以将虚拟环境路径包括在一起，如下所示:

```
ps aux | grep '/home/project/myenv/python -c from multiprocessing'
```

我们需要做的就是在我们的文件中包含这个命令和随后的`kill -9`命令。查看下面的[要点](https://gist.github.com/wfng92/4882be87a5017508e92fa11198a1d9e8)获得完整的 bash 脚本。

# 结论

让我们回顾一下今天所学的内容。

我们从一个简短的问题陈述开始，这个问题陈述是关于开发人员在重启 FastAPI 服务器时所面临的棘手问题。

接下来，我们深入探讨了手动重启 FastAPI 服务器所涉及的步骤。整个工作流程非常繁琐，容易出现人为错误。

我们继续前进，创建了一个简单的 bash 脚本，它自动化了服务器终止和服务器启动过程。我们可以简单地调用 bash 脚本来重启 FastAPI 服务器。

之后，我们对文件做了一些小的修改，以处理由多个 workers 启动的 FastAPI 服务器。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [重启 FastAPI 服务器要点](https://gist.github.com/wfng92/8211e05134e66285d83a8b06f1c0c7ff)
2.  [重启多工人 FastAPI 服务器要点](https://gist.github.com/wfng92/4882be87a5017508e92fa11198a1d9e8)