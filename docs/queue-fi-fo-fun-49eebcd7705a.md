# 排队取乐

> 原文：<https://levelup.gitconnected.com/queue-fi-fo-fun-49eebcd7705a>

## 在 TypeScript 和 JavaScript 中实现基本队列

> *我看一个程序员的代码
> 好不好；或者是坏的
> 增加一个队列会让我高兴*

在实现优化时，软件开发人员可能会遇到超出特定功能或模块的性能问题。体系结构中的一个潜在问题，两个模块紧密耦合，但是具有非常不同或不可预测的执行速度。

代码不必是一组线性的操作，事实上，如果没有一个简单的设计模式:队列，你的操作系统会运行得更慢。

![](img/879cdcefd56c1a96be0fb977a23f0286.png)

照片由[micha Parzuchowski](https://unsplash.com/@mparzuchowski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 什么？为什么？

想象一下在一家受欢迎的甜甜圈店外面有一队人。他们每个人都急切地等待着热豆水和环形糕点。这是一个队列。你已经看到了。

顾客可以很快实现他们的目的，付款的速度比准备食物的速度快得多。如果我们没有排队，一旦有人走进商店提出要求，宇宙将不得不尴尬地等待他们完成交易。没有队列的后果在日常生活中是站不住脚的。在软件中，它也会产生类似的结果。

现在，假设一个函数向服务器发送日志，并在完成之前等待日志被发送。该函数还负责处理服务器请求中的错误。在本例中，该函数的目的最多是通过添加日志记录和错误处理来实现的。在最坏的情况下，该函数的性能很差，因为它必须等待日志被发送。

如果实现了队列，它将提取日志记录和交付的责任。它还可以接受来自任何函数的日志，区分错误日志的优先级，并处理远程日志服务器不可达的情况。

# 创建队列

**注意:下面的代码是 TypeScript 格式的，但是最终代码的 JavaScript 版本可以在底部找到。**

创建队列的方法有很多，下面的代码只是一个实现，它为队列是什么以及如何使用队列奠定了概念基础。

让我们从队列的三个有状态元素开始。

*   队列本身
*   将为队列中的每个项目调用的回调
*   表示队列当前是否正在处理的标志

```
class Queue<T> {
  private running: boolean = false;
  private queue: T[] = []; constructor(private callback: (item: T) => Promise<void>) {}
}
```

# 处理队列

通常有两种方法处理队列，通过循环或递归。以下示例使用了一个递归函数:

1.  检查队列是否为空
2.  检索队列中的第一项，并将其从队列中移除
3.  用该项调用回调
4.  返回对自身的调用

```
private async processNext(): Promise<void> {
  if (this.queue.length === 0) return;
  const item = this.queue.shift() as T;
  await this.callback(item);
  return this.processNext();
}
```

# 运行队列

如果队列正在被处理，不要启动另一个处理实例。如果队列没有被处理，将运行标志设置为`true`并开始处理队列。加工完成后，将运行设置为`false`。

```
private async run() {
  if (this.running) return;
  this.running = true;
  await this.processNext();
  this.running = false;
}
```

# 挤到队列中

不出所料，当我们队列上的 push 方法被调用时，将项目推到内部队列上，然后调用`run`。

```
push(item: T) {
  this.queue.push(item);
  this.run();
}
```

# 所有放在一起

```
class Queue<T> {
  private running: boolean = false;
  private queue: T[] = [];constructor(private callback: (item: T) => Promise<void>) {}private async processNext(): Promise<void> {
    if (this.queue.length === 0) return;
    const item = this.queue.shift() as T;
    await this.callback(item);
    return this.processNext();
  }private async run() {
    if (this.running) return;
    this.running = true;
    await this.processNext();
    this.running = false;
  }push(item: T) {
    this.queue.push(item);
    this.run();
  }
}
```

## 在 JavaScript 中

```
class Queue {
  constructor(callback) {
    this.callback = callback;
    this.running = false;
    this.queue = [];
  }
  async processNext() {
    if (this.queue.length === 0)
      return;
    const item = this.queue.shift();
    await this.callback(item);
    return this.processNext();
  }
  async run() {
    if (this.running)
      return;
    this.running = true;
    await this.processNext();
    this.running = false;
  }
  push(item) {
    this.queue.push(item);
    this.run();
  }
}
```

# 扩展功能

*   **重试**。在处理远程服务中的不可预测性时很有用。可以将项目推回到队列中再次尝试。向项目添加重试计数器可以限制重试次数。还应该添加一些逻辑来处理当达到该限制时会发生什么。本文也可能对这种情况有用:[重试和带承诺的指数回退](https://medium.com/swlh/retrying-and-exponential-backoff-with-promises-1486d3c259)。
*   **推迟回调注册**。当回调在程序中的某个点之前是未知的时，这很有用。这样，函数可以推入队列，然后继续前进。一旦注册了回调，就可以处理队列。
*   **限制队列大小**。有助于防止队列占用太多内存。
*   **任务优先级**。当某些任务需要优先处理时，这很有用。这可以通过拥有多个队列或者通过评分并将项目插入到排序队列中来实现。

# TL；速度三角形定位法(dead reckoning)

队列是一种工具，可以用来分离运行速度快的模块和运行速度慢的模块。

*来源于:*

 [## 排队取乐

### 我看一个程序员的代码好不好；或者在实现时添加一个队列会让我很高兴…

www.bayanbennett.com](https://www.bayanbennett.com/posts/queue-fi-fo-fun)