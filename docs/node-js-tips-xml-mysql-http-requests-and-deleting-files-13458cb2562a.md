# Node.js 提示— XML、MySQL、HTTP 请求和删除文件

> 原文：<https://levelup.gitconnected.com/node-js-tips-xml-mysql-http-requests-and-deleting-files-13458cb2562a>

![](img/2dfc26f5928f59ac784ccde6ed07e81d.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写节点应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些编写节点应用程序时常见问题的解决方案。

# 节点。JS 异步并行

`async`模块是让我们并行运行异步代码的`parallel`方法。

例如，我们可以写:

```
async.parallel({
  one(callback) {
    callback(null, 'foo');
  },
  two(callback) {
    callback(null, 'bar');
  }
}, (err, results) => {
  //...
});
```

我们有两个方法，在传递给`async.parallel`的对象中接受回调。

它们都用错误和结果对象作为参数来调用`callback`。

然后我们在`results`对象中得到两者。

`results`会是:

```
{ one: 'foo', two: 'bar' }
```

方法名被用作结果的关键字。

# 正在读取 Node.js 中的 XML 文件

我们可以用`readFile`方法读取 Node.js 中的 XML 文件。

然后我们可以用`xml2json`库解析内容。

例如，我们可以写:

```
fs = require('fs');
const parser = require('xml2json');fs.readFile('./data.xml', (err, data) => {
  const json = parser.toJson(data);
  console.log(json);
});
```

我们用 XML 文件的路径调用`readFile`。

然后我们用`data`得到 XML 文本。

接下来我们调用`parser.toJson`来解析存储在`data`中的 XML 字符串。

# 节点 MySQL Escape LIKE 语句

我们可以这样写来转义`LIKE`语句中的字符:

```
mysql.format("SELECT * FROM persons WHERE name LIKE CONCAT('%', ?,  '%')", searchString)
```

我们只是将`%`和`searchString`连接在一起。

# 删除超过一小时的文件

要获取一个小时以前的所有文件并删除它们，我们可以使用`readdir`来获取目录中的文件。

然后，我们可以使用`fs.stat`来获取时间，并使用它与当前时间进行比较，以查看自文件创建以来是否已经过去了一个小时。

当文件在一小时前或更早创建时，我们可以使用`rimraf`删除它。

例如，我们可以写:

```
const uploadsDir = path.join( __dirname, '/uploads');fs.readdir(uploadsDir, (err, files) => {
  files.forEach((file, index) => {
    fs.stat(path.join(uploadsDir, file), (err, stat) => {
      let endTime, now;
      if (err) {
        return console.error(err);
      }
      now = new Date().getTime();
      endTime = new Date(stat.ctime).getTime() + 3600000;
      if (now > endTime) {
        return rimraf(path.join(uploadsDir, file), (err) => {
          if (err) {
            return console.error(err);
          }
          console.log('successfully deleted');
        });
      }
    });
  });
});
```

我们用`readdir`读取文件夹。

然后我们循环回调中获得的`files`。

然后我们调用`fs.stat`来获取文件信息。

我们使用`ctime`属性来获取文件创建的时间。

然后我们用`getTime`把它变成时间戳。

我们加上 3600000，这是以毫秒为单位的一个小时。

那么如果`now > endTime`是`true`，我们就知道从文件创建到现在已经过去了一个多小时。

然后我们可以使用`rimraf`删除文件。

我们使用完整路径。

# 使用 Node.js 调用 Web 服务

我们可以像在客户端一样，通过发出 HTTP 请求来调用节点应用程序中的 web 服务器。

例如，我们可以写:

```
const http = require('http');
const data = JSON.stringify({
  'id': '2'
});const options = {
  host: 'host.com',
  port: '80',
  path: '/some/url',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;',
    'Content-Length': data.length
  }
};const req = http.request(options, (res) => {
  let msg = ''; res.setEncoding('utf8');
  res.on('data', (chunk) => {
    msg += chunk;
  });
  res.on('end', () => {
    console.log(JSON.parse(msg));
  });
});req.write(data);
req.end();
```

我们使用`http.request`方法来发出请求。

我们叫`req.write`用身体发出请求

`options`对象有头、主机名和请求方法。

一旦请求完成，回调就被调用，并且`res`拥有带有响应的流。

我们监听`data`事件来获取数据块并连接到`msg`字符串。

然后我们监听`end`事件来解析检索到的块。

![](img/c55ababcc57dc7853e56d27d2e209287.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

`async.parallel`可以并行运行回调。

我们可以用 rimraf 删除文件。

此外，我们可以用`http`模块发出 HTTP 请求。

为了将 XML 解析成 JSON，我们可以使用`xml2json`包。