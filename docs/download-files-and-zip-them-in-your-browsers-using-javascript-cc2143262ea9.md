# 使用 Javascript 下载文件并在浏览器中压缩它们

> 原文：<https://levelup.gitconnected.com/download-files-and-zip-them-in-your-browsers-using-javascript-cc2143262ea9>

## 与其生成 zip 文件并从您的服务器传输，为什么不下载数据并在您的浏览器中压缩它们呢？

![](img/6daeae08299890162e691a714005f1e7.png)

我最近在做一个副业项目，它可以根据用户的请求生成报告。对于每个请求，我们的后端将生成一个报告，上传到亚马逊 S3 存储，并将其 URL 返回给客户端。由于生成报告需要一段时间，所以输出文件被存储起来，服务器通过请求参数缓存它们的 URL。如果用户订购相同的东西，后端将返回现有文件的 URL。

前几天，我有了新的要求。我需要下载包含数百份报告的 zip 文件，而不是单个文件。我想到的第一个解决方案是:

*   在服务器上准备 zip 文件
*   将其上传到存储器
*   给客户端一个下载它的 URL

但是这种解决方案有一些缺点:

*   生成 zip 文件的逻辑相当复杂。我需要考虑为每个请求生成所有文件，或者在重用现有文件和生成新文件之间进行组合。这两种方法似乎都很复杂。它们需要时间来处理，并且需要大量的编码、测试和后期维护工作。
*   它不能利用我已经建立的功能。尽管 Zip 文件是不同的报告集，但是很可能大多数单独的报告都是由先前的请求生成的。因此，虽然 Zip 文件本身不太可能重用，但单个文件可以。使用上面的方法，我需要一直重做整个事情，这不是很有效率。
*   生成 zip 文件需要很长时间。由于我的后端是一个单线程进程，这个操作可能会阻塞其他请求一段时间，并且在这段时间内可能会超时。
*   在客户端很难跟踪流程。我喜欢在网站上放一个进度条。如果一切都在后端处理，我需要找到一个额外的方法向前端报告状态。这并不容易。
*   我想为我的基础设施节省成本。如果我们可以将一些计算转移到前端，并降低基础架构的成本，那就太好了。我的客户不会介意他们多等几秒钟，或者在他们的笔记本电脑上多花一些内存。

我想到的最终解决方案是:将所有文件下载到浏览器并压缩到那里。在这篇文章中，我将介绍我是如何做到的。

