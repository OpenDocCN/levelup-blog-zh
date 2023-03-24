# Node.js FS 模块-打开文件

> 原文：<https://levelup.gitconnected.com/node-js-fs-module-opening-files-f4388a459001>

![](img/95cdfb04ceccab7d30e71448cce3c0f2.png)

维克多·塔拉舒克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

操作文件是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个基本特性。幸运的是，Node.js 的库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。

支持的文件和文件夹操作包括基本的操作，如操作和打开目录中的文件。同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。此外，它还可以显示文件的统计数据。

几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。在本文中，我们将介绍`fs`模块，以及如何构造可用于打开文件的路径和用它打开文件。

我们还将尝试使用`fs` promises API 来执行常规`fs`模块中存在的等效操作，如果它们存在于`fs` promises API 中的话。`fs` promise API 函数返回承诺，因此这让我们更容易地顺序运行异步操作。

要使用`fs`模块，我们只需像下面这行代码那样要求它:

```
const fs = require('fs');
```

# 异步操作

文件函数的异步版本接受一个回调，该回调包含一个错误和操作结果作为参数。如果操作成功完成，那么`null`或`undefined`将被传入错误参数，这应该是为每个函数传入的回调函数的第一个参数。异步操作不是按顺序执行的。例如，如果我们想删除一个名为`file`的文件，我们可以写:

```
const fs = require('fs');

fs.unlink('/file', (err) => {
  if (err) throw err;
  console.log('successfully deleted file');
});
```

我们有`err`参数，如果它存在就有错误数据，如果发生错误就有错误数据。异步操作不是按顺序完成的，要按顺序完成多个操作，我们可以将它转换为一个承诺，或者将操作嵌套在前面操作的回调函数中。例如，如果我们要重命名一个文件，然后检查该文件是否存在，我们可以不应该编写以下内容:

```
fs.rename('/file1', '/file2', (err) => {
  if (err) throw err;
  console.log('renamed complete');
});
fs.stat('/file1', (err, stats) => {
  if (err) throw err;
  console.log(stats);
});
```

因为它们不能保证按顺序运行。因此，我们应该改为写:

```
fs.rename('./file1', './file2', (err) => {
  if (err) throw err;
  console.log('renamed complete'); fs.stat('./file2', (err, stats) => {
    if (err) throw err;
    console.log(stats);
  })
;});
```

然而，如果我们想顺序地做很多操作，这就变得很麻烦了。如果我们有多个文件操作，我们有太多的回调函数嵌套。过多的回调函数嵌套被称为回调地狱，它使得阅读和调试代码变得非常困难和混乱。因此，我们应该使用类似承诺的东西来执行异步操作。例如，我们应该用下面的代码重写上面的例子:

```
const fsPromises = require("fs").promises;(async () => {
  try {
    await fsPromises.rename("./files/file1.txt", "./files/file2.txt");
    const stats = await fsPromises.stat("./files/file2.txt");
    console.log(stats);
  } catch (error) {
    console.log(error);
  }
})();
```

我们使用了`fs` promises API，它具有返回承诺的文件操作函数。这要干净得多，并且利用了`async`和`await`链接承诺的语法。注意，`fs` promises API 有一个警告，说它是实验性的。

然而，到目前为止，它非常稳定，对于需要链接多个文件操作的基本文件操作来说，它很好。注意，我们正在用`try...catch`块捕捉错误。`async`函数看起来像同步函数，但只能返回承诺。

上面的每个示例都将输出如下内容:

```
Stats {
  dev: 3605029386,
  mode: 33206,
  nlink: 1,
  uid: 0,
  gid: 0,
  rdev: 0,
  blksize: undefined,
  ino: 6192449489177455,
  size: 0,
  blocks: undefined,
  atimeMs: 1572568634188,
  mtimeMs: 1572568838068,
  ctimeMs: 1572569087450.1968,
  birthtimeMs: 1572568634187.734,
  atime: 2019-11-01T00:37:14.188Z,
  mtime: 2019-11-01T00:40:38.068Z,
  ctime: 2019-11-01T00:44:47.450Z,
  birthtime: 2019-11-01T00:37:14.188Z }
```

# 同步操作

同步文件操作通常在名字的末尾有单词`Sync`并且被逐行调用。我们得到了`try...catch`块的错误。如果我们把上面的例子重写为一个同步操作，我们可以写成:

```
const fs = require('fs');

try {
  fs.unlinkSync('./files/file1.txt');
  console.log('successfully deleted ./files/file1.txt');
} catch (err) {
  console.err(err);
}
```

我们得到与`catch`子句绑定的`err`来获得错误数据。Node.js 中同步操作的问题是，它会在进程完成之前一直占用处理器，这使得长时间的资源密集型操作会挂起计算机。因此，它降低了程序的执行速度。

![](img/21c46bd802cb4a307abbebf614f23888.png)

