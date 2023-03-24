# 立即开始您的脚本之旅| Bash 脚本—第 1 部分

> 原文：<https://levelup.gitconnected.com/start-your-scripting-journey-today-bash-script-part-1-46cbddf4e4e7>

## 编写 Bash 脚本需要知道的一切

![](img/f9a3d10876fa89c80e4235b0deb4567d.png)

照片由[Ty suggest](https://unsplash.com/@burtonts?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 你的第一个剧本

一个 **Bash 脚本** t 是一个包含一系列命令的纯文本文件。我们通常可以从命令行运行的任何东西都可以用来创建脚本。

bash 脚本的第一行:

> #!/bin/bash

井号和感叹号 **( #！)**字符序列被称为**射帮**。后跟" **/bin/bash"** 哪个是应该用来运行其余命令的解释器的路径。

让我们创建我们的第一个 bash 脚本:

**执行过程:** 我们可以使用相对路径和绝对路径来执行一个脚本。在执行脚本之前，我们需要使用 **chmod** 命令使脚本可执行:

```
**$** chmod **+x** fisrtscript.sh  # Make the script executable
**$** ./firstscript.sh      # Executing the script (using relative path)-------------------------------------
 Hello Learners!
```

在 bash 脚本中， **"#"** 之后的任何内容都将被假定为注释。

## 任务— 1

假设，我们需要安装一个 apache 服务器，然后我们必须将一个简单的静态网页部署到 web 服务器中。由于静态网页存储在 GitHub 中，我们必须将相应的 git 存储库克隆到 web 服务器的默认目录中。

正如我们前面讨论的，bash 脚本是命令的集合。让我们试着通过创建一个脚本来实现我们的目标；

执行上面的脚本:

```
**$** chmod **+x** webserver-setup.sh 
**$** ./webserver-setup.sh 
```

现在，让我们检查 apache 服务器是否为我们的静态网站提供服务:

```
curl http://18.181.179.198/
```

## 变量

变量只不过是指向实际数据的指针。shell 使我们能够创建、分配和删除变量。可以通过以下方式创建变量:

> GREET="Hello，World"
> echo $GREET

要将一个变量和一个字符串连接起来，我们必须使用方括号{ }。例子—

> TIME=12
> DAY= "星期一"
> 
> echo“现在是${TIME}pm，$DAY”

## 任务— 2

创建一个包含多个变量的脚本，并为这些变量分配不同类型的数据。

```
**# Execute the script**
**$** chmod **+x** variables.sh **$** ./variables.sh----------------------------
Hello, World
It's 12pm, Monday
Money exchange rate:
USD TO YEN: 1$ = 144.7655¥
```

## **命令行参数**

命令行参数是我们可以在执行过程中提供给我们的 **bash 脚本**的参数。

为了给 bash 脚本传递一个参数，我们只需要在执行它的时候把它写在 Bash 脚本的名字后面；

> 。/script . sh<argument></argument>

第一个参数将存储在 **$1** 中，第二个存储在 **$2** 中，第三个存储在 **$3 中…..$9.**我们可以使用 **$1** 到 **$9** 来存储命令行参数。

> echo“你最喜欢的电视剧是:$ 1”
> echo“你最喜欢的电影是:$2”

## 任务— 3

用命令行参数稍微修改一下上面的[脚本。](#db1f)

```
**# Execute the script with arguments
$** chmod **+x** command-line-args.sh
**$** ./command-line-args.sh  **12  monday  1  144.23**--------------------------------------------------------------------
Hello, World
It's 12pm, monday
Money exchange rate:
USD TO YEN: 1$ = 144.23¥
```

## **特殊和保留变量**

以下是一些特殊变量的列表:

> ***$0 —*** *当前脚本的文件名。* ***$ 1-$ 9****—存储前 9 个参数的名称* ***$ $—****当前 bash 的进程 id。* ***】$ #****—提供给脚本的参数数量。* ***】$ *—****将所有命令行参数连接在一起存储。* ***$ @****—将参数列表存储为数组。* ***$？*** *—指定最后一个命令或最近一次执行过程的退出状态。*

以下是一些保留变量的列表:

> ***主机类型*** *—当前主机的名称。* ***主机名*** *—生成 0 到 32767 之间的随机整数。* ***【PWD】****—返回当前工作目录。* ***OSTYPE****—返回正在运行的操作系统 Bash。*

更多关于[中特殊变量和保留变量的信息，请点击](https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html)。

## 任务— 4

使用一些特殊的和保留的变量创建一个脚本。

```
**# Execute the script
$** chmod +x special.sh
**$** ./special.sh --------------------------------------------------------------------
Welcome to TASK-3Environment configuration: 
 OSTYPE: linux-gnu
 HOSTTYPE: x86_64
 HOSTNAME: centosScript details:
 Script name: ./special.sh
 Process Id: 8940
```

## 环境变量

**预定义环境** 系统中有许多预定义的环境变量。使用 **printenv** 命令找出所有预定义的环境变量。

```
**$** printenv--------------------
XDG_SESSION_ID=163
HOSTNAME=centos
TERM=xterm
SHELL=/bin/bash
--------------------**$** echo **$SHELL**
/bin/bash**$** echo **$HOSTNAME**
centos
```

**创建一个新的环境变量** 我们可以在 Linux 中使用 **export** 命令创建一个新的环境变量:

```
**$** **export** APP_MODE="test"
```

上面创建的环境变量可以在 bash 脚本中使用:

> echo "当前应用模式为:$APP_MODE "

如果我们关闭终端，重启服务器，或者会话结束，env 变量将不会持久。

要永久保存 env 变量，有两种方法:我们可以只保存相应用户的 env 变量，或者保存所有用户的 env 变量。

为了只为相应的用户保存 env 变量，我们必须将 env 变量放在**下。bashrc** 文件位于用户的主目录中。

```
# Add env into the .bashrc
**$** echo "export APP_MODE=test" >> **~/.bashrc**# Reload the file
**$** source **~/.bashrc****$** echo **$APP_MODE**
----------------
test
```

要为所有用户保存环境变量，我们必须将环境变量放在 **/etc/profile** 文件下。

```
# Add env into the **/etc/profile** file
$ su
$ vim  echo "export APP_VERSION=1.25.0" >> **/etc/profile**# Reload the file
$ source **/etc/profile**$ echo **$APP_VERSION**
-------------------
1.25.0
```

## 任务— 5

将上面创建的 env 变量用于脚本:

```
**# Execute the script
$** chmod **+x** env.sh **$** ./env.sh--------------------------------------------------------------------Current app mode is: test
Current app version is: 1.25.0
```

## 命令替换

Bash **中的命令替换允许我们执行一个命令，并用它的标准输出**替换它。

> ***注意:*** *该命令在子 shell 中执行，这意味着它有自己的环境，因此不会影响父 shell 的环境。*

```
**# Execute the script
$** chmod **+x** command-sub.sh
**$** ./command-sub.sh----------------------------------------
Mon Oct 3 09:02:21 UTC 2022
----------------------------------------
Available space in 'dev' disk: /dev 474M
Used Memory: 295M
```

## 引用

我们可以在 bash 脚本中同时使用单引号和双引号。但是单引号和双引号的用例是不同的:

```
**# Double quotes**
**$** echo "hostname is: $HOSTNAME"
--------------------------------
hostname is: centos
--------------------------------**# Single quotes
$** echo 'hostname is: $HOSTNAME'
--------------------------------
hostname is: **$HOSTNAME** 
--------------------------------
```

**单引号**忽略所有特殊字符(如— `**$ & * ; |.'**`)，按原样打印语句。

相比之下，要在**双引号**中打印一个特殊字符，我们必须在我们要打印的字符前面使用反斜杠 **( \ )** 。

```
 **$** echo "hostname is:**\"**$HOSTNAME**\"**"
--------------------------------
hostname is: "centos" **$** echo "I have only 5**\$** today"
--------------------------------
I have only 5$ today
```

## 用户输入

为了创建一个交互式脚本，我们可能需要从用户那里获得输入。使用**读取**命令，我们可以从用户处获得输入。

如果没有指定 NAME，则使用名为 REPLY 的变量来存储用户输入。

```
$ read 
$ echo $REPLY
```

读取并插入变量:

```
$ read $VAR
$ echo $VAR
```

我们可以在读取数据时使用各种选项。

> ***-p*** *提示用户输入一些消息* ***-s****提示用户输入一个秘密* ***-n****限制使用*的字符数

## 任务— 6

创建一个脚本，您需要从用户那里读取数据，并尝试在双引号中使用特殊字符。

```
**# Execute the script
$** chmod **+x** userlogin.sh
**$** ./userlogin.sh----------------------------------------
Welcome to "kubehub.com"
USENAME: shamim           
Enter your 4 digit PIN
PIN: 
Successfully singed in
Welcome, shamim
```

## Bash 中$()和${ }的区别

在命令替换期间，我们必须使用 **$(命令)。** 另一边，我们可以用 **${paramtere}** 来避免歧义。

```
**# command substitution $()**
**$** USED_MEM=$(free -m | grep Mem | awk '{print $3}')**# without using ${}**
**$** echo $USED_MEMMB      
  ------------------------
  No output, because there is no variable named "USED_MEMMB"**# by using ${}**
**$** echo ${USED_MEM}MB  
  -------------------------
  295MB
```

## **下一个—**

[](https://medium.com/geekculture/start-your-scripting-journey-today-bash-script-part-2-4d93ecb59249) [## 立即开始您的脚本之旅| Bash 脚本—第 2 部分

### 编写 Bash 脚本需要知道的一切

medium.com](https://medium.com/geekculture/start-your-scripting-journey-today-bash-script-part-2-4d93ecb59249) 

> *如果你觉得这篇文章很有帮助，请* ***别忘了*** *来点击* ***跟随*******拍拍*** *按钮，帮我写更多这样的文章。* ***谢谢🖤****