# 如何在 Node.js 中实现队列

> 原文：<https://levelup.gitconnected.com/how-to-implement-queues-in-node-js-8b3a06ce0dd0>

## Javascript 编程

## 如何用 Bull/Redis/Node.js/Javascript 实现作业队列的演示

![](img/06f8647721e4100136555f2f5fbce98d.png)

[freestocks.org](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

队列可以以一种优雅的方式解决许多不同的问题，从消除处理高峰到在微服务之间创建健壮的通信通道，或者将繁重的工作从一个服务器卸载到许多较小的工作人员，等等。那么，如何在 JavaScript 世界中实现这一点呢？

答案是扯淡。 [Bull](https://github.com/OptimalBits/bull) 是一个节点库，基于 [Redis](https://redis.io/) 实现了一个快速健壮的队列系统。在我看来，如果您想利用队列机制，这可能是 Node.js 的最佳解决方案。

一个 bull 队列实例通常有 3 个主要的不同角色:一个作业生产者、一个作业消费者和/或一个事件监听器。

> **生产者**:向队列添加作业的节点程序
> **消费者**:定义流程函数的节点程序，作业内容
> **事件监听器**:你可以只监听队列中发生的事件，对不同的事件进行不同的处理。

# 公牛队列实现

## 目的:

为作业创建一个队列解决方案，以便将文件上传到互联网。该 API 由第三方实施。我们需要创建一个可以调用这个 API 的作业，并将作业放入队列中；此外，我们必须保持接口开放给更多的工作/队列在未来。

```
const uploadFiles = require("../../../operations/uploadFiles");
```

## 解决方案结构:

典型的多头队列解决方案结构如下所示:

```
├── queues
│   ├── index.js
│   └── process.js
└── tasks
    └── file
        ├── processor.js
        ├── queue.js
```

**queues/index.js** 是**声明**队列的节点代码(在我们的例子中，只有一个队列叫做“UPLOAD_FILE_BY_ID”)。 ***第一部制片人。***

**queues/process.js** 是通过将队列与其处理器链接来定义流程的地方。换句话说，一个队列(例如通过 ID 上传文件)专门负责一种类型的作业(通过 ID 上传文件，不通过名称上传文件，等等)。但是，就业岗位的数量会很多。此外，它是定义侦听器的地方，这意味着如何反映队列中作业的不同状态。 ***听者***

**tasks/file/processor . js**是做真正工作的节点代码，比如用某些参数调用/触发 API。 ***处理器***

**task/file/queue.js** 是 app 在队列外调用的节点代码，触发生产者添加作业。换句话说——**特定队列的 API/entry 和生产者的第二*部分。***

## 履行

***监制***

我使用 2 个文件来完成定义，而不是 1 个。原因是我们需要创建一个入口，让对方 app 可以调用 job add 函数；更重要的是，当队列的数量变得越来越大时，通过这种方式，解决方案更容易维护。

这里的代码也是一个重用 Redis 连接的例子，因为我的解决方案将部署在 Heroku 上，更多详细信息，请参考[https://github . com/optimal bits/bull/blob/master/patterns . MD # reuse-Redis-Connections](https://github.com/OptimalBits/bull/blob/master/PATTERNS.md#reusing-redis-connections)

队列/index.js

任务/文件/队列. js

***消费者***

下面是一份工作的真实内容。我们可以看到，它基本上是从 uploadFiles 调用另一个 API 来按 ID 上传文件。值得注意的是，异步函数更好。

处理器. js

***听者***

将最大侦听器增加到 50，以避免 EventEmitter 内存泄漏警告；

为 3 种类型的事件定义处理程序。

使用数组来保持定义过程的接口对更多队列开放。换句话说，我们构建了一个队列引擎。

js 通过将处理器链接到 queue.process()来定义处理器/工作者

## 注意事项:

*   在上面的例子中，我们将过程函数定义为`async`，这是一种强烈推荐的定义方法。如果您的节点运行时不支持 async/await，那么您可以在流程函数结束时返回一个承诺来获得类似的结果。
*   我们使用两个文件来完成 worker/processor 的定义，它们是 process.js 和 processor.js。
*   下面是侦听器的示例，很容易看出，当前这个队列只处理 3 种类型的事件:失败、完成和停止。

```
// here are samples of listener events : //"failed","completed","stalled", the other events will be ignored  queue.on("failed", failHandler);  
queue.on("completed", completedHandler);  
queue.on("stalled", handleStalled);// link the correspondant processor/worker  
queue.process(processor);
```

# 运行队列

我们现在可以使用以下命令来运行/开发/调试队列

```
"process-queues": "node ./src/jobs/queues/process.js","dev-queues": "nodemon ./src/jobs/queues/process.js","debug-queues": "node --nolazy --inspect-brk=9242 ./src/jobs/queues/process.js"
```

更多 API 参考详情，请参考[https://github . com/optimal bits/bull/blob/master/reference . MD](https://github.com/OptimalBits/bull/blob/master/REFERENCE.md)

更多关于更通用的公会详情，请参考[https://optimalbits.github.io/bull/](https://optimalbits.github.io/bull/)