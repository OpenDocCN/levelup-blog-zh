# Node.js FS 模块-符号链接和时间戳

> 原文：<https://levelup.gitconnected.com/node-js-fs-module-symbolic-links-and-timestamps-93a58c9f4d92>

![](img/efd30188a398346954de9b92bf6568eb.png)

[JJ 英](https://unsplash.com/@jjying?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个基本特性。

幸运的是，Node.js 的库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。支持的文件和目录操作包括基本的操作，如操作和打开目录中的文件。同样，它也可以对文件做同样的事情。

它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。

此外，它还可以显示文件的统计数据。几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。

在本文中，我们将用`symlink`函数族创建符号链接，并用`utimes`函数族设置时间戳。

# 创建符号链接

符号链接是以文件的相对或绝对路径的形式引用其他文件的文件。我们可以用`symlink`函数在 Node.js 程序中创建符号链接。

该函数有 4 个参数。

第一个参数是符号链接的目标路径，也就是我们想要在符号链接中引用的文件路径。它可以是字符串、缓冲区对象或 URL 对象的形式。

第二个参数是符号链接的路径，它也可以是字符串、缓冲区对象或 URL 对象的形式。

第三个参数是类型，它是一个字符串。它只在 Windows 上可用，在其他平台上被忽略。可能的值有`'dir'`、`'file'`或`'junction'`。

如果没有设置类型参数，它将自动检测目标的类型并使用`'file'`或`'dir'`。如果目标不存在，则使用`'file'`。Windows 要求符号链接路径是绝对的。使用`'junction'`时，目标参数将被转换为绝对路径。

最后一个参数是在符号链接创建操作结束时调用的回调函数。它有一个`err`参数，当操作成功时为`null`，当操作失败时有一个包含错误信息的对象。

我们可以用`symlink`函数创建一个符号链接，如下所示:

```
const fs = require("fs");
const target = "./files/file.txt";
const path = "./files/symlink";fs.symlink(target, path, "file", err => {
  if (err) {
    throw err;
  }
  console.log("Symbolic link creation complete!");
});
```

运行上面的代码后，当我们在 POSIX 系统上运行`stat ./files/symlink`时，我们应该得到类似如下的输出:

```
File: './files/symlink' -> './files/file.txt'
  Size: 16              Blocks: 0          IO Block: 512    symbolic link
Device: eh/14d  Inode: 62487444831945583  Links: 1
Access: (0777/lrwxrwxrwx)  Uid: ( 1000/hauyeung)   Gid: ( 1000/hauyeung)
Access: 2019-11-03 11:22:19.787359800 -0800
Modify: 2019-11-03 11:22:19.787359800 -0800
Change: 2019-11-03 11:22:19.787359800 -0800
 Birth: -
```

这意味着符号链接已经成功创建。

还有一个同步版本的`symlink`函数，叫做`symlinkSync`函数。它需要三个参数。

第一个参数是符号链接的目标路径，也就是我们想要在符号链接中引用的文件路径。它可以是字符串、缓冲区对象或 URL 对象的形式。

第二个参数是符号链接的路径，它也可以是字符串、缓冲区对象或 URL 对象的形式。第三个参数是类型，它是一个字符串。它只在 Windows 上可用，在其他平台上被忽略。

可能的值有`'dir'`、`'file'`或`'junction'`。如果没有设置类型参数，它将自动检测目标的类型并使用`'file'`或`'dir'`。如果目标不存在，则使用`'file'`。Windows 要求符号链接路径是绝对的。

使用`'junction'`时，目标参数将被转换为绝对路径。它返回`undefined`。

我们可以使用它来创建符号链接，如下面的代码所示:

```
const fs = require("fs");
const target = "./files/file.txt";
const path = "./files/symlink";try {
  fs.symlinkSync(target, path, "file");
  console.log("Symbolic link creation complete!");
} catch (error) {
  console.error(error);
}
```

运行上面的代码后，当我们在 POSIX 系统上运行`stat ./files/symlink`时，我们应该得到与上面相同的输出。

还有一个`symlink`功能的承诺版本。它需要三个参数。

第一个参数是符号链接的目标路径，也就是我们想要在符号链接中引用的文件路径。它可以是字符串、缓冲区对象或 URL 对象的形式。

第二个参数是符号链接的路径，它也可以是字符串、缓冲区对象或 URL 对象的形式。

第三个参数是类型，它是一个字符串。它只在 Windows 上可用，在其他平台上被忽略。

可能的值有`'dir'`、`'file'`或`'junction'`。如果没有设置类型参数，它会自动检测目标的类型并使用`'file'`或`'dir'`。如果目标不存在，则使用`'file'`。

Windows 要求符号链接路径是绝对的。当使用`'junction'`时，目标参数将被转换为绝对路径。当它成功时，它返回一个没有任何争论的承诺。

我们可以使用它来创建符号链接，如下面的代码所示:

```
const fsPromises = require("fs").promises;
const target = "./files/file.txt";
const path = "./files/symlink";(async () => {
  try {
    await fsPromises.symlink(target, path, "file");
    console.log("Symbolic link creation complete!");
  } catch (error) {
    console.error(error);
  }
})();
```

运行上面的代码后，当我们在 POSIX 系统上运行`stat ./files/symlink`时，我们应该得到与上面相同的输出。

当您想连续做多件事(包括调用`symlink`函数)时，promise 版本的`symlink` 函数是比`symlinkSync`函数更好的选择，因为它不会占用整个程序，等待符号链接创建操作完成后再继续编写程序的其他部分。

![](img/580657327fcb2846e354953ff02cbae7.png)

埃里克·维特索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 更改存储在磁盘上的项目的时间戳

我们可以用`utimes`函数更改文件的最后访问时间和最后修改时间的时间戳。

该函数有 4 个参数。

第一个是存储在磁盘上的对象的路径。它可以是字符串、缓冲区对象和 URL 对象。

第二个参数是`atime`，它是对象最后一次被访问的时间。它可以是数字、字符串或日期对象。

如果它是一个数字，那么它应该是 UNIX 时间戳。

如果它是一个字符串，那么它应该是 UNIX 时间戳的字符串形式。

如果该值不能转换成数字或者是`NaN`、`Infinity`或`-Infinity`，那么将会抛出一个错误。

第三个参数是`mtime`，这是对象最后一次被修改的时间。它可以是数字、字符串或日期对象。它应该与`atime`参数的格式相同。

第四个参数是一个接受`err`参数的回调函数。当操作成功时，它是`null`,如果操作失败，它有一个包含错误信息的对象。

我们可以像下面的代码一样使用`utimes`函数:

```
const fs = require("fs");
const path = "./files/file.txt";fs.utimes(
  path,
  new Date(2019, 0, 1, 0, 0, 0, 0),
  new Date(2019, 0, 1, 0, 0, 0, 0),
  err => {
    if (err) {
      throw err;
    }
    console.log("Timestamps changed");
  }
);
```

如果我们运行上面的代码，然后运行`stat ./files/file.txt`，我们应该得到类似下面的输出:

```
File: './files/file.txt'
  Size: 16              Blocks: 0          IO Block: 512    regular file
Device: eh/14d  Inode: 22799473115106242  Links: 1
Access: (0777/-rwxrwxrwx)  Uid: ( 1000/hauyeung)   Gid: ( 1000/hauyeung)
Access: 2019-01-01 00:00:00.000000000 -0800
Modify: 2019-01-01 00:00:00.000000000 -0800
Change: 2019-11-03 11:41:16.155815100 -0800
 Birth: -
```

正如我们所看到的，`Access` 和`Modify` 时间变成了`2019–01–01 00:00:00.000000000`，所以我们知道`utimes`函数已经成功地改变了时间戳。

有一个同步版本的`utimes`函数叫做`utimesSync`函数。

该函数有 3 个参数。

第一个是存储在磁盘上的对象的路径。它可以是字符串、缓冲区对象和 URL 对象。第二个参数是`atime`，它是对象最后一次被访问的时间。

它可以是数字、字符串或日期对象。如果它是一个数字，那么它应该是 UNIX 时间戳。如果它是一个字符串，那么它应该是 UNIX 时间戳的字符串形式。如果该值不能转换成数字或者是`NaN`、`Infinity`或`-Infinity`，那么将会抛出一个错误。

第三个参数是`mtime`，这是对象最后一次被修改的时间。它可以是数字、字符串或日期对象。它应该与`atime`参数的格式相同。

我们可以在下面的代码中使用它:

```
const fs = require("fs");
const path = "./files/file.txt";try {
  fs.utimesSync(
    path,
    new Date(2019, 0, 1, 0, 0, 0, 0),
    new Date(2019, 0, 1, 0, 0, 0, 0)
  );
  console.log("Timestamps changed");
} catch (error) {
  console.error(error);
}
```

如果我们运行上面的代码，然后运行`stat ./files/file.txt`，我们应该得到与上面相同的输出。

还有一个`utimes`函数的承诺版本，它让我们异步运行`utimes`，同时像处理`utimesSync`函数一样按顺序运行它。

该函数有 3 个参数。第一个是存储在磁盘上的对象的路径。它可以是字符串、缓冲区对象和 URL 对象。

第二个参数是`atime`，它是对象最后一次被访问的时间。它可以是数字、字符串或日期对象。如果它是一个数字，那么它应该是 UNIX 时间戳。

如果它是一个字符串，那么它应该是 UNIX 时间戳的字符串形式。如果该值不能转换成数字或者是`NaN`、`Infinity`或`-Infinity`，那么将会抛出一个错误。

第三个参数是`mtime`，它是对象最后一次被修改的时间。它可以是数字、字符串或日期对象。它应该与`atime`参数的格式相同。

我们可以在下面的代码中使用它:

```
const fsPromises = require("fs").promises;
const path = "./files/file.txt";(async () => {
  try {
    await fsPromises.utimes(
      path,
      new Date(2019, 0, 1, 0, 0, 0, 0),
      new Date(2019, 0, 1, 0, 0, 0, 0)
    );
    console.log("Timestamps changed");
  } catch (error) {
    console.error(error);
  }
})();
```

如果我们运行上面的代码，然后运行`stat ./files/file.txt`，我们也应该得到与常规`utimes`示例相同的输出。

我们可以用`symlink`函数族创建符号链接。

它需要三个参数。第一个是存储在磁盘上的对象的路径。它可以是字符串、缓冲区对象和 URL 对象。

第二个参数是`atime`，它是对象最后一次被访问的时间。它可以是数字、字符串或日期对象。如果是数字，那么它应该是 UNIX 时间戳，如果是字符串，那么它应该是 UNIX 时间戳的字符串形式。

常规的异步版本也有一个回调函数，当它结束时运行。`utimes`函数改变存储在磁盘上的对象的访问时间戳和修改时间。它采用实体的路径来应用访问和修改时间的改变和时间戳。

常规的异步版本也有一个回调函数，当它结束时运行。当我们需要在 Node.js 程序中执行这些操作时，它们非常方便。