[Lili Popper](https://unsplash.com/@lili_popper?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 文件路径

大多数文件操作函数使用`file:`协议接受字符串、缓冲区或 URL 对象形式的路径。字符串路径被解释为 UTF-8 字符序列，用于标识文件或文件夹的绝对或相对路径。

相对路径是相对于当前工作目录解析的，当前工作目录是由`process.cwd()`函数返回的。例如，我们可以用相对路径打开一个文件，如下面的代码所示:

```
const fs = require("fs");fs.open("./files/file.txt", "r", (err, fd) => {
  if (err) throw err;
  fs.close(fd, err => {
    if (err) throw err;
  });
});
```

第一个斜杠前面的点表示相对路径的当前工作目录。或者使用 promise API，我们可以将其重写为以下代码:

```
(async () => {
  try {
    const fileHandle = await fsPromises.open("./files/file.txt", "r");
    console.log(fileHandle);
  } catch (error) {
    console.log(error);
  }
})();
```

注意，promise API 没有`close`函数。常规的`fs` API 将文件操作许可标志作为第二个参数。`r`代表只读。

`fs` promise API 有一个`open`函数，你可以得到`fd`对象，它代表文件描述符，是对我们打开的文件的引用。我们可以通过传递文件描述符`fd`来调用`close`函数。

`fs` promise API 没有这个，所以文件不能用 promise API 关闭。

`fs` API 也接受一个 URL 对象作为文件位置的引用。它必须有`file:`协议，并且它们必须是绝对路径。例如，我们可以创建一个 URL 对象，并将其传递给`read`函数来读取文件。为此，我们可以编写以下代码:

```
const fs = require("fs");
const fileUrl = new URL(`file://${__dirname}/files/file.txt`);fs.open(fileUrl, "r", (err, fd) => {
  if (err) throw err;
  fs.close(fd, err => {
    if (err) throw err;
  });
});
```

它做的事情和使用相对路径完全一样。只是稍微复杂一点，因为我们必须获得 URL 对象，并用`__dirname`对象获得代码的当前目录。在 Windows 上，任何带有主机名的路径都被解释为 UNC 路径，UNC 路径是通过局域网或互联网访问文件的路径。例如，如果我们有以下内容:

```
fs.readFileSync(new URL('file://hostname/path/to/file'));
```

那么这将被解释为使用主机名`hostname`访问服务器上的文件。

在 Windows 上，任何包含驱动器号的文件 URL 都将被解释为绝对路径。例如，如果我们有以下路径:

```
fs.readFileSync(new URL('file://c:/path/to/file'));
```

然后它会尝试访问`c:\path\to\file`路径中的文件。任何没有主机名的文件都必须有驱动器号。因此，只有与上述格式相同的文件路径才是 Windows 上 URL 对象的有效文件路径。以驱动器号开头的路径必须在驱动器号后有一个冒号。在所有其他平台上，`file:`带主机名的 URL 不受支持，像`fs.readFileSync(new URL('file://hostname/path/to/file'));`这样的文件路径会抛出错误。

任何带有转义斜杠字符的`file:` URL 都将在所有平台上抛出错误，因此下面的任何例子都将是无效的并抛出错误。在 Windows 上，这些示例会引发错误:

```
fs.readFileSync(new URL('file:///C:/path/%2F'));
fs.readFileSync(new URL('file:///C:/path/%2f'));
```

在 POSIX 系统上，这些会失败:

```
fs.readFileSync(new URL('file:///pathh/%2F'));
fs.readFileSync(new URL('file:///path/%2f'));
```

在 Windows 上，带有编码反斜杠字符的文件 URL 将引发错误，因此以下示例无效:

```
fs.readFileSync(new URL('file:///D:/path/%5C'));
fs.readFileSync(new URL('file:///D:/path/%5c'));
```

在 POSIX 系统上，内核为每个进程维护一个打开的文件和资源列表。每个打开的文件都被分配一个简单的数字标识符，称为文件描述符。

操作系统使用文件描述来识别和跟踪每个特定的文件。在 Windows 上，跟踪文件使用类似的机制，文件描述符仍然用于跟踪由各种进程打开的文件和资源。

Node.js 负责将文件描述符分配给资源，因此我们不必手动完成这项工作。这对于清理打开的资源很方便。

在 Node.js 中，`fs.open()`函数用于打开文件，并为打开的文件分配一个新的文件描述符。处理完成后，可以通过`close`函数关闭它，以便关闭和清理开放的资源。这可以像下面的代码一样使用:

```
const fs = require("fs");fs.open("./files/file.txt", "r", (err, fd) => {
  if (err) throw err;
  console.log(fd);
  fs.fstat(fd, (err, stat) => {
    if (err) throw err;
    console.log("Stat", stat); fs.close(fd, err => {
      if (err) throw err;
      console.log('Closed');
    });
  });
});
```

如果我们运行上面的代码，我们会得到类似如下的输出:

```
3
Stat Stats {
  dev: 3605029386,
  mode: 33206,
  nlink: 1,
  uid: 0,
  gid: 0,
  rdev: 0,
  blksize: undefined,
  ino: 22799473115106240,
  size: 0,
  blocks: undefined,
  atimeMs: 1572569358035.625,
  mtimeMs: 1572569358035.625,
  ctimeMs: 1572569358035.625,
  birthtimeMs: 1572569358035.625,
  atime: 2019-11-01T00:49:18.036Z,
  mtime: 2019-11-01T00:49:18.036Z,
  ctime: 2019-11-01T00:49:18.036Z,
  birthtime: 2019-11-01T00:49:18.036Z }
Closed
```

在上面的代码中，我们用`open`函数打开了文件，该函数在回调函数中提供了包含文件描述符的`fd`对象，我们可以用它来用`fstat`函数获取文件的信息。完成后，我们可以用`close`函数关闭打开的文件资源。

我们完成了文件打开操作，读取了文件元数据，然后用`close`函数关闭了文件。

promise API 没有`fstat`或`close`函数，所以我们不能用它做同样的事情。

Node.js 运行时平台的标准库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。

支持的文件和文件夹操作包括基本的操作，如操作和打开目录中的文件。

同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。

此外，它还可以显示文件的统计数据。几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。在本文中，我们将介绍`fs`模块，以及如何构造可用于打开文件的路径，并使用它打开文件。

我们还将尝试使用`fs` promises API 来执行常规`fs`模块中存在的等效操作，如果它们存在于`fs` promises API 中的话。

`fs` promise API 函数返回承诺，因此这让我们更容易地顺序运行异步操作。我们仅仅触及了表面，所以请继续关注本系列的第 2 部分。