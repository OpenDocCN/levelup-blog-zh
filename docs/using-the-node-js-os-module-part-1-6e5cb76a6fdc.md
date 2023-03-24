# 使用 Node.js 操作系统模块(第 1 部分)

> 原文：<https://levelup.gitconnected.com/using-the-node-js-os-module-part-1-6e5cb76a6fdc>

![](img/b265136afc745209beff8857bec81a3e.png)

由 [Domenico Loia](https://unsplash.com/@domenicoloia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Node.js OS 模块有许多有用的实用函数，用于获取有关运行 OS 模块程序的计算机系统的信息。它可以提供关于硬件的信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、关于当前登录用户的信息等等。

我们可以通过在文件顶部写`const os = require('os');`来使用 OS 模块。OS 模块中有许多有用的属性。以下是操作系统模块中一些有用的属性:

# os。寿命终止

`os.EOL`属性是一个字符串常量，具有特定于操作系统的行尾标记。对于 POSIX 操作系统，它是`\n`，对于 Windows，它是`\r\n`。我们可以像下面的代码一样使用它作为例子:

```
console.log(`End of line market is ${os.EOL}`)
```

然后我们得到:

```
'End of line market is \n'
```

# os.arch()

`os.arch()`函数返回一个字符串，告诉我们编译 Node.js 二进制文件的操作系统 CPU 架构。可能的值有`'arm'`、`'arm64'`、`'ia32'`、`'mips'`、`'mipsel'`、`'ppc'`、`'ppc64'`、`'s390'`、`'s390x'`、`'x32'`和`'x64'`。与`process.arch`功能相同。例如，我们可以在下面的代码中使用它:

```
console.log(`Node.js is built in ${os.arch()}`)
```

我们得到:

```
'Node.js is built in x64'
```

如果 Node.js 是在 x64 系统上编译的

# 操作系统常量

`os.constants`属性包含错误代码、过程信号等常量的集合。以下是`os.constants`中的常量:

## 信号常数

*   `SIGHUP` —控制终端关闭或父进程退出时发出的信号。
*   `SIGINT` —当用户希望中断过程时发出的信号(`(Ctrl+C)`)。
*   `SIGQUIT` —当用户希望终止进程并执行堆芯转储时发送的信号。
*   `SIGILL` —发送给进程的信号，通知它试图执行非法、格式错误、未知或特权指令。
*   `SIGTRAP` —发生异常时发送给流程的信号。
*   `SIGABRT` —发送给进程的信号，请求其中止。
*   `SIGIOT` —同`SIGABRT`
*   `SIGABRTSIGBUS` —发送给进程的信号，通知其导致总线错误。
*   `SIGFPE` —发送给进程的信号，通知它执行了非法算术运算。
*   `SIGKILL` —发送给进程的信号，以立即终止该进程。
*   `SIGUSR1` `SIGUSR2` —发送给流程的信号，用于识别用户定义的条件。
*   `SIGSEGV` —发送给流程的信号，通知分段故障。
*   `SIGPIPE` —当进程试图写入断开的管道时发送给进程的信号。
*   `SIGALRM` —当系统定时器超时时，发送给进程的信号。
*   `SIGTERM` —发送给进程请求终止的信号。
*   `SIGCHLD` —子进程终止时发送给进程的信号。
*   `SIGSTKFLT` —发送到进程的信号，指示协处理器上的堆栈故障。
*   `SIGCONT` —发送信号，指示操作系统继续暂停的进程。
*   `SIGSTOP` —发送信号，指示操作系统停止进程。
*   `SIGTSTP` —发送给流程的信号，请求其停止。
*   `SIGBREAK` —当用户希望中断过程时发送的信号。
*   `SIGTTIN` —当进程在后台从电传打字机中读取数据时，发送给进程的信号。
*   `SIGTTOU` —当进程在后台写入电传打字机时，发送给进程的信号。
*   `SIGURG` —当套接字有紧急数据要读取时，发送给进程的信号。
*   `SIGXCPU` —当进程超过其 CPU 使用限制时发送给进程的信号。
*   `SIGXFSZ` —当文件增长超过允许的最大值时，发送给进程的信号。
*   `SIGVTALRM` —虚拟定时器超时时发送给进程的信号。
*   `SIGPROF` —当系统定时器超时时，发送给进程的信号。
*   `SIGWINCH` —当控制终端改变尺寸时，发送给进程的信号。
*   `SIGIO` —当 I/O 可用时，发送给进程的信号。
*   `SIGPOLL` —同`SIGIO`
*   `SIGIOSIGLOST` —当文件锁定丢失时发送给进程的信号。
*   `SIGPWR` —发送给过程的信号，通知电源故障。
*   `SIGINFO` —同`SIGPWR`
*   `SIGPWRSIGSYS` —发送给流程的信号，通知错误的参数。
*   `SIGUNUSED` —同`SIGSYS`

![](img/432224a4e10b3c08ac7188119fb1ab50.png)

[傅勇华](https://unsplash.com/@hhh13?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## POSIX 系统的误差常数

以下是 POSIX 系统中出现的错误指示器的常量。

*   `E2BIG` —参数列表比预期的要长。
*   `EACCES` —该操作没有足够的权限。
*   `EADDRINUSE` —网络地址已被使用。
*   `EADDRNOTAVAIL` —网络地址目前不可用。
*   `EAFNOSUPPORT` —不支持网络地址族。
*   `EAGAIN` —当前没有可用的数据，稍后重试该操作。
*   `EALREADY` —套接字已经有一个正在进行的挂起连接。
*   `EBADF` —文件描述符无效。
*   `EBADMSG` —无效的数据信息。
*   `EBUSY` —设备或资源正忙。
*   `ECANCELED` —一次手术被取消。
*   `ECHILD` —没有子进程。
*   `ECONNABORTED` —网络连接已中止。
*   `ECONNREFUSED` —网络连接被拒绝。
*   `ECONNRESET` —网络连接已重置。
*   `EDEADLK` —避免了资源死锁。
*   `EDESTADDRREQ` —需要目的地地址。
*   `EDOM` —参数超出了函数的范围。
*   `EDQUOT` —已超过磁盘配额。
*   `EEXIST` —文件已经存在。
*   `EFAULT` —无效的指针地址。
*   `EFBIG` —文件太大。
*   `EHOSTUNREACH` —主机不可访问。
*   `EIDRM` —标识符已被删除。
*   `EILSEQ` —遇到非法字节序列。
*   `EINPROGRESS` —操作已经在进行中。
*   `EINTR` —函数调用被中断。
*   `EINVAL` —提供了无效的参数。
*   `EIO` —未指明的 I/O 错误。
*   `EISCONN` —插座已连接。
*   `EISDIR` —路径是一个目录。
*   `ELOOP` —路径中的符号链接级别过多。
*   `EMFILE` —打开的文件太多。
*   `EMLINK` —文件的硬链接太多。
*   `EMSGSIZE` —提供的消息太长。
*   `EMULTIHOP` —尝试了多跳。
*   `ENAMETOOLONG` —文件名太长。
*   `ENETDOWN` —网络中断。
*   `ENETRESET` —网络已中止连接。
*   `ENETUNREACH` —网络不可达。
*   `ENFILE` —系统中打开的文件太多。
*   `ENOBUFS` —没有可用的缓冲空间。
*   `ENODATA` —流头读取队列中没有可用的消息。
*   没有这样的装置。
*   `ENOENT` —没有这样的文件或目录。
*   `ENOEXEC` —一个 exec 格式错误。
*   `ENOLCK` —没有可用的锁。
*   一个链接被切断了。
*   没有足够的空间。
*   `ENOMSG` —没有所需类型的消息。
*   `ENOPROTOOPT` —给定的协议不可用。
*   `ENOSPC` —设备上没有可用空间。
*   `ENOSR` —没有可用的流资源。
*   `ENOSTR` —给定的资源不是流。
*   `ENOSYS` —功能尚未实现。
*   `ENOTCONN` —插座未连接。
*   `ENOTDIR` —路径不是目录。
*   `ENOTEMPTY` —目录不为空。
*   `ENOTSOCK` —给定项目不是套接字。
*   `ENOTSUP` —不支持给定的操作。
*   `ENOTTY` —不适当的 I/O 控制操作。
*   `ENXIO` —没有这样的设备或地址。
*   `EOPNOTSUPP` —不支持套接字上的操作。虽然`ENOTSUP`和`EOPNOTSUPP`在 Linux 上有相同的值，但是根据 POSIX.1，这些错误值应该是不同的。)
*   `EOVERFLOW` —值太大，无法存储在给定的数据类型中。
*   `EPERM` —不允许操作。
*   `EPIPE` —管道破裂。
*   `EPROTO` —协议错误。
*   `EPROTONOSUPPORT` —不支持协议。
*   `EPROTOTYPE` —套接字协议类型错误。
*   `ERANGE` —结果太大。
*   `EROFS` —文件系统是只读的。
*   `ESPIPE` —无效的查找操作。
*   `ESRCH` —没有这个流程。
*   `ESTALE` —文件句柄过时。
*   `ETIME` —定时器到期
*   `ETIMEDOUT` —连接超时。
*   `ETXTBSY` —一个文本文件正忙。
*   `EWOULDBLOCK` —操作将被阻止。
*   `EXDEV` —链接不当。

## Windows 系统的错误常数

以下是 Windows 系统中引发的错误指示器的常量。

*   `WSAEINTR` —一个中断的函数调用。
*   `WSAEBADF` —无效的文件句柄。
*   `WSAEACCES` —权限不足，无法完成操作。
*   `WSAEFAULT` —无效的指针地址。
*   `WSAEINVAL` —传递了一个无效的参数。
*   `WSAEMFILE` —打开的文件太多。
*   `WSAEWOULDBLOCK` —资源暂时不可用。
*   `WSAEINPROGRESS` —当前正在进行一项操作。
*   `WSAEALREADY` —操作已经在进行中。
*   `WSAENOTSOCK` —资源不是套接字。
*   `WSAEDESTADDRREQ` —需要目的地地址。
*   `WSAEMSGSIZE` —消息太长。
*   `WSAEPROTOTYPE` —套接字的错误协议类型。
*   `WSAENOPROTOOPT` —一个错误的协议选项。
*   `WSAEPROTONOSUPPORT` —不支持该协议。
*   `WSAESOCKTNOSUPPORT` —不支持插座类型。
*   `WSAEOPNOTSUPP` —不支持该操作。
*   `WSAEPFNOSUPPORT` —不支持协议族。
*   `WSAEAFNOSUPPORT` —不支持地址族。
*   `WSAEADDRINUSE` —网络地址已被使用。
*   `WSAEADDRNOTAVAIL` —网络地址不可用。
*   `WSAENETDOWN` —网络中断。
*   `WSAENETUNREACH` —网络不可达。
*   `WSAENETRESET` —网络连接已重置。
*   `WSAECONNABORTED` —连接已中止。
*   `WSAECONNRESET` —连接已被对等方重置。
*   `WSAENOBUFS` —没有可用的缓冲空间。
*   `WSAEISCONN` —插座已经连接。
*   `WSAENOTCONN` —插座未连接。
*   `WSAESHUTDOWN` —套接字关闭后无法发送数据。
*   `WSAETOOMANYREFS` —引用太多。
*   `WSAETIMEDOUT` —连接已经超时。
*   `WSAECONNREFUSED` —连接被拒绝。
*   `WSAELOOP` —姓名无法翻译。
*   名字太长了。
*   `WSAEHOSTDOWN` —一台网络主机出现故障。
*   `WSAEHOSTUNREACH` —没有到网络主机的路由。
*   `WSAENOTEMPTY` —目录不为空。
*   `WSAEPROCLIM` —流程太多。
*   `WSAEUSERS` —已超过用户配额。
*   `WSAEDQUOT` —已超过磁盘配额。
*   `WSAESTALE` —遇到过时的文件句柄引用。
*   `WSAEREMOTE` —项目是远程的。
*   `WSASYSNOTREADY` —网络子系统未准备好。
*   `WSAVERNOTSUPPORTED`—`winsock.dll`版本超出范围。
*   `WSANOTINITIALISED` —尚未成功执行 WSAStartup。
*   `WSAEDISCON` —正常关机正在进行中。
*   `WSAENOMORE` —没有更多的结果。
*   `WSAECANCELLED` —操作被取消。
*   `WSAEINVALIDPROCTABLE` —程序调用表无效。
*   `WSAEINVALIDPROVIDER` —无效的服务提供商。
*   `WSAEPROVIDERFAILEDINIT` —服务提供者无法初始化。
*   `WSASYSCALLFAILURE` —系统调用失败。
*   `WSASERVICE_NOT_FOUND` —未找到服务。
*   `WSATYPE_NOT_FOUND` —未找到类别类型。
*   `WSA_E_NO_MORE` —没有更多的结果。
*   `WSA_E_CANCELLED` —通话取消。
*   `WSAEREFUSED` —数据库查询被拒绝。

## dlopen 常量

`dlopen`命令的结果也包含在`os.constants`对象中。`dlopen`命令用于将库动态加载到内存中。

*   `RTLD_LAZY` —执行惰性绑定。Node.js 默认设置这个标志。
*   `RTLD_NOW` —在`dlopen(3)` 返回之前解决库中所有未定义的符号。
*   `RTLD_GLOBAL` —由库定义的符号将可用于后续加载的库的符号解析。
*   `RTLD_LOCAL` —`RTLD_GLOBAL`的反义词。如果未指定`RTLD_GLOBAL`或`RTLD_LOCAL`，这是默认行为。
*   `RTLD_DEEPBIND` —使一个自包含的库优先使用自己的符号，而不是先前加载的库中的符号。

## 优先级常数

以下常量用于设置计划进程的优先级。nice 值是指 Unix 和 Linux 中的`nice`程序使用的 CPU 调度优先级的整数值。但是，有些值在 Windows 中也是相同的。默认值为 0。19 表示最低的 CPU 优先级，而-20 表示最高。

*   `PRIORITY_LOW` —最低的进程调度优先级。这与 Windows 上的`IDLE_PRIORITY_CLASS`相对应，在所有其他平台上这个值是 19。
*   `PRIORITY_BELOW_NORMAL` —这在 Windows 上对应于`BELOW_NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于一个很好的值 10。
*   `PRIORITY_NORMAL` —默认的进程调度优先级。这在 Windows 上对应于`NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于 0。
*   `PRIORITY_ABOVE_NORMAL` —这对应于 Windows 上的`ABOVE_NORMAL_PRIORITY_CLASS`，在所有其他平台上是一个不错的值-7。
*   `PRIORITY_HIGH`——。这对应于 Windows 上的`HIGH_PRIORITY_CLASS`和所有其他平台上的-14。
*   `PRIORITY_HIGHEST` —最高的进程调度优先级。这相当于 Windows 上的`REALTIME_PRIORITY_CLASS`，在所有其他平台上相当于-20。

调度优先级从低到高依次为— `PRIORITY_LOW`、`PRIORITY_BELOW_NORMAL`、`PRIORITY_NORMAL`、`PRIORITY_ABOVE_NORMAL`、`PRIORITY_HIGH`、`PRIORITY_HIGHEST`。

Node.js OS 模块有许多有用的实用函数，用于获取有关运行 OS 模块程序的计算机系统的信息。这些模块有更多的属性，包含有用的信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、当前登录用户的信息等等。