***免责声明*** *:在这篇文章中，我假设你已经对*[*Javascript*](https://javascript.info/)*和*[*Promise*](https://web.dev/promises/)*有了基本的了解。如果你没有，我会建议你先了解他们，然后再来这里:)*

# 下载单个文件

在应用新的解决方案之前，我的系统允许下载单个报告文件。有许多方法可以做到这一点。后端可以通过 HTTP 请求直接响应原始文件内容，或将文件上传到另一个存储并返回文件 URL。我选择第二种方法，因为我想缓存所有生成的文件。

一旦我有了一个文件 URL，客户端的工作就非常简单了:在一个新标签中打开这个 URL。浏览器将完成下载文件的其余工作。

```
 const downloadViaBrowser = url => {
 window.open(url, ‘_blank’);
}
```

# 下载多个文件并存储在内存中

当下载和压缩多个文件时，我们不能再使用上面的简单方法了。

*   如果一个 JS 脚本试图同时打开许多链接，浏览器会怀疑这是否是一个威胁，并警告用户阻止这些操作。虽然用户可以确认继续，但这不是一个好的体验
*   您无法控制下载的文件。浏览器管理文件内容和位置

另一种处理方式是使用`fetch`下载文件并将数据作为 [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) 存储在内存中。然后，我们可以将其写入文件或将这些 blob 数据合并到一个 zip 文件中。

```
const download = url => {
  return fetch(url).then(resp => resp.blob());
};
```

该函数返回一个将被解析为 blob 的承诺。我们可以结合一个`Promise.all()`来下载多个文件。`Promise.all()`将一次完成所有的承诺，如果所有的子承诺都已解决或其中一个出现错误，则进行解决。

```
const downloadMany = urls => {
  return Promise.all(urls.map(url => download(url))
}
```

# 按 X 个文件分组下载

但是如果我们需要一次下载大量的文件会怎么样呢？比如说 1000 个文件？使用`Promise.all()`可能不再是一个好主意。您的代码将一次发送一千个请求。这种方法存在许多问题:

*   操作系统和浏览器支持的并发连接数是有限的。因此，浏览器一次只能处理几个请求。其他请求被放入队列，并进行超时计数。结果是，您的大多数请求甚至在发送之前就会超时。
*   一次发送大量的请求也会使后端过载

我考虑的解决方案是将文件分成多个组。比方说，我有 1000 个文件要下载。我将每次下载 5 个文件，而不是用`Promise.all()`一次下载所有文件。做完那 5 个，我再开始另一包。总共我会下载 250 包。

为了实现这一点，我们可以做一个自定义逻辑。或者我可以建议的一个更简单的方法是利用第三方库 [bluebirdjs](http://bluebirdjs.com/) 。该库实现了许多有用的 promise 函数。对于这个用例，我将使用 [Promise.map()](http://bluebirdjs.com/docs/api/promise.map.html) 。注意这里的`Promise`是库提供的自定义承诺，而不是内置承诺。

```
import Promise from 'bluebird';const downloadByGroup = (urls, files_per_group=5) => {
  return Promise.map(
    urls, 
    async url => {
      return await download(url);
    },
    {concurrency: files_per_group}
  );
}
```

通过上面的实现，该函数将接收一个 URL 数组，并开始下载所有 URL，每次最多下载`files_per_group`。该函数返回一个承诺，当所有的 URL 都被下载时，该承诺将被解析，如果其中任何一个失败，该承诺将被拒绝。

# 创建 zip 文件

现在我已经把所有东西都下载到内存里了。正如我上面提到的，下载的内容存储为 Blob。下一步是使用这些 Blob 数据创建一个 zip 文件。

```
import JsZip from 'jszip';
import FileSaver from 'file-saver';const exportZip = blobs => {
  const zip = JsZip();
  blobs.forEach((blob, i) => {
    zip.file(`file-${i}.csv`, blob);
  });
  zip.generateAsync({type: 'blob'}).then(zipFile => {
    const currentDate = new Date().getTime();
    const fileName = `combined-${currentDate}.zip`;
    return FileSaver.saveAs(zipFile, fileName);
  });
}
```

# 最终代码

让我们在这里完成我为此所做的所有代码。

```
import Promise from 'bluebird';
import JsZip from 'jszip';
import FileSaver from 'file-saver';const download = url => {
  return fetch(url).then(resp => resp.blob());
};const downloadByGroup = (urls, files_per_group=5) => {
  return Promise.map(
    urls, 
    async url => {
      return await download(url);
    },
    {concurrency: files_per_group}
  );
}const exportZip = blobs => {
  const zip = JsZip();
  blobs.forEach((blob, i) => {
    zip.file(`file-${i}.csv`, blob);
  });
  zip.generateAsync({type: 'blob'}).then(zipFile => {
    const currentDate = new Date().getTime();
    const fileName = `combined-${currentDate}.zip`;
    return FileSaver.saveAs(zipFile, fileName);
  });
}const downloadAndZip = urls => {
  return downloadByGroup(urls, 5).then(exportZip);
}
```

# 结论

*   利用客户端的能力有时对于减少后端的工作量和复杂性非常有用
*   不要一次发送大量的请求。你可以在前端和后端都遇到麻烦。而是把作品分成小块。
*   介绍一些第三方库[蓝鸟](http://bluebirdjs.com/)、 [jszip](https://www.npmjs.com/package/jszip) 、[文件保存器](https://github.com/eligrey/FileSaver.js)。它们对我很有用，也可能对你有帮助:)

*原发布于*[*https://huynvk . dev*](https://huynvk.dev/blog/download-files-and-zip-them-in-your-browsers-using-javascript)*。*

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)