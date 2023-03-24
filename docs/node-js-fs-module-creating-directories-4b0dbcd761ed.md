# Node.js FS 模块-创建目录

> 原文：<https://levelup.gitconnected.com/node-js-fs-module-creating-directories-4b0dbcd761ed>

![](img/27fe96c6a855e373af2452fc708ba7e5.png)

由 [Alexandru Rotariu](https://unsplash.com/@rotalex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个核心特性。

幸运的是，Node.js 的库中内置了`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。支持的文件和目录操作包括基本的操作，如操作和打开目录中的文件。

同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。

此外，它还可以显示文件的统计数据。几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。在本文中，我们将使用`mkdir`和`mkdtemp`系列函数创建目录，分别创建正常目录和临时目录。

# 用 fs.mkdir 系列函数创建永久目录

为了创建永久目录，我们可以使用`mkdir`函数来异步创建它们。它需要三个参数。第一个参数是路径对象，可以是字符串、缓冲区对象或 URL 对象。

第二个参数是一个具有各种属性的对象，我们可以将这些属性设置为选项。

`recursive`属性是一个布尔属性，如果不是所有的级别都存在，它允许我们创建目录的所有级别。`recursive`属性的默认值是`false`。

属性是一个八进制数属性，我们为 POSIX 系统设置了目录的权限和粘性位。Windows 不支持此选项。

`mode`的默认值是`0o777`，对于每个人都是可读、可写、可执行的。

`mode`整数也可以代替 options 对象作为第二个参数。第三个参数是一个回调函数，它在目录创建操作完成时被调用。

该函数接受一个`err`参数，当操作成功时该参数为`null`，否则该函数有一个包含错误信息的对象。

当路径是一个存在的目录时调用`mkdir`只会在`recursive`选项设置为`false`时导致错误。

我们可以像下面的代码一样使用`mkdir`函数:

```
const fs = require("fs");
const dirToCreate = "./files/createdFolder/createdFolder";fs.mkdir(
  dirToCreate,
  {
    recursive: true,
    mode: 0o77
  },
  err => {
    if (err) {
      throw err;
    }
    console.log("Directory created!");
  }
);
```

如果我们运行上面的代码，我们可以看到，自从我们将`recursive`设置为`true`以来，它为最低级别的目录创建了所有的父目录。

`mkdir`功能的同步版本是`mkdirSync`功能。它需要两个参数。第一个参数是路径对象，可以是字符串、缓冲区对象或 URL 对象。

第二个参数是一个具有各种属性的对象，我们可以将这些属性设置为选项。`recursive`属性是一个布尔属性，如果不是所有的级别都存在，它允许我们创建目录的所有级别。`recursive`属性的默认值是`false`。

`mode`属性是一个八进制数字属性，我们为 POSIX 系统设置了目录的权限和粘性位。

Windows 不支持此选项。`mode`的默认值是`0o777`，对于每个人都是可读、可写、可执行的。`mode`整数也可以代替 options 对象作为第二个参数。该函数返回`undefined`。

当路径是存在的目录时调用`mkdir`只会在`recursive`选项设置为`false`时导致错误。

我们可以像下面的代码一样使用`mkdirSync`函数:

```
const fs = require("fs");
const dirToCreate = "./files/createdFolder/createdFolder";fs.mkdirSync(dirToCreate, {
  recursive: true,
  mode: 0o77
});
console.log("Directory created!");
```

如果我们运行上面的代码，我们可以看到，自从我们将`recursive`设置为`true`以来，它为最低级别的目录创建了所有的父目录。

还有一个`mkdir`功能的承诺版本。。它需要两个参数。第一个参数是路径对象，可以是字符串、缓冲区对象或 URL 对象。

第二个参数是一个具有各种属性的对象，我们可以将这些属性设置为选项。属性是一个布尔属性，如果不是所有的级别都存在，我们可以创建目录的所有级别。`recursive`属性的默认值为`false`。

属性是一个八进制数字属性，我们为 POSIX 系统设置了目录的权限和粘性位。Windows 不支持此选项。

`mode`的默认值是`0o777`，对于每个人来说都是可读、可写和可执行的。`mode`整数也可以代替 options 对象作为第二个参数。

该函数返回`undefined`。当路径是一个存在的目录时调用`mkdir`,只有当`recursive`选项设置为`false`时才会导致承诺拒绝。

当目录创建操作成功时，该函数返回一个解析时不带任何参数的承诺。

我们可以使用`mkdir`函数的 promise 版本，如以下代码所示:

```
const fsPromises = require("fs").promises;
const dirToCreate = "./files/createdFolder/createdFolder";(async () => {
  await fsPromises.mkdir(dirToCreate, {
    recursive: true,
    mode: 0o77
  });
  console.log("Directory created!");
})();
```

如果我们运行上面的代码，我们可以看到，自从我们将`recursive`设置为`true`以来，它为最低级别的目录创建了所有的父目录。这是一个比使用`mkdirSync`顺序创建目录更好的选择，因为它不会像同步版本那样在运行目录创建操作时占用整个程序。

![](img/1e39055462c45b28fa1078f162a92588.png)

照片由 [ipet photo](https://unsplash.com/@ipet_photo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 使用 fs.mkdtemp 系列函数创建临时目录

Node.js `fs`模块有创建临时目录的特殊函数。`mkdtemp`系列函数允许我们通过一次函数调用来实现这一点。

`mkdtemp`函数有 3 个参数。第一个参数是临时文件夹的前缀，它是一个字符串。它是您想要创建的文件夹的路径，该路径后面会附加一些字符。第二个参数是选项字符串或对象。

options 对象由`encoding`属性组成，该属性是文件夹名称的字符编码，默认为`utf8`。

编码值字符串可以替换对象作为第二个参数。第二个参数是可选的。

第三个参数是在临时目录创建函数结束时调用的回调函数。

回调函数有两个参数。第一个是`err`对象，当操作成功时为`null`。第二个参数是文件夹路径，它是一个字符串。

生成的文件夹的文件夹名称将带有前缀，前缀后面会附加 6 个随机字符。在某些系统中，如 BSD 系统，它可以在前缀后返回 6 个以上的随机字符。

如果我们想在`/tmp`中创建一个临时目录，那么前缀必须以特定于平台的路径分隔符结尾，我们可以从`require(‘path’).sep`中获得这个分隔符。

我们可以用下面的代码创建一个临时目录:

```
const fs = require("fs");
const prefix = "./files/tempDir";fs.mkdtemp(
  prefix,
  {
    encoding: "utf8"
  },
  (err, folder) => {
    if (err) {
      throw err;
    }
    console.log("Temp directory created!", folder);
  }
);
```

如果我们运行代码，我们会在`console.log`中记录文件夹路径，我们应该会看到在给定的路径中创建了一个新的临时文件夹。

`mkdtemp`函数有一个名为`mkdtempSync`函数的同步对应函数。`mkdtemp`函数有两个参数。第一个参数是临时文件夹的前缀，它是一个字符串。

它是您想要创建的文件夹的路径，该路径后面会附加一些字符。

第二个参数是可选的字符串或对象。options 对象由`encoding`属性组成，它是文件夹名称的字符编码，默认为`utf8`。

编码值字符串可以替换对象作为第二个参数。第二个参数是可选的。它返回创建的文件夹路径。

如果我们想在`/tmp`中创建一个临时目录，那么前缀必须以一个特定于平台的路径分隔符结尾，我们可以从`require(‘path’).sep`中得到这个分隔符。

我们可以像下面的代码一样使用`mkdtempSync`函数:

```
const fs = require("fs");
const prefix = "./files/tempDir";
try {
  const folder = fs.mkdtempSync(prefix, {
    encoding: "utf8"
  });
  console.log("Temp directory created!", folder);
} catch (error) {
  console.error(error);
}
```

如果我们运行代码，我们会在`console.log`中记录文件夹路径，我们会看到在给定路径中创建了一个新的临时文件夹。

还有一个`mkdtemp`函数的承诺版本。`mkdtemp`函数有两个参数。第一个参数是临时文件夹的前缀，它是一个字符串。

它是您想要创建的文件夹的路径，该路径后面会附加一些字符。第二个参数是可选的字符串或对象。

可选对象由`encoding`属性组成，该属性是文件夹名称的字符编码，默认为`utf8`。

编码值字符串可以替换对象作为第二个参数。第二个参数是可选的。

当临时目录创建操作成功时，它返回一个用创建的目录的路径解析的承诺。

如果我们想在`/tmp`中创建一个临时目录，那么前缀必须以一个特定于平台的路径分隔符结尾，我们可以从`require(‘path’).sep`中得到这个分隔符。

我们可以在下面的代码中使用`mkdtemp`函数的 promise 版本:

```
const fsPromises = require("fs").promises;
const prefix = "./files/tempDir";(async () => {
  try {
    const folder = await fsPromises.mkdtemp(prefix, {
      encoding: "utf8"
    });
    console.log("Temp directory created!", folder);
  } catch (error) {
    console.error(error);
  }
})();
```

如果我们运行代码，我们会在`console.log`中记录文件夹路径，我们应该会看到在给定的路径中创建了一个新的临时文件夹。这是一个比使用`mkdtempSync`顺序创建目录更好的选择，因为它不会像同步版本那样在运行目录创建操作时占用整个程序。

我们用`mkdir`系列函数创建了永久目录来创建普通目录。`mkdir`系列函数可以创建所有的父目录以及我们可以创建的实际目录。

此外，我们用`mkdtemp`系列函数创建了临时目录。

有常规的基于回调的异步函数，新承诺的基本异步函数，以及每个函数的同步版本。

这种有希望的函数版本可以更好地顺序创建目录，因为它不会像同步版本那样在运行目录创建操作时占用整个程序。