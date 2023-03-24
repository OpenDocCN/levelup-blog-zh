# 使用 Node.js 操作系统模块(第 3 部分)

> 原文：<https://levelup.gitconnected.com/using-the-node-js-os-module-part-3-e3f04529edbb>

![](img/73543f97d2c0932f2d17d33bce2aec69.png)

布鲁斯·马尔斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Node.js OS 模块有许多有用的实用函数，用于获取有关运行 OS 模块程序的计算机系统的信息。它可以为我们提供硬件信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、当前登录用户的信息等等。

我们可以通过在 JS 文件的顶部写`const os = require('os');`来使用 OS 模块。OS 模块中有许多有用的属性。以下是操作系统模块中更有用的属性:

# 操作系统版本

`os.release`函数返回一个标识操作系统版本的字符串。在 Unix 和 Linux 系统上，该函数使用`uname`命令识别操作系统。在 Windows 上，使用 Win32 API 中的`GetVersionExW()`函数。例如，如果我们运行:

```
console.log(os.release());
```

那么我们可能会得到这样的结果:

```
'4.15.0-1036-gcp'
```

# os.setPriority

`os.setPriority`函数让我们设置由进程 ID 指定的进程的调度优先级。它需要两个参数。第一个是进程 ID，它是一个整数，标识要设置优先级的正在运行的进程。默认值为 0。

第二个参数是优先级，它是一个整数，表示我们想要设置的进程优先级的调度。它的范围从最高优先级的-20 到最低优先级的 19。

由于 Windows 和 POSIX 系统之间优先级的差异，Node.js 为我们提供了具有进程优先级的常量。

它们在`os.constants.priority`常量中。在 Windows 上，将进程优先级设置为`PRIORITY_HIGHEST`需要提升权限。如果运行该程序的用户没有提升的特权，那么它将被默认为`PRIORITY_HIGH`。

Node.js 提供了以下优先级常量:

*   `PRIORITY_LOW` —最低的进程调度优先级。这对应于 Windows 上的`IDLE_PRIORITY_CLASS`和所有其他平台上的 19。
*   `PRIORITY_BELOW_NORMAL` —这在 Windows 上对应于`BELOW_NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于 10。
*   `PRIORITY_NORMAL` —默认的进程调度优先级。这对应于 Windows 上的`NORMAL_PRIORITY_CLASS`,在所有其他平台上的值为 0。
*   `PRIORITY_ABOVE_NORMAL` —这在 Windows 上对应于`ABOVE_NORMAL_PRIORITY_CLASS`，在所有其他平台上对应于-7。
*   `PRIORITY_HIGH` —这对应于 Windows 上的`HIGH_PRIORITY_CLASS`，在所有其他平台上是一个不错的值-14。
*   `PRIORITY_HIGHEST` —最高的进程调度优先级。这对应于 Windows 上的`REALTIME_PRIORITY_CLASS`和所有其他平台上的-20。

例如，我们可以在下面的代码中使用它:

```
os.setPriority(0, os.constants.priority.PRIORITY_LOW)
```

# os.tmpdir

`os.tmpdir`函数返回一个字符串，该字符串指定操作系统存储临时文件的默认目录。例如，如果我们写:

```
console.log(os.tmpdir());
```

那么我们可能会得到:

```
'/tmp'
```

在 Linux 系统上。

# os.totalmem

`os.totalmem`函数返回系统内存总量，以整数形式表示为字节数。例如，如果我们运行:

```
console.log(os.totalmem());
```

那么我们可能会得到这样的结果:

```
27389460480
```

# os.type

`os.type`函数返回一个字符串，该字符串标识由`uname`命令返回的操作系统。比如 Linux 上可能是`'Linux'`，macOS 上可能是`'Darwin'`，Windows 上可能是`'Windows_NT'`。例如，如果我们运行以下代码:

```
console.log(os.type());
```

在 Linux 系统上，我们得到`‘Linux’`。

# os.uptime

`os.uptime`函数返回一个以秒为单位的系统正常运行时间的整数。例如，如果我们运行:

```
console.log(os.uptime());
```

那么我们可能会得到这样的结果:

```
4928
```

![](img/71a972fbed661d65ad265bab9a7cc3ba.png)

安娜·塔瓦雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 操作系统.用户信息

`os.userInfo`函数返回一个对象，该对象包含当前有效用户的信息，即假定用户的用户身份。

这可能与当前登录的用户不同，因为用户可以在多用户操作系统中以不同用户的身份运行程序。在 POSIX 系统上，这通常是密码文件的子集。

返回的对象包括`username`、`uid`、`gid`、`shell`和`homedir`属性。在 Windows 上，`uid`和`gid`为-1，`shell`为`null`。

如果用户没有`username`或`homedir`，将抛出`SystemError`。`os.userInfo`函数返回的`homedir`值由操作系统提供。

这与`os.homedir`函数不同，后者从各种环境变量中返回用户主目录的路径字符串。在 Unix 和 Linux 系统上，如果定义了`$HOME`变量，它将使用它。

否则，它将通过有效的 UID 查找主目录路径，该 UID 是您当前假设的用户身份的用户 ID。例如，如果您使用`sudo`作为根用户使用计算机，那么有效用户 ID 将是根用户的用户 ID。

在 Windows 上，如果定义了`USERPROFILE`的值，将会使用它。否则，它将是当前用户的配置文件目录的路径。

它有一个参数，这个对象让我们设置调用函数的选项。它可以有一个属性，即`encoding`属性，这是一个字符串，指示用于解释结果字符串的字符编码。

默认值是`'utf8'`，但也可以是`'buffer'`，将返回对象的属性值设置为缓冲区对象。该参数是可选的。

例如，如果我们运行以下代码:

```
console.log(os.userInfo({ option: 'utf8' }));
```

我们可能会得到类似如下的结果:

```
{ uid: 1000,
  gid: 1000,
  username: 'runner',
  homedir: '/home/runner',
  shell: '/bin/bash' }
```

在上面的输出中，`uid`是用户标识符，它标识了当前登录用户所采用的用户身份，如果它以自己的身份运行程序，它可能是当前登录的用户，或者如果 Node.js 程序当前以 root 身份运行，它可能是不同的用户，如 root 用户。这也称为当前有效用户。

`gid`是组 ID。它是将一个用户与其他有共同之处的用户联系起来的标识符。一个用户可以是一个或多个组的成员，并且可以有多个 GID。

`username`是当前有效用户的用户名。`homedir`是当前有效用户的主目录。`shell`是当前有效用户正在使用的命令行解释程序。

Node.js OS 模块有许多有用的实用函数，用于获取有关运行 OS 模块程序的计算机系统的信息。

这些模块有更多的属性，包含有用的信息，如 CPU、字节序、主目录、IP 地址、主机名、程序运行的平台、系统正常运行时间、当前登录用户的信息等等。它还让我们设置运行进程的优先级。