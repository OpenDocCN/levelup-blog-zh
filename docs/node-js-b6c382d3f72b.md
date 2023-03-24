# 节点. js

> 原文：<https://levelup.gitconnected.com/node-js-b6c382d3f72b>

## Node.js 是什么，是做什么的？

![](img/0bbfa6f85f525f6fa2b7e6a90407e72c.png)

## Node.js 是什么？

Node.js 是一个开源、跨平台的 JavaScript 运行时环境，它在浏览器之外执行 JavaScript 代码。它主要用于开发应用程序。

## 核心模块

Node.js 拥有核心模块，执行每个学习 Node.js 的开发人员都应该理解的最基本的功能。这些模块是 Node.js 的一部分，所以您不需要`npm install [ node_module ]`。以下是一些核心模块:

![](img/99abef415c26f0a22b3f0c3d9d5cdaf5.png)

## 小路

路径模块包括处理文件路径的方法。只需使用`__filename`就可以获得基本文件名、目录名、文件扩展名和连接路径。

```
const path = require('path'); // imports path module// Base File Nameconsole.log(path.basename(__filename));// Directory Name // Same As __dirnameconsole.log(path.dirname(__filename)); // File Extensionconsole.log(path.extname(__filename));// Concatenate Paths // target path: ../test/hello.htmlconsole.log(path.join(__filename, 'test', 'hello.html'))
```

![](img/00e0aa96a8cf1374d60a9a2cb72630c8.png)

## 文件系统

FS 模块允许你操作文件。您可以创建文件、读取文件、写入文件、附加到文件以及重命名文件。此外，为了操作文件，你必须掌握你之前学过的“路径”核心模块的知识。

```
const fs = require('fs'); // imports fs moduleconst path = require('path'); // imports path module// Create folderfs.mkdir(path.join(__dirname, '/test'), {}, err => { if (err) throw err; console.log('Folder created...');})// Write to file // overwrites anything in the filefs.writeFile(path.join(__dirname, '/test', 'hello.html'), 'Hello World!', err => { if (err) throw err; console.log('File written to...')})// Append to filefs.appendFile(path.join(__dirname, '/test', 'hello.html'), ' I love Node.js!', err => { if (err) throw err; console.log('File appended to...')})// Read file // utf8 to read languagefs.readFile(path.join(__dirname, '/test', 'hello.html'), 'utf8', (err, data) => { if (err) throw err; console.log(data)})// Rename filefs.rename(path.join(__dirname, '/test', 'hello.html'), path.join(__dirname, '/test', 'helloworld.txt'), err => { if (err) throw err; console.log('File renamed...');})
```

## 统一资源定位器

URL 模块包括解析 URL 的方法。可以从 URL 获得的一些信息包括主机名和端口、查询、路径名和搜索参数。您还可以操作 URL 的搜索参数。

```
const url = require('url'); // imports url moduleconst myUrl = new URL('http://mywebsite.com/hello.html?id=100&status=active'); // example website// Serialized URL // Same as 'myUrl.toString()'console.log(myUrl.href);// Host (root domain) // shows host name and port numberconsole.log(myUrl.host);// Hostname // does not get port numberconsole.log(myUrl.hostname);// Port // does not get host nameconsole.log(myUrl.port);// Pathname // Stuff between'.com' and '?'console.log(myUrl.pathname); // Serialized Query // Stuff after the '?'console.log(myUrl.search);// Params // Shows params in an objectconsole.log(myUrl.searchParams);// Add ParamsmyUrl.searchParams.append('abc', '123')console.log(myUrl.searchParams);// Loop through Params myUrl.searchParams.forEach((value, key) => console.log(`${key}: ${value}`))
```

![](img/b2066e59cb2826d48282479f78f92282.png)

## 超文本传送协议

HTTP 模块允许你为你的应用程序创建一个服务器。这通常与 Ruby on Rails 和 Express 等框架一起使用，但是 Node.js 有能力创建一个简单的服务器。

```
const http = require('http'); // imports http module// Create a server objecthttp.createServer((req, res) => { // Write Response res.write('Hello World'); res.end();}).listen(5000, () => console.log('Server running...'));
```

在终端中用`node [file name]`运行这段代码。等待终端响应`Server running...`。然后，在浏览器的地址栏中输入`localhost:5000`。您应该会在页面上看到`Hello World`。使用我前面提到的框架，您可以更容易地管理您的服务器和数据库。

![](img/2532161324b07a0d1acea8749a7edd2a.png)

## 事件

事件模块允许你创建事件发射器来监听事件触发器并执行一个动作。

```
const EventEmitter = require('events'); // imports events module// Create Classclass MyEmitter extends EventEmitter {}// Initialize Objectconst emitter = new MyEmitter();// Event Listeneremitter.on('event', () => console.log('Event fired!'));// Initialize Eventemitter.emit('event');
```

事件模块允许您创建在类中使用的自定义事件。事件模块将 JavaScript 事件和事件侦听器转换到 Node.js。

## 操作系统

OS 模块包含获取设备操作系统信息的方法。这在您启动应用程序供他人使用时会很有帮助。它可以接收设备平台、CPU 拱、CPU 核心信息、空闲内存、总内存、主目录、正常运行时间等详细信息。

```
const os = require('os'); // imports os module// Platformconsole.log(os.platform());// CPU Archconsole.log(os.arch());// CPU Core Infoconsole.log(os.cpus());// Free Memoryconsole.log(os.freemem());// Total Memoryconsole.log(os.totalmem());// Home Directoryconsole.log(os.homedir());// Uptime (in seconds)console.log(os.uptime());
```

操作系统模块可能有用的一些示例是，当用户试图安装应用程序时，您可以检查设备的平台，比如说 android，并安装与 android 平台匹配的应用程序的 android 版本。

## 结论

学习 Node.js 的基础知识让我更深刻地理解了为什么在我的应用程序中使用它，以及我以前使用的节点模块。Node.js 有一个很大的节点模块库，任何人都可以学习使用。我现在明白了 Node.js 是一个多么伟大的工具，以及为什么人们倾向于将它作为 MERN 的一部分。