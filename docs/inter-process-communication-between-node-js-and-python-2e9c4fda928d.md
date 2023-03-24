# Node.js 和 Python 之间的进程间通信

> 原文：<https://levelup.gitconnected.com/inter-process-communication-between-node-js-and-python-2e9c4fda928d>

带有命名管道的 IPC

![](img/c741c0bf71d334afcc852b0e89a586dc.png)

最近，我遇到了一个用例，涉及运行 PyTorch 模型进行实时推理，以及处理 I/O 的 Node.js 服务器，在最初尝试使用 torch.js 失败后，我们决定使用 Tensorflow.js (tfjs)。然而，[层模型](https://www.tensorflow.org/js/guide/models_and_layers)是一个同步且阻塞的进程，它强制使用工作线程(这条路线将在另一篇文章中详述)。这种方法有其自身的困难:(1)tfjs 特定工作线程需要单独运行。(2)通过 WebGL 后端使用 GPU 并不简单。

PyTorch 便于 AI 开发，具有使用 Python 的易用性。此外，PyTorch 提供了一个很好的 [C++前端](https://pytorch.org/tutorials/advanced/cpp_frontend.html)，这对低延迟系统特别有吸引力。对于上面的用例，我们决定继续使用 PyTorch。Node.js 处理输入和输出，而 Python/PyTorch 将处理 AI 模型推理。这两个系统将使用命名管道进行通信。

## 命名管道

> “管道”是 Linux 的一个基本特性，它允许不同的进程相互通信和传递数据。命名管道(使用文件系统)是可以由两个不相关的进程访问的文件；一个是读者，另一个是作家。更多细节可以在[这里](https://www.linuxjournal.com/article/2156)找到。命名管道有时被称为 FIFOs:先进先出，因为字节顺序(进入和出来)是保留的。

# 简化问题陈述…

目标是使用命名管道进行通信。

1.  Node.js 进程将一些数据写入管道(A)
2.  Python 进程从管道(A)读取数据并操作数据
3.  然后，上面的 python 进程将数据写入管道(B)
4.  Node.js 从管道(B)中读取

## Python:读取、处理和写入

首先，让我们创建一个简单的 Python 进程，它从命名管道 A 读取数据，处理数据，然后将其写入命名管道 B(为了简单起见，这里的`process_msg()`函数返回读取的数据)。该脚本首先使用`os.mkfifo()`命令创建命名管道 A。

## Node.js:写和读

接下来，让我们编写一个简单的 Node.js 程序来写入管道。有几个包可以做到这一点:[命名管道](https://www.npmjs.com/package/named-pipe)， [fifo-js](https://github.com/raksooo/fifo-js) 。但是，node 提供了一些功能，可以方便地使用管道设置 IPC。下面的脚本每 1 秒钟将数据写入管道 A(发送数据)，并从管道 B 读取数据(从 Python 接收处理后的数据)。为简单起见，这里的数据是字符串形式的当前时间。

注意，虽然`createReadStream`和`createWriteStream`看起来有同步接口，但它们是异步操作:当操作完成时，它们不返回承诺或接受回调来进行通信。更多细节可以在[这个 stackoverflow 回答](https://stackoverflow.com/a/30386838/3449335)中找到。

*接下来*，启动两个终端，一个用于 Python，另一个用于 Node.js，流水线增加了大约 1ms 的延迟。

# 结论

命名管道为进程间的通信提供了一种便捷的方式。我介绍了一种简单的方法，将数据从 Node.js 线程传递到可以进行数据操作的 Python 进程。对于较慢的任务，Python 中可以使用多线程分别读写。

感谢阅读！