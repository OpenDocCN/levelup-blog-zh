# 了解 Node.js 文件系统模块

> 原文：<https://levelup.gitconnected.com/understanding-node-js-file-system-module-b16da1e01949>

文件系统是一种机制，用于控制数据在硬盘上的存储方式，以及在操作系统上的访问和管理方式。Node.js 中的文件系统模块允许您以编程方式与操作系统上的文件系统进行交互。

![](img/5793ddcb85159c8a0ad29cc5d065e351.png)

图片来自 Pixabay

使用文件系统(`fs`)模块，我们可以执行读、写、删除和许多其他操作。让我们看看一些最重要的操作。

# 先决条件

在我们开始研究文件系统操作之前，我们需要导入`fs`模块。

```
const fs = require("fs");
```

# 同步和异步读取文件

fs 模块提供了简单的读取文件的方法:`fs.readFile()`和`fs.readFileSync()`T12。`fs.readFile()`用于异步读取文件，`fs.readFileSync()`用于同步读取文件。

```
fs.readFile('file.txt', function(err, data) {
    if (err) return callback(err);

    // If succeeded, print the contents.
    console.log(data);
});
```

这里，文件路径必须作为第一个参数和一个回调函数提供，该函数将使用文件数据进行调用。

对于`fs.readFileSync()`，我们将文件路径作为参数传递。

```
const data = fs.readFileSync('test.txt', "utf8");
console.log(data);
```

`utf8` 是默认编码，但是我们可以指定任何我们需要的定制编码作为两个方法中的第二个参数。

这两种方法都在返回数据之前读取内存中的整个文件内容。当我们读取大文件时，这可能会造成问题。为了处理这些情况，建议将*流*与`fs`模块一起使用。

# 通过流读取文件

我们经常要处理大文件。读取操作可能会花费相当多的时间，并占用更多的内存。在这种情况下，我们可以使用流。我们可以使用读取文件，并在一大块数据准备好发送时通过 HTTP 连接提供它。

```
const http = require('http')
const fs = require('fs')const server = http.createServer((req, res) => {
  const stream = fs.createReadStream('test.txt')
  stream.pipe(res)
})
server.listen(3000, () => {
    console.log(`Server running at port 3000`)
})
```

这里，我们正在建立 HTTP 连接，读取文件并将其传输到 HTTP 客户端。`pipe()` 用于将文件流传送到 HTTP 响应。

# 同步和异步写文件

编写文件最简单的方法是调用`fs.writeFile()` ，这是一个异步操作。

```
fs.writeFile('file.txt', "Hello world", function(err) {
    if (err) return callback(err); // If succeeded, print the message.
    console.log("file created successfully!")
});
```

在同步写操作中，我们写如下:

```
try {
  const file = fs.writeFileSync('test.txt', "Hello world");
} catch (err) {
  console.error(err)
}
```

默认情况下，这些 API 调用将覆盖已经存在的内容。我们可以通过在 options 参数中指定一个标志来覆盖此行为，如下所示:

```
{ flag: 'a+' }
```

当写入大量数据时，我们可能会遇到内存效率的问题。像可读的流一样，我们也有可写的流 API `fs.createWriteStream()` 来成块地写数据。

# 检查文件的状态

我们可以使用`fs.stat()`和`fs.statSync()` *来检查文件的细节。*

```
fs.stat('test.txt', (err, stats) => {
    if (err) {
      console.error(err)
      return
    }
    console.log(stats.isFile());
    console.log(stats.isSymbolicLink());
    console.log(stats.isDirectory());
    console.log(stats.size);
  });const status = fs.statSync('test.txt'); console.log(status.isFile());
  console.log(status.isSymbolicLink());
  console.log(status.isDirectory());
  console.log(status.size);
```

这里， *stats 或 status* 变量上的`isFile()` 方法告诉它是否是一个文件。同样，`isDirectory()` 告知是否是目录。`isSymbolicLink()` 告知文件是否是符号链接。最后，`size`*属性告诉文件的大小。*

# 删除文件或符号链接

我们可以异步删除一个文件或一个符号链接。该方法只接受文件路径和回调函数。

```
fs.unlink('test.txt', (err) => {
  if (err) throw err;
  console.log('test.txt was removed');
});
```

此方法不适用于目录。我们可以用`fs.rmdir()` 来代替。

对于同步操作，我们有`fs.unlinkSync()`。

```
fs.unlinkSync('text.txt');
```

# 重命名文件

可以使用`fs.rename()` *重命名文件。这里，它以旧文件名、新文件名和一个回调函数作为参数。*

```
fs.rename('file.txt', 'hello.txt', (err) => {
  if (err) throw err;
  console.log('Rename complete!');
});
```

同样，我们有`fs.renameSync()` 进行同步操作。

# 包扎

在本文中，我们只是介绍了一些最常用的 API 方法。在`fs`模块中有更多的方法。查看[此处](https://nodejs.org/api/fs.html)的完整文档。

这里是本文中使用的例子的 [GitHub 库](https://github.com/swathisprasad/nodejs-fs-module-examples)。

*本文原载于*[*techshard.com*](https://techshard.com/2019/06/24/understanding-node-js-file-system-module/)*。*

[](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### 排名前 44 的 Node.js 教程-免费学习 Node.js。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/node-js)