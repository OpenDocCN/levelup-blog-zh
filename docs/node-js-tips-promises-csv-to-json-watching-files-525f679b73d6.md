# Node.js 提示—承诺、CSV 转 JSON 和监视文件

> 原文：<https://levelup.gitconnected.com/node-js-tips-promises-csv-to-json-watching-files-525f679b73d6>

![](img/7ad28ca8f72abf81cd7f32cb5e40b333.png)

由[文森特·范·扎林格](https://unsplash.com/@vincentvanzalinge?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写节点应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些编写节点应用程序时常见问题的解决方案。

# 处理 Node.js 中 require()引发的错误

我们可以使用 try-catch 来捕捉由`require`抛出的错误。

例如，我们可以写:

```
try {
  require('./app.js');
}
catch (e) { 
  console.log(e);
}
```

我们只需调用`try`中的`require`，就可以捕捉到`catch`中的错误。

# 如何对流使用 async/await

我们可以通过用`Promise`构造函数创建流的承诺，将`async`和`await`用于流。

例如，我们可以写:

```
const fd = fs.createReadStream('/foo.txt');
const hash = crypto.createHash('sha1');
hash.setEncoding('hex');
fd.pipe(hash);const readHash = new Promise((resolve, reject) => {
  hash.on('end', () => resolve(hash.read()));
  fd.on('error', reject);
});
```

然后我们可以通过编写以下代码在异步函数中使用`readHash`:

```
(async function() {
  const sha1 = await readHash;
  console.log(sha1);
}());
```

# 承诺重试设计模式

我们可以通过推迟承诺来重试承诺。

例如，我们可以写:

```
const retry = (fn, retries=3, err=null) => {
  if (!retries) {
    return Promise.reject(err);
  }
  return fn().catch(err => {
    return retry(fn, (retries - 1), err);
  });
}
```

`fn`是一个返回承诺的函数。

只要我们还没有用尽重试，我们就会重试。

如果被`fn`拒绝的承诺被拒绝，那么我们通过传入一个回调来调用`retry`并把`retries`减 1 来调用`catch`。

我们这样做，直到`retries`达到 0，然后重试次数用尽。

# 在编写 Promise 代码时，我们不需要`. catch(err => console.error(err))'

我们不需要编写`.catch(err => console.error(err))`，因为 Node.js 已经记录了承诺拒绝。

即使没有这行代码，我们也会知道发生了错误。

# 如何在 Node.js 中将 CSV 转换成 JSON

我们可以使用`csvtojson`库将 CSV 转换成 JSON。

要安装它，我们运行:

```
npm install --save csvtojson
```

然后我们可以通过写来使用它:

```
const csv = require("csvtojson");const readCsv = (csvFilePath) => {
  csv()
    .fromFile(csvFilePath)
    .then((jsonArrayObj) => { 
       console.log(jsonArrayObj); 
     })
}
```

我们可以通过将`csvFilePath`传递给`fromFile`方法来读取小文件。

它回报一个承诺。

我们在`jsonArrayObj`参数中得到转换后的结果。

我们也可以从流中读取内容，然后获取 JSON。

例如，我们可以写:

```
const readFromStream = (readableStream) => {
  csv()
    .fromStream(readableStream)
    .subscribe((jsonObj) => { 
       console.log(jsonObj)
    })
}
```

我们调用`fromStream`方法从流中获取数据。

然后我们用回调函数调用`subscribe`来读取对象。

此外，我们可以将`fromFile`与`async`和`await`一起使用，因为它返回一个承诺:

```
const csvToJson = async (filePath) => {
  const jsonArray = await csv().fromFile(filePath);
}
```

它也可以在命令行上使用。

例如，我们可以通过运行以下命令直接运行它:

```
npm install -g csvtojson
csvtojson ./csvFile.csv
```

# 无需重新启动 Node.js 即可在服务器上编辑节点应用程序文件，并查看最新的更改

我们可以使用像 Node-Supervisor 这样的包来监视文件的变化，并自动重启 Node 应用程序。

要安装它，我们运行:

```
npm install supervisor -g
```

然后，我们可以通过运行以下命令使用节点管理器运行我们的程序:

```
supervisor app.js
```

同样，我们可以使用 Nodemon 来做同样的事情。

我们可以通过运行以下命令来安装它:

```
npm install -g nodemon
```

全球安装。

或者，我们可以通过运行以下命令来安装它:

```
npm install --save-dev nodemon
```

将其作为项目的开发依赖项进行安装。

然后我们可以运行:

```
nodemon app.js
```

运行我们的应用程序。

# 用承诺来链接和分享先前的结果

为了用承诺共享先前的结果，我们可以用`then`将它们链接起来。

例如，我们可以写:

```
makeRequest(url1, data1)
  .then((result1) => {  
    return makeRequest(url2, data2);
  })
  .then((result2) => {  
    return makeRequest(url3, data3);
  })
  .then((result3) => {
     // ...
  });
```

我们从`then`回调中的先前承诺中得到解决的结果。

为了缩短这个过程，我们可以使用`async`和`await`来做同样的事情:

```
const makeRequests = async () => {
  //...
  const r1 = await makeRequest(url1, data1);
  const r2 = await makeRequest(url2, data2);
  const r3 = await makeRequest(url3, data3);
  return someResult;
}
```

![](img/539af44f4d60ff38c952c3221dd32af4.png)

照片由 [Lydia Tan](https://unsplash.com/@purplelydia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以在承诺中加入溪流。

此外，我们可以观察代码文件的变化，并用各种程序重启我们的节点应用程序。

`csvtojson`库让我们将 CSV 转换成 JSON。

承诺可以通过再次调用来重试。