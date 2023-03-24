# 用 NodeJS 多线程是现实！

> 原文：<https://levelup.gitconnected.com/multithreading-with-nodejs-is-reality-10871986b8a9>

## Javascript 以单线程语言而闻名，但是现在有办法解决这个问题

![](img/c713229635f92e8d7ef67eaf391daee7.png)

照片由 [Surene Palvie](https://www.pexels.com/@surene-palvie-1075224?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/four-green-yarns-on-chopping-board-2062061/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

Javascript 是一种很棒的编程语言，但是长期以来，Javascript 的一个主要问题是它是单线程的。

在前端，我们可以访问 web worker API，这是一个标准的 API，允许在浏览器端使用多线程。

在后端，这是另一个故事。对于版本 10。x 带来了一个名为`worker thread`的新模块，作为实验特性和版本 12。x 使其稳定。

> 请记住，异步函数不是 JS 中的线程。这是一个常见的错误。

## 它是如何工作的？

NodeJS 团队让它变得非常简单。你需要一个用 ES5 Javascript 编写的入口文件(ES6+不被支持，你不能在你的工作线程直接或间接使用的文件上使用 Babel/Typescript)

您必须从`worker_threads`模块导入属性`Worker`并实例化它。使用从 package.json 位置到条目文件的绝对路径(这里不能使用相对路径)。

# 一些练习

## 基本设置

首先，确保至少在 12 版本上。NodeJS 的 x。您可以通过运行命令`node -v`进行检查

让我们通过在一个空文件夹中运行命令`npm init -y`来建立一个 npm 项目。

然后在一个`src`文件夹中，创建两个文件，`index.js`和`thread-entry.js`这是我们将要使用的两个文件。

最后，在您的 package.json 中，添加以下脚本:
`"start": "node ./src/index.js"`

目前，我们只是在线程入口文件中记录一些东西，只是为了确保我们的线程被正确初始化。
只需在`thread-entry.js`文件中添加一个`console.log("Hello world");`。

现在，让我们开始我们的工人。请记住，我们必须只与 ES5 代码。所以这里没有进口，只有要求！

```
// index.js
const { Worker } = require("worker_threads");(function () {
  new Worker("./src/thread-entry.js")
})()
```

我们正在从`worker_threads`导入`Worker`，它是一个类。

然后，我们创建一个自动执行的函数，该函数实例化 Worker 并给出线程条目文件的路径作为参数。

> 注意我们是如何从项目的根开始的，尽管文件就在索引旁边。如果你尝试键入`./thread-entry.js`，你的程序将不会运行。

使用`npm start`运行你的程序，如果一切正常，你应该会在控制台上看到`Hello world`！

## 处理线程退出

当运行一个线程时，父线程需要知道线程什么时候完成了它的工作。为此，我们将向 worker 实例添加一个处理程序。

首先，让我们编辑我们的`index.js`文件，我们将让我们的函数返回一个承诺，并在线程关闭时解决它。

此外，nodejs 不会等待这个承诺得到解决，而是退出。我们必须建立一个运行的函数，直到这个承诺实现

```
const { Worker } = require("worker_threads");

let canRun = true;

(function () {
  return new ***Promise***((resolve) => {
    const worker = new Worker("./src/thread-entry.js");
    worker.on("exit", () => {
      ***console***.log("Closing");
      resolve()
    });
  })
})().then(() => {
  canRun = false;
})

function infiniteLoop() {
  ***setTimeout***(() => {
    if (canRun) {
      infiniteLoop();
    }
  }, 1000)
}

infiniteLoop();
```

开始了。如您所见，我们编辑了函数，现在返回一个承诺。然后，我们在 close 消息上添加一个事件监听器，直接解析这个承诺。

一旦承诺完成，我们就将变量`canRun`设置为 false，这样无限循环就停止了。

对于无限循环，一个简单的`setTimeout`递归调用相同的函数就可以完成这项工作！

在线程方面，我们必须从`worker_thread`包中导入`parentPort`。这使我们能够与父母沟通，并向其发送消息。

目前，我们只是关闭线程并通知父线程，以便它可以解决承诺。

```
// thread-entry.js
const {***parentPort***} = require("worker_threads");

***console***.log("Hello world");

***parentPort***.close();
```

如果一切正常，运行程序后，您的控制台将显示

```
Hello world
Closing
```

你的程序也应该关闭了！

## 将消息发回给家长

向父节点发送消息允许您在处理完数据后将数据发送回父节点。尽管请记住这些数据是深度复制的，因此，您需要考虑是否值得使用线程！

为了将消息发送回父对象，我们将使用来自同一个`parentPort`对象的函数`postMessage`。

首先，让我们在 worker 对象上添加一个新的处理程序。将这段代码添加到实例化工人的承诺中

```
// index.jsworker.on("message", (value) => {
  ***console***.log("message received: ", value);
})
```

这个句柄将打印接收到的值。

然后，在我们的`thread-entry.js`中添加下面一行

```
***parentPort***.postMessage("Hello from the other side")
```

如果您运行您的程序，这一新行将出现在您的控制台中

```
message received:  Hello from the other side
```

开始了。我们收到了来自线程的消息！

## 运行多个线程

当然，一个线程本身是没有意义的，你要同时运行多个线程！我们要做的是在`index.js`文件中编辑更多的主函数。这个函数将访问大量线程并实例化所有线程。然后，我们将等待他们完成之前，完成该计划。

我们的新 index.js 文件现在看起来像这样

```
//index.js// imports and canRunconst THREADS_AMOUNT = 4;
(function () {
  // Array of promises
  const promises = [];
  for (let idx = 0; idx < THREADS_AMOUNT; idx += 1) {
    // Add promise in the array of promises
    promises.push(new ***Promise***((resolve) => {
      const worker = new Worker("./src/thread-entry.js");
      worker.on("exit", () => {
        ***console***.log("Closing ", idx);
        resolve()
      });
      worker.on("message", (value) => {
        ***console***.log(`message received from ${idx}: ${value}`);
      })
    }))
  }
  // Handle the resolution of all promises
  return ***Promise***.all(promises);
})().then(() => {
  canRun = false;
})// infinite loop
```

这里，我们将实例化 4 个线程。我们还编辑了日志，以便知道哪些线程发送了哪些消息。

在这一点上，当你运行程序的时候，你将会有日志告诉你哪些线程是相关的。此外，顺序可能有所不同。这是完全正常的，证明了它是并行运行的(如果顺序没问题，再次运行您的程序，您最终会看到顺序发生了变化)

## 向线程发送参数

首先，Javascript 线程不像在其他编程语言如 C、C++或 Java(仅引用它们)上那样工作，因为在这里并发不是一件事情。你要传递给线程的参数，将会是一个深层拷贝。不需要担心访问这些参数！但是，在 JS 中深度复制对象也是很耗费资源的，永远不要忘记这一点，想想它的价值。

在线程中发送参数超级简单！你所要做的就是使用 Worker 构造函数的第二个参数并填充一个名为`workerData`的对象

```
const worker = new Worker("./src/thread-entry.js", {
  workerData: {
    workerId: idx
  }
});
```

您的 worker 的新实例现在应该看起来像这样。我们作为参数 workerId 发送，这个 Id 将是 for 循环的索引。

在线程端，我们将获取这个 id，并通过消息将其发送回父线程。为此，我们从`worker_threads`包中导入对象`workerData`。`workerData`将会是与你之前在`index.js`
中发送的对象完全相同的对象。我们只需要从它那里获得 workerId 并编辑 postMessage 来发送 Id(我们已经有足够的 console.log)

```
// thread-entry.jsconst {***parentPort***, ***workerData***} = require("worker_threads");

***console***.log("Hello world");

***parentPort***.postMessage(`ThreadId: ${***workerData***.workerId}`)

***parentPort***.close();
```

这是`thread-entry.js`文件的最终外观。现在，如果您启动您的程序，您将在控制台中看到一些变化。还要确保 workerId 对应于 idx。

# 小心工作线程

Worker threads 是一个很好的工具，但是请记住 NodeJS(和 V8 VM)也在做一些多线程和优化工作。始终对您的代码进行基准测试，以确保最佳实践。

线程也很难维护，这也是实现它之前要考虑的事情。你的代码比以前快了 400 毫秒，但是值得吗？

每个技术选择都必须在一些概念验证和基准测试之后完成，以确保您的方法正确！

我希望你喜欢读这篇文章，就像我喜欢写它一样！

如果你有任何问题，你可以访问我的 github 查看最终代码。

如果你想看看多线程提高 NodeJS 执行速度的例子，你可以看看[这个报告](https://github.com/psyycker/gameOfLifeGenerator)。

感谢您的阅读！

雷米