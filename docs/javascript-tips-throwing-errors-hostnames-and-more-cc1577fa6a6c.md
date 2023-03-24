# JavaScript 提示—抛出错误、主机名等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-throwing-errors-hostnames-and-more-cc1577fa6a6c>

![](img/9a845ea0098f0bc036e1c6e32dc5a678.png)

Justin Young 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 提取 URL 的主机名部分

我们可以用`window.location`的`hostname`属性提取 URL 的主机名部分。

例如，如果我们去[http://example.com/,](http://example.com/,)然后`window.location.hostname`返回`“example.com”`。

# 从数组中获取前 N 个元素

要从数组中获得第一个`n`数量的元素，我们可以使用`slice`方法。

例如，我们可以写:

```
arr.slice(0, n)
```

获取数组的第一个`n`元素。

不包括索引为`n`的项目。

我们还可以设置数组的`length`属性。

所以`arr.length = n`会把`arr`截断成长度`n`。

# 在 Javascript 中查找对象数组中的值

要在具有给定值的对象数组中找到一个值，我们可以使用`find`方法。

例如，如果我们有给定的数组:

```
const arr = [
  { name: "james", value:"foo", other: "that" },
  { name: "mary", value:"bar", other: "that" }
];
```

然后我们可以写:

```
const obj = arr.find(o => o.name === 'james');
```

找到`name`属性设置为``'james'`的条目，并将其分配给`obj`。

# 有条件地向对象添加属性

我们可以用三元运算符有条件地给对象添加一个属性。

例如，我们可以写:

```
const obj = {
  b: addB ? 5 : undefined,
  c: addC ? 5 : undefined,
}
```

其中`addB`和`addC`是布尔表达式，用于确定`b`和`c`是否应该分别有一个值。

# 在 Javascript 中四舍五入到小数点后 1 位

要将一个数字四舍五入到小数点后一位，我们可以使用`toFixed`方法来实现。

例如，我们可以写:

```
number.toFixed(1)
```

四舍五入到小数点后 1 位。

# 为什么 Node.js 的 fs.readFile()返回的是缓冲区而不是字符串？

如果没有指定编码，那么`fs.readFile`返回一个缓冲区。

为了让它返回一个字符串，我们传入编码。

例如，我们可以写:

```
fs.readFile("foo.txt", "utf8", (err, data) => {...});
```

我们读取`foo.txt`的内容，并指定编码为`'utf8'`，以确保它被读取为文本。

# Node.js Array.forEach 是异步的吗？

Node.js 的数组`forEach`方法与常规的`forEach`方法相同。

因此，这是一个同步函数。

如果我们想要异步迭代，我们可以使用带有承诺的 for-of 循环或者来自`async`库的`async.each`:

```
async.each(files, saveFile, (err) => {
  //...
});
```

我们传入数组作为第一个参数进行迭代。

作为第二个参数在每个条目上运行的函数。

最后一个参数是迭代完成时要调用的回调。

`er`使用遇到错误时定义的错误对象。

# 直接从对象生成格式化的易读 JSON

`JSON.stringify`可以用来格式化已经字符串化的 JSON。

例如，我们可以写:

```
JSON.stringify(obj, null, 2);
```

将字符串化的 JSON 缩进两个空格。

我们还可以传入一个缩进字符:

```
JSON.stringify(obj, null, '\t');
```

然后我们用制表符缩进。

# “抛出新错误”和“抛出对象”的区别

投掷错误和投掷任何其他种类的物体是有区别的。

`Error`对象有名称、消息和堆栈跟踪。

抛出任何其他类型的对象都会将消息暴露给用户。

例如，我们可以写:

```
try {
  throw "error";
} catch (e) {
  console.log(e);
}
```

然后控制台日志将记录`'error'`。

不会有`name`和`message`属性。

另一方面，如果我们有:

```
try {
  throw new Error("error")
} catch (e) {
  console.log(e.name, e.message);
}
```

那么`e.name`将是`'Error'`并且`e.message`将是`'error'`。

![](img/0a170bffd68cec4f2654b92dd099c5c2.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用`window.location.hostname`属性提取 URL 的主机名部分。

我们可以使用`slice`从数组中获取第一个`n`条目。

要从对象数组中找到具有给定属性值的对象，我们可以使用`find`。

抛出`Error`实例和任何其他类型的对象或值是有区别的。