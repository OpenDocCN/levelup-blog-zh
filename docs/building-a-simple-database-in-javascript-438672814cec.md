# 用 Javascript 构建简单的数据库

> 原文：<https://levelup.gitconnected.com/building-a-simple-database-in-javascript-438672814cec>

![](img/db058467ac81275b1f4e2d1e9ee1e09f.png)

*最初发布于*[*https://devtails . XYZ*](https://devtails.xyz/@adam/building-a-simple-database-in-javascript)*。*

我大约从 2015 年开始使用 [MongoDB](https://www.mongodb.com/atlas/database) 。我喜欢不定义模式和不断运行迁移的灵活性。尤其是在原型和实验方面。

作为我的项目[https://github.com/engramhq/engram](/engram)的一部分，我正在重新学习、探索和分享 web 开发的不同部分是如何构建的。现在很容易拉别人的代码或者程序就收工了。但我发现这通常会让我们对它实际上是如何工作的缺乏了解，并限制了可能的事情。

这篇文章将介绍如何创建一个简单的 NoSQL 数据库，将数据持久地存储在`json`文件中。

我们将逐步开发它，让每一个 CRUD(创建、读取、更新、删除)首字母缩略词在进入下一个之前工作，因为每一个都建立在前一个的基础上。

我录下了整个过程的视频。我可能最终会在顶部添加口头解释，但现在如果你把它调到 2 倍的速度，应该可以看到它全部聚集在一起，一起工作。有标记的章节对应于本教程的标题。

## 构建 TCP 服务器

一般来说，大多数数据库通过 TCP 连接进行通信。我们将使用 node 中内置的`net`包来创建一个服务器，该服务器监听连接并回显客户端发送给它的任何内容。

```
// server.js
const net = require('net')

const port = 3939;
const hostname = '127.0.0.1'

const server = net.createServer()
server.listen(port, hostname, () => {
  console.log("listening on port", port)
})

server.on('connection', (sock) => {
  sock.on('data', (data) => {
    console.log("Received", String(data));
    sock.write(data);
  })
})
```

## 构建 Hello World 客户端

为了测试服务器，我们需要一个单独的客户端程序。这个使用一个套接字连接到创建服务器的特定主机(“127.0.0.1”)和端口(3939)。它使用`write`方法向服务器写入一条消息，并在接收到数据后将其返回控制台。

```
// client.js
const net = require('net')

const port = 3939;
const hostname = '127.0.0.1'

const socket = new net.Socket();

socket.connect(port, hostname, () => {
  socket.write("Hello World!")
  socket.on('data', (data) => {
    console.log(String(data));
  })
})
```

## 测试 Hello World 客户端

```
node server.js
node client.js
```

您必须首先启动服务器，以便它准备好接受来自客户端的连接。

## 创建—实现 insertOne

既然我们已经在服务器和客户机之间有了一些基本的通信，那么是时候添加我们的第一个命令`insertOne`了。这为之后的所有事情奠定了基础，所以这是拼图中重要的一块。

可以很快看到的第一个设计决策是服务器和客户机之间的数据格式转换为 JSON 字符串。这使得结构化数据变得极其简单。在一个更大的数据库系统中，使用 JSON 格式的浪费可能是一个问题，但是对于我们的目的来说，我们没有通过二进制协议发送信息是一点也不明显的。

为了进一步简化，`_id`属性只是作为 unix epoch 的毫秒数的时间戳生成的。这意味着在同一毫秒内创建两个文档会给我们带来 duplicate _ ids，这会导致各种各样的问题。在 engram notes 数据库的实际使用中，我很难在同一毫秒内手动生成两个项目，所以这是目前可以接受的限制。

实际数据存储在一个 JavaScript 对象`collections`中。为每个集合创建另一个对象来存放特定集合中的“文档”。

下面是将单个文档添加到`notes`集合后，`collections`变量的样子。

```
{
  "notes": {
    "1667863798986": {
      _id: "1667863798986",
      body: "Hello World!"
    }
  }
}
```

我希望这些数据即使在数据库服务器停止运行后也能持续存在，这意味着这些数据需要在某个时候写入磁盘。现在，我决定这将作为一个`json`文件为每个集合编写。每当对集合进行更新时，整个集合都将被字符串化并写入磁盘。在高容量的情况下，这在许多方面可能是低效的，但是这使得理解一切变得极其简单。

## server.js

```
const net = require("net");
const fs = require("fs");

const collections = {};

const port = 3939;
const hostname = "127.0.0.1";

const server = net.createServer();
server.listen(port, hostname, () => {
  console.log("listening on port", port);
});

server.on("connection", (sock) => {
  sock.on("data", (data) => {
    const jsonData = JSON.parse(data);
    const collection = getCollection(jsonData.collection);
    let response = "1";

    if (jsonData.insertOne) {
      const _id = new Date().getTime();

      collection[_id] = {
        ...jsonData.insertOne,
        _id
      }

      saveToFile(jsonData.collection)

      response = JSON.stringify({ insertedId: _id });
    }

    sock.write(response);
  });
});

function getCollection(collectionName) {
  if (!collections[collectionName]) {
    collections[collectionName] = {};
  }
  return collections[collectionName];
}

function saveToFile(collectionName) {
  fs.writeFileSync(`${collectionName}.json`, JSON.stringify(getCollection(collectionName)));
}
```

## client.js

```
const net = require('net')

const port = 3939;
const hostname = '127.0.0.1'

const socket = new net.Socket();

socket.connect(port, hostname, () => {
  socket.write(JSON.stringify({
    collection: "blocks",
    insertOne: {
      body: "Hello World!"
    }
  }))
  socket.on('data', (data) => {
    console.log(String(data));
  })
})
```

## 阅读—实现 findOne

既然我们能够创建文档，自然下一步就是能够获取这些文档。现在，我只关心通过 id 获取它们，所以我实现了一个`findOne`操作。

现在我们有了两个可能的操作，有必要为不同的操作创建辅助函数。虽然旧的`client.js`只能在数据返回时将其注销，但我承诺了请求/响应，这样我就可以从新创建的文档中获取`insertedId`。这样，我就能够查询刚刚创建的特定文档。

## server.js

```
// server.js
const net = require("net");
const fs = require("fs");

const collections = {};

const port = 3939;
const hostname = "127.0.0.1";

const dbFolderName = 'db'

try {
  fs.statSync(dbFolderName)
} catch(err) {
  fs.mkdirSync(dbFolderName)
}

const filenames = fs.readdirSync(dbFolderName);
for (const filename of filenames) {
  const collectionName = filename.split('.')[0]
  const collectionFileContents = fs.readFileSync(`${dbFolderName}/${filename}`);
  if (collectionFileContents) {
    collections[collectionName] = JSON.parse(collectionFileContents)
  }
}

const server = net.createServer();
server.listen(port, hostname, () => {
  console.log("listening on port", port);
});

server.on("connection", (sock) => {
  sock.on("data", (data) => {
    const jsonData = JSON.parse(data);
    const collection = getCollection(jsonData.collection);
    let response = "1";

    if (jsonData.insertOne) {
      const _id = new Date().getTime();

      collection[_id] = {
        ...jsonData.insertOne,
        _id,
      };

      saveToFile(jsonData.collection);

      response = JSON.stringify({ insertedId: _id });
    } else if (jsonData.findOne) {
      const filter = jsonData.findOne.filter;
      if (filter._id) {
        const data = collection[filter._id];

        response = JSON.stringify(data);
      }
    }

    sock.write(response);
  });
});

function getCollection(collectionName) {
  if (!collections[collectionName]) {
    collections[collectionName] = {};
  }
  return collections[collectionName];
}

function saveToFile(collectionName) {
  fs.writeFileSync(
    `${dbFolderName}/${collectionName}.json`,
    JSON.stringify(getCollection(collectionName))
  );
}
```

## client.js

```
const net = require("net");

async function run() {
  const port = 3939;
  const hostname = "127.0.0.1";

  const socket = new net.Socket();

  socket.connect(port, hostname, async () => {
    const { insertedId } = await insertOne(socket, {
      collection: "blocks",
      data: {
        body: "Hello World!",
      },
    });

    const note = await findOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId,
      },
    });

    console.log(note);
  });
}

function insertOne(socket, { collection, data }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        insertOne: data,
      })
    );
  });
}

function findOne(socket, { collection, filter }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        findOne: {
          filter,
        },
      })
    );
  });
}

run();
```

## 更新-实施 updateOne

updateOne 遵循我们对 findOne 操作所做的操作。唯一的区别是它现在随请求一起发送一个`data`属性，这样服务器就可以更新现有的文档。

## server.js

```
const net = require("net");
const fs = require("fs");

const collections = {};

const port = 3939;
const hostname = "127.0.0.1";

const dbFolderName = 'db'

try {
  fs.statSync(dbFolderName)
} catch(err) {
  fs.mkdirSync(dbFolderName)
}

const filenames = fs.readdirSync(dbFolderName);
for (const filename of filenames) {
  const collectionName = filename.split('.')[0]
  const collectionFileContents = fs.readFileSync(`${dbFolderName}/${filename}`);
  if (collectionFileContents) {
    collections[collectionName] = JSON.parse(collectionFileContents)
  }
}

const server = net.createServer();
server.listen(port, hostname, () => {
  console.log("listening on port", port);
});

server.on("connection", (sock) => {
  sock.on("data", (data) => {
    const jsonData = JSON.parse(data);
    const collectionName = jsonData.collection;
    const collection = getCollection(collectionName);
    let response = "1";

    if (jsonData.insertOne) {
      const _id = new Date().getTime();

      collection[_id] = {
        ...jsonData.insertOne,
        _id,
      };

      saveToFile(collectionName);

      response = JSON.stringify({ insertedId: _id });
    } else if (jsonData.findOne) {
      const filter = jsonData.findOne.filter;
      if (filter._id) {
        const data = collection[filter._id];

        response = JSON.stringify(data);
      }
    } else if (jsonData.updateOne) {
      const filter = jsonData.updateOne.filter;
      if (filter._id) {
        collection[filter._id] = {
          ...collection[filter._id],
          ...jsonData.updateOne.data
        };

        saveToFile(collectionName);

        response = "0";
      }
    }

    sock.write(response);
  });
});

function getCollection(collectionName) {
  if (!collections[collectionName]) {
    collections[collectionName] = {};
  }
  return collections[collectionName];
}

function saveToFile(collectionName) {
  fs.writeFileSync(
    `${dbFolderName}/${collectionName}.json`,
    JSON.stringify(getCollection(collectionName))
  );
}
```

## client.js

```
const net = require("net");

async function run() {
  const port = 3939;
  const hostname = "127.0.0.1";

  const socket = new net.Socket();

  socket.connect(port, hostname, async () => {
    const { insertedId } = await insertOne(socket, {
      collection: "blocks",
      data: {
        body: "Hello World!",
      },
    });

    await updateOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId
      },
      data: {
        body: "Goodbye World!"
      }
    })

    const note = await findOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId,
      },
    });

    console.log(note);
  });
}

function insertOne(socket, { collection, data }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        insertOne: data,
      })
    );
  });
}

function findOne(socket, { collection, filter }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        findOne: {
          filter,
        },
      })
    );
  });
}

function updateOne(socket, { collection, filter, data }) {
  return new Promise((resolve) => {
    socket.once("data", () => {
      resolve();
    });

    socket.write(
      JSON.stringify({
        collection,
        updateOne: {
          filter,
          data
        },
      })
    );
  });
}

run();
```

## 删除—实现 deleteOne

现在我们已经完成了一些操作，deleteOne 应该很容易理解，甚至可以自己实现，而无需遵循。它只是从集合中删除对象，并将 json 文件重新保存到磁盘。

## server.js

```
// server.js
const net = require("net");
const fs = require("fs");

const collections = {};

const port = 3939;
const hostname = "127.0.0.1";

const dbFolderName = "db";

try {
  fs.statSync(dbFolderName);
} catch (err) {
  fs.mkdirSync(dbFolderName);
}

const filenames = fs.readdirSync(dbFolderName);
for (const filename of filenames) {
  const collectionName = filename.split(".")[0];
  const collectionFileContents = fs.readFileSync(`${dbFolderName}/${filename}`);
  if (collectionFileContents) {
    collections[collectionName] = JSON.parse(collectionFileContents);
  }
}

const server = net.createServer();
server.listen(port, hostname, () => {
  console.log("listening on port", port);
});

server.on("connection", (sock) => {
  sock.on("data", (data) => {
    const jsonData = JSON.parse(data);
    const collectionName = jsonData.collection;
    const collection = getCollection(collectionName);
    let response = "1";

    if (jsonData.insertOne) {
      const _id = new Date().getTime();

      collection[_id] = {
        ...jsonData.insertOne,
        _id,
      };

      saveToFile(collectionName);

      response = JSON.stringify({ insertedId: _id });
    } else if (jsonData.findOne) {
      const filter = jsonData.findOne.filter;
      if (filter._id) {
        const data = collection[filter._id];

        response = JSON.stringify(data);
      }
    } else if (jsonData.updateOne) {
      const filter = jsonData.updateOne.filter;
      if (filter._id) {
        collection[filter._id] = {
          ...collection[filter._id],
          ...jsonData.updateOne.data,
        };

        saveToFile(collectionName);

        response = "0";
      }
    } else if (jsonData.deleteOne) {
      const filter = jsonData.deleteOne.filter;
      if (filter._id) {
        delete collection[filter._id];

        saveToFile(collectionName);

        response = "0";
      }
    }

    sock.write(response);
  });
});

function getCollection(collectionName) {
  if (!collections[collectionName]) {
    collections[collectionName] = {};
  }
  return collections[collectionName];
}

function saveToFile(collectionName) {
  fs.writeFileSync(
    `${dbFolderName}/${collectionName}.json`,
    JSON.stringify(getCollection(collectionName))
  );
}
```

## client.js

```
// client.js
const net = require("net");

async function run() {
  const port = 3939;
  const hostname = "127.0.0.1";

  const socket = new net.Socket();

  socket.connect(port, hostname, async () => {
    const { insertedId } = await insertOne(socket, {
      collection: "blocks",
      data: {
        body: "Hello World!",
      },
    });

    await updateOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId
      },
      data: {
        body: "Goodbye World!"
      }
    })

    const note = await findOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId,
      },
    });

    await deleteOne(socket, {
      collection: "blocks",
      filter: {
        _id: insertedId
      }
    })
  });
}

function insertOne(socket, { collection, data }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        insertOne: data,
      })
    );
  });
}

function findOne(socket, { collection, filter }) {
  return new Promise((resolve) => {
    socket.once("data", (data) => {
      resolve(JSON.parse(String(data)));
    });

    socket.write(
      JSON.stringify({
        collection,
        findOne: {
          filter,
        },
      })
    );
  });
}

function updateOne(socket, { collection, filter, data }) {
  return new Promise((resolve) => {
    socket.once("data", () => {
      resolve();
    });

    socket.write(
      JSON.stringify({
        collection,
        updateOne: {
          filter,
          data
        },
      })
    );
  });
}

function deleteOne(socket, { collection, filter }) {
  return new Promise((resolve) => {
    socket.once("data", () => {
      resolve();
    });

    socket.write(
      JSON.stringify({
        collection,
        deleteOne: {
          filter
        },
      })
    );
  });
}

run();
```

## 包裹

多年来，我一直在考虑建立一个数据库的想法。我已经开始和停止这些项目几次了。我认为真正深入了解它的唯一方法是在实际应用中开始使用它。从这里开始，我将转换我现有的 [engram](https://github.com/engramhq/engram) 应用程序来使用这个数据库，而不是它当前的 mongodb。我希望这样做将自然地引入构建数据库时存在的一些挑战，以便我可以更好地了解幕后真正发生的事情。