# 使用 Node.js 操作系统模块(第 2 部分)

> 原文：<https://levelup.gitconnected.com/using-the-node-js-os-module-part-2-9d6a793cc302>

![](img/0a7db8fe761c3af747da0e16545f20d1.png)

[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Node.js OS 模块有许多有用的实用函数，用于获取运行该 OS 模块程序的计算机系统的信息。它可以提供关于硬件的信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、关于当前登录用户的信息等等。

我们可以通过在代码顶部写`const os = require('os');`来使用 OS 模块。OS 模块中有许多有用的属性。以下是操作系统模块中更有用的属性:

# os.cpus

`os.cpus()`函数返回一个对象数组，该数组包含关于主机 CPU 的每个逻辑核心的各种信息。每个条目都有一些属性。有一个`model`属性，它是一个表示计算机 CPU 型号的字符串。`speed`属性是一个以兆赫为单位的数字属性。`times`属性是一个具有以下属性的对象:

*   `user` —一个数字属性，表示 CPU 在用户模式下花费的毫秒数。
*   `nice` —一个数字属性，指示 CPU 在 nice 模式下花费的毫秒数。Nice 模式是指 CPU 运行具有正 nice 值的进程，这意味着一个优先级较低的进程。
*   `sys` —一个数字属性，表示 CPU 在 sys 模式下花费的毫秒数。
*   `idle` —一个数字属性，表示 CPU 处于空闲模式的毫秒数。当 CPU 不被任何程序使用时，它就是空闲的。
*   `irq` —一个数字属性，表示 CPU 在 IRQ 模式下花费的毫秒数。IRQ 是一个硬件信号，它使 CPU 暂时停止一个正在运行的程序，而允许一个中断处理程序运行。

例如，我们可以像下面这样使用函数:

```
console.log(os.cpus());
```

我们可以得到类似如下的输出:

```
[ { model: 'Intel(R) Xeon(R) CPU @ 2.30GHz',
    speed: 2300,
    times:
     { user: 3367100, nice: 0, sys: 757800, idle: 9833900, irq: 0 } },
  { model: 'Intel(R) Xeon(R) CPU @ 2.30GHz',
    speed: 2300,
    times:
     { user: 3387000, nice: 0, sys: 730100, idle: 10054300, irq: 0 } },
  { model: 'Intel(R) Xeon(R) CPU @ 2.30GHz',
    speed: 2300,
    times:
     { user: 3259600, nice: 0, sys: 748300, idle: 10168800, irq: 0 } },
  { model: 'Intel(R) Xeon(R) CPU @ 2.30GHz',
    speed: 2300,
    times:
     { user: 3229700, nice: 0, sys: 755800, idle: 10195600, irq: 0 } } ]
```

# 操作系统端序

`os.endianness()`函数返回一个字符串，标识编译 Node.js 运行时二进制文件的 CPU 的字节顺序。两个可能的值是:

*   `'BE'` —大端。大端 CPU 顺序首先放置最高有效字节，最后放置最低有效字节。
*   `'LE'` —小端序，大端序的反义词。

如果我们对`os.endianness()`的返回值运行`console.log`，我们可能会得到如下结果:

```
'LE'
```

# os.freemem

`os.freemem()`函数返回一个整数，以字节数表示空闲内存的数量。如果我们对`os.freemem()`的输出运行`console.log`，我们会得到如下结果:

```
15338930176
```

# os.getPriority

`os.getPriority`函数返回一个整数，表示 PID 指定的进程的调度优先级。它有一个参数，即作为整数传入的进程 ID。如果没有提供 PID 或者 PID 为 0，那么返回当前进程的优先级。例如，如果我们写:

```
os.getPriority()
```

然后我们得到一个数字。19 表示最低的 CPU 优先级，而-20 表示最高。Node.js 中的处理器优先级由以下常量定义:

*   `PRIORITY_LOW` —最低的进程调度优先级。这对应于 Windows 上的`IDLE_PRIORITY_CLASS`和所有其他平台上的 19。
*   `PRIORITY_BELOW_NORMAL` —这在 Windows 上对应于`BELOW_NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于 10。
*   `PRIORITY_NORMAL` —默认进程调度优先级。这对应于 Windows 上的`NORMAL_PRIORITY_CLASS`和所有其他平台上的 0 值。
*   `PRIORITY_ABOVE_NORMAL` —这在 Windows 上对应于`ABOVE_NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于-7。
*   `PRIORITY_HIGH`——。这对应于 Windows 上的`HIGH_PRIORITY_CLASS`和所有其他平台上的-14。
*   `PRIORITY_HIGHEST` —最高的进程调度优先级。这对应于 Windows 上的`REALTIME_PRIORITY_CLASS`和所有其他平台上的-20。

# os.homedir

`os.homedir`函数返回用户主目录的路径字符串。在 Unix 和 Linux 系统上，如果定义了`$HOME`变量，它将使用它。否则，它将通过有效的 UID 查找主目录路径，该 UID 是您当前假设的用户身份的用户 ID。例如，如果您使用`sudo`作为根用户使用计算机，那么有效的用户 ID 将是根用户的用户 ID。在 Windows 上，如果定义了`USERPROFILE`的值，就会使用它。否则，它将是当前用户的配置文件目录的路径。例如，如果我们对`os.homedir()`的输出运行`console.log`，那么我们可能会得到如下结果:

```
'/home/runner'
```

如果你用的是 Linux 系统。

# 操作系统.主机名

`os.hostname`函数以字符串形式返回操作系统的主机名。例如，如果我们在`os.hostname()`的返回值上调用`console.log`，那么我们可能会得到类似于`‘5b84600c80eb’`的东西。

# os.loadavg

`os.loadavg`函数返回一个包含 1、5 和 15 分钟平均负载的数字数组。平均负载衡量系统活动，由操作系统计算并以十进制数表示。理想的平均负载应该少于系统中逻辑 CPU 的数量。该功能仅在 Unix 和 Linux 系统上有效。在 Windows 上，它总是返回`[0,0,0]`。例如，如果我们在 Linux 系统上运行这个函数，如下面的代码所示:

```
console.log(os.loadavg())
```

我们可能会得到这样的结果:

```
[ 12.60791015625, 13.3916015625, 9.8798828125 ]
```

![](img/9198e6e78299893502e8588596af370e.png)

托马斯·詹森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 操作系统.网络接口

`os.networkInterfaces`函数返回一个对象，该对象的网络接口已经被分配了一个网络地址。返回的对象上的关键字标识网络接口。密钥的相应值是描述分配的网络地址的对象数组。分配的网络地址对象的属性包括:

*   `address` —具有分配的 IPv4 或 IPv6 地址的字符串
*   `netmask` —具有 IPv4 或 IPv6 网络掩码的字符串
*   `family` —有两个可能值的字符串:`IPv4`或`IPv6`
*   `mac` —包含网络接口的 MAC 地址的字符串
*   `internal` —一个布尔值，如果网络接口是回环或不可远程访问的类似接口，则该值为`true`。否则就是`false`
*   `scopeid` —具有 IPv6 作用域 ID 的号码，仅当`family`为`IPv6`时适用
*   `cidr` —具有分配的 IPv4 或 IPv6 地址的字符串，路由前缀采用 CIDR 表示法。CIDR 符号有两组数字。首先是一组位，即网络地址。第一组后面跟着一个斜杠。第二组是被认为对网络路由很重要的位数。例如，如果我们有一个 IP 地址`192.168.0.15/24`，那么`192.168.0.15`是网络地址，而`24`是用于网络路由目的的有效位数。如果`netmask`无效，该属性设置为`null`。

例如，如果我们运行:

```
console.log(os.networkInterfaces());
```

那么我们可能会得到这样的结果:

```
{ lo:
   [ { address: '127.0.0.1',
       netmask: '255.0.0.0',
       family: 'IPv4',
       mac: '00:00:00:00:00:00',
       internal: true,
       cidr: '127.0.0.1/8' } ],
  eth0:
   [ { address: '172.18.0.103',
       netmask: '255.255.0.0',
       family: 'IPv4',
       mac: '02:42:ac:12:00:67',
       internal: false,
       cidr: '172.18.0.103/16' } ] }
```

我们将`lo`用于环回地址，将`eth0`用于以太网接口。

# 操作系统平台

`os.platform`函数返回一个字符串，标识编译 Node.js 二进制文件的计算机的操作系统平台。当前可能的值有`'aix'`、`'darwin'`、`'freebsd'`、`'linux', 'openbsd'`、`'sunos'`或`'win32'`。和`process.platform`属性一样。如果 Node.js 二进制文件构建在 Android 设备上，可能会返回值`'android'`。然而，Node.js 中的 Android 支持仍处于试验阶段。例如，如果我们运行:

```
console.log(os.platform());
```

那么我们可能会得到这样的结果:

```
'linux'
```

Node.js OS 模块有许多有用的实用函数，用于获取有关运行 OS 模块程序的计算机系统的信息。这些模块有更多的属性，包含有用的信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、当前登录用户的信息等等。