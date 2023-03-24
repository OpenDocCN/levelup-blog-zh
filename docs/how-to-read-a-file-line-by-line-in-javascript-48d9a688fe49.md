# 如何在 JavaScript 中逐行读取文件

> 原文：<https://levelup.gitconnected.com/how-to-read-a-file-line-by-line-in-javascript-48d9a688fe49>

## 哪种方式更好？

![](img/6f4d880453e609501fca1d96298146bf.png)

由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有些情况下，我们需要在 JavaScript 中逐行读取文件，这可能是分析一些日志，或者提取部分信息。简而言之，我们不需要将文件的全部内容加载到内存中，因为一次读取一个大文件会使进程内存密集，你有更好的办法，那么你该怎么办？

# 一次读完

你可以准备一个大于 50MB 的文件，然后用下面的代码一次性读取它的全部内容，然后计算这个程序使用的内存和消耗的时间:

```
console.time('Time');const fs = require('fs');
const allContents = fs.readFileSync('test.json', 'utf-8');allContents.split(/\r?\n/).forEach((line) => {
  // console.log('line: ', line);
});console.log(`Used ${process.memoryUsage().heapUsed / 1024 / 1024} MB`);console.timeEnd('Time');
```

我在这里准备了一个 65MB 左右的`test.json`文件，运行程序的最终结果是:

```
Used 261.44498443603516 MB
Time: 841.644ms
```

你可以看到它需要大约`261MB`的内存，如果你有 1.4 GB 以上的文件，不推荐这种方法。因为 Node.js 默认可以使用的最大内存空间是 1.4G 左右(为了保证 V8 的内存回收效率)。

# 使用读取线

我们可以使用 Node.js 提供的 Readline API，通过从任何可读流中一次读取一行，可以使用它来逐行读取文件:

之后我们得到:

```
Used 4.973365783691406 MB
Time: 672.116ms
```

可以看出它使用的内存减少了好几倍。为了解释上面的代码:首先，我们使用`readline.createInterface`创建一个接口，其中输入是使用`fs.createReadStream('test.json')`创建的可读流。在接口选项中，我们还传递了`crlfDelay`，它表示如果`\r`和`\n`之间的延迟超过`crlfDelay`毫秒，那么`\r`和`\n`都将被视为单独的行尾输入。`crlfDelay`将被强制为不小于`100`的数。它可以被设置为`Infinity`，在这种情况下`\r`后跟`\n`将总是被认为是一个单独的换行符(这对于[读取带有`\r\n`行分隔符的文件](https://nodejs.org/api/readline.html#example-read-file-stream-line-by-line)可能是合理的)。

接下来绑定`line`事件，这是每一行的数据，我们可以分析。最后我们监听了`close`事件，并把它做成一个承诺，也就是说整个文件都读完了。

# 使用 npm 模块

最后，我们可以选择 NPM 上的一些第三方库来实现类似的功能。我在这里做了简单的搜索，找到了`[n-readlines](https://www.npmjs.com/package/n-readlines)`、`[line-reader](https://www.npmjs.com/package/line-reader)`、`[readlines-ng](https://www.npmjs.com/package/readlines-ng)`等。按更新和下载排序，最终选择了`[n-readlines](https://github.com/nacholibre/node-readlines)`。

它的源代码比较简单，不到 160 行。它不使用流，而是使用缓冲区和`fs`模块来读取[块](https://github.com/nacholibre/node-readlines/blob/master/readlines.js#L84)中的文件内容，这也意味着它不会将整个文件加载到内存中。以下是使用案例:

```
console.time('Time');const ReadLines = require('n-readlines');
const readLines = new ReadLines('test.json');let line;
while ((line = readLines.next())) {
  // console.log(line.toString('ascii'));
}console.log(`Used ${process.memoryUsage().heapUsed / 1024 / 1024} MB`);
console.timeEnd('Time');
```

之后我们得到:

```
Used 6.603157043457031 MB
Time: 2.015s
```

可以看出，它使用的内存也更少，但消耗的时间更多。

# 结论

我在看`[tsc-watch](https://www.npmjs.com/package/tsc-watch)`的源码，发现它用`readline`分析`tsc`的输出，然后做它的逻辑。我也推荐使用`[readline](https://github.com/gilamran/tsc-watch/blob/master/lib/tsc-watch.js#L93)`逐行读取文件，如果有高级需求，第三方库也是不错的选择。

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说很重要——谢谢。