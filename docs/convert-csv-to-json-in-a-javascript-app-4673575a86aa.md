# 在 JavaScript 应用程序中将 CSV 转换为 JSON

> 原文：<https://levelup.gitconnected.com/convert-csv-to-json-in-a-javascript-app-4673575a86aa>

![](img/421e65e30ff1328102732d7a1cafb87d.png)

Krzysztof Niewolny 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

将 CSV 转换为 JSON 是一项正在进行的任务，因为我们需要在应用程序中使用 CSV。

在本文中，我们将看看如何使用`csvtojson`模块将 CSV 转换成 JSON。

# 装置

`csvtojson`可作为 NPM 模块使用。我们可以运行以下命令来安装它:

```
npm i csvtojson
```

这个包适用于浏览器和 Node.js。

# 基本用法

我们可以从一个文件中获取一个 CSV 文件并将其转换成 JSON，或者我们可以从一个字符串中获取 CSV 文件并做同样的事情。

例如，我们可以从一个文件中获取一个 CSV 文件，并将其转换为 JSON 文件，如下所示:

`person.csv`

```
first_name,last_name
john,smith
jane,smith
```

`index.js`

```
const csvFilePath = 'person.csv'
const csv = require('csvtojson');
(async () => {
  const jsonObj = await csv().fromFile(csvFilePath)
  console.log(jsonObj);
})();
```

在上面的代码中，我们调用了`csv`函数，该函数使用接受文件路径的`fromFile`方法返回一个对象。

`fromFile`方法返回一个承诺，将从 CSV 解析的 JSON 作为解析值。

然后，当我们像上面那样记录`jsonObj`时，我们得到:

```
[ { first_name: 'john', last_name: 'smith' },
  { first_name: 'jane', last_name: 'smith' } ]
```

我们还可以将 CSV 从字符串解析成 JSON。为此，我们可以编写以下代码:

```
const csv = require('csvtojson');
const csvStr = `first_name,last_name
john,smith
jane,smith`;(async () => {
  const jsonObj = await csv().fromString(csvStr)
  console.log(jsonObj);
})();
```

在上面的代码中，我们有`csvStr`，它包含存储在字符串中的相同 CSV 内容。

然后我们调用`fromString`而不是`fromFile`来获取 CSV 内容，并用解析后的数据解析返回承诺。

因此，我们得到与第一个例子相同的结果。

# 异步处理 CSV URL 中的每一行

我们可以用`subscribe`方法逐行解析 CSV 字符串。例如，我们可以如下使用它:

```
const csv = require('csvtojson');
const csvFilePath = 'person.csv'csv()
.fromFile(csvFilePath)
.subscribe(
  (json) => console.log(json), 
  () => console.log('error'), 
  () => console.log('success')
);
```

在上面的代码中，我们调用了`fromFile`方法，但是我们在它之后调用了`subscribe`方法，而不是从承诺中获取解析后的值。

然后，我们将一次一行地获取解析后的值，而不是全部放在一个数组中。

# 转换为 CSV 行

我们还可以使用`subscribe`方法和`'line'`输出选项一次一行地获取原始 CSV 行。

例如，我们可以调用以下代码来实现这一点:

```
const csv = require('csvtojson');
const csvFilePath = 'person.csv'csv({ output: "line" })
.fromFile(csvFilePath)
.subscribe((csvLine) => console.log(csvLine))
```

然后我们得到:

```
john,smith
jane,smith
```

从控制台日志输出。

![](img/91bc1d5d828c59c8ebd1b4ef8cbba373.png)

照片由 [russn_fckr](https://unsplash.com/@russn_fckr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 选择

我们传递给`csv`函数的对象可以采用以下选项:

*   `output` —要转换成的格式。它可以是默认的`'json'`，CSV 行的`'csv'`，或者将 CSV 转换为 CSV 行字符串的`'line'`。
*   `delimiter` —用于分隔列的分隔符。`'auto'`应该是未知的值
*   `quote` —如果一个列有分隔符，它可以使用引号将列内容括起来，这样像`'hello,world'`这样的文本就不会被认为是在两列中。
*   `trim` — `true`如果我们想要空间被修剪，否则`false`
*   `checkType` —打开和关闭是否检查字段类型
*   `ignoreEmpty` —忽略 CSV 列中的空值
*   `noheader` — `true`如果我们解析的 CSV 中没有标题
*   `headers` —指定 CSV 数据头的数组。如果是`false`，那么这个值将覆盖 CSV 标题行
*   `flatKeys` —不要将标题字段中的`.`和方括号解释为嵌套对象。默认为`false`
*   `maxRowLength`—CSV 行可以包含的最大字符数
*   `checkColumn` —检查一行的列号是否与标题相同
*   `eol` —要检查的行尾字符
*   `escape` —引用列中使用的转义字符。默认为双引号
*   `includeColumns` —包含列模式的正则表达式
*   `ignoreColumns` —包含要忽略的列的模式的正则表达式
*   `colParser` —覆盖如何解析列的 JSON 对象。例如`{ foo : ’number’}`将把`foo`字段解析为一个数字。
*   `alwaysSplitAtEOL` —布尔值，表示是否按行尾字符分割每一行
*   `nullObject` —布尔值，表示我们是否保留`null`。
*   `downstreamFormat` —设置下游需要什么 JSON 数组格式的选项。可以用`'line'`解析成 CSVlines，`'array'`向下游写一个完整的 JSON 数组
*   `needEmitAll` —如果调用了`.then`或使用了`await`，解析器将构建 JSON 结果。

# 结论

有了`csvtojson`节点包，将 CSV 解析成 JSON 就很容易了。它返回一个承诺或逐行发出解析后的 CSV，这样我们就可以按照自己喜欢的方式处理它们。

解析是异步完成的，所以性能不会成为问题。