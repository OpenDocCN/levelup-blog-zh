# JavaScript 中的异步编程

> 原文：<https://levelup.gitconnected.com/asynchronous-programming-in-javascript-9fff87cb7df1>

## 异步/等待的最佳用途

> 学习一门新的编程语言的唯一方法是用它写程序——丹尼斯·里奇

计算机编程中的异步是指独立于主程序流的事件的发生以及处理这些事件的方式。

![](img/a10d1daa93dbe2572a4f54919d00cf83.png)

图片由[内森·安德森](https://unsplash.com/@nathananderson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/parallel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

与编程语言 Java/C#不同，JavaScript 是单线程的，所有代码都是按顺序逐行执行的，而不是并行执行的。通常，我们希望事件的发生独立于主程序流。这可以在 JavaScript 中使用回调、承诺、异步/等待来实现。

Async/await 是一种编写异步代码的新方法，异步代码的其他替代方法是回调或承诺。

> *Async/await 使异步代码看起来和行为都不像同步代码。*

在这篇文章中，我将尝试解决

1.  什么是异步/等待
2.  异步/等待工作
3.  承诺并发
4.  异步并行执行

# 异步ˌ非同步(asynchronous)

异步函数声明定义了一个异步函数，一个返回对象的函数。这些函数使我们能够编写基于承诺的代码，而不会阻塞执行线程。它们通过事件循环以不同于其余代码的顺序运行，返回一个承诺作为结果。

```
async function GetText() {
 return ”Hello”;
 }

 GetText().then(alert); // Hello
```

如果你观察到上面运行的 GetText()返回一个 promise 并使用 then 调用。

# 等待

异步函数可以包含 await 表达式，await 会暂停异步执行，直到相关的承诺被解析。Await 使异步块等待，但不是整个执行块。Await 只能在异步函数中使用。

```
function getData() {
   return new Promise(resolve => {setTimeout(() =>     {resolve(‘resolved’);}, 2000);});}async function asyncGet() {console.log(‘calling’);
const result = await getData();
console.log(result);// expected output: ‘resolved’}asyncGet();
```

通过运行上面的代码，我们可以观察到异步函数代码因为 await 关键字而暂停了 2 秒钟。一旦 getData()承诺得到解决，我们就可以在异步函数中看到期望的输出。

我们太担心语法和它的酷了。让我们更深入地研究示例，并开始使用 async/wait。

# 异步/等待工作

在整篇文章中，我们将使用一个制作午餐的指令示例来了解 async/await 如何简化一系列异步操作。这是做午餐的说明清单。

![](img/3a041066f1a04dc8ac9320bc4af37abf.png)

图片由 [Unsplash](https://unsplash.com/s/photos/lunch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[雅各布·卡普斯纳克](https://unsplash.com/@foodiesfeed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

1.倒辣花生汤。

2.准备蔬菜沙拉。

3.准备炒鸡蛋。

4.炸汉堡小面包。

5.在汉堡中加入蔬菜。

6.倒一杯柠檬汁。

如果您有做午餐的经验，您可以异步执行这些指令。烹饪午餐是一个很好的非并行异步工作的例子。一个人(或线程)可以处理所有这些任务。在午餐类比中，一个人可以通过在第一个任务完成之前开始下一个任务来异步制作午餐。不管有没有人看，烹饪都会进行下去。一旦你开始切蔬菜做沙拉，你就可以做炒鸡蛋了。一旦开始争抢，你就可以在锅上煎馒头了。

让我们用 JavaScript 写所有的指令。

顺序

JavaScript 解释这些指令的方式与人们不同。JavaScript 将在每条语句上阻塞，直到工作完成，然后继续下一条语句。这就造成了一顿不令人满意的午餐。在前面的任务完成之前，后面的任务不会开始。做午餐要花更长的时间，而且一些食物在端上来之前就已经凉了。

如果您想异步执行这些指令，应该编写异步代码。

前面的代码演示了一个不好的做法:构造同步代码来执行异步操作。如前所述，这段代码阻止执行它的线程做任何其他工作。让我们从更新这段代码开始，这样线程就不会在任务运行时阻塞。await 关键字提供了一种非阻塞方式来启动任务，然后在任务完成时继续执行。

异步的

当沙拉或炒鸡蛋正在烹饪时，上面的代码不会阻塞。但是这段代码不会启动任何其他任务。你还是会在锅里煎小面包，然后盯着它直到它被煎熟。但至少，你会回应任何想引起你注意的人。现在，处理午餐的线程在等待任何尚未完成的已启动任务时不会被阻塞。

# 承诺并发

在许多情况下，您希望立即开始几个独立的任务。然后，随着每项任务的完成，你可以继续其他准备好的工作。承诺使您能够编写更类似于创建午餐的代码。你会开始做沙拉，炒鸡蛋，同时煎小面包。因为每一个都需要行动，你会把注意力转移到那个任务上，关注下一个行动，然后等待其他需要你注意的事情。

你可以为每项任务创建一个承诺，然后等待每项任务的结果。

并发

如果您观察上面的代码，您可以立即启动所有异步任务，并且仅在需要结果时才等待每个任务。这就像构建一个 web 应用程序的代码发出几个 web APIs 的请求，然后将结果组合成一个结果。

这段代码是异步的，就像一个人做午餐一样。

# 异步并行执行

Async await 使执行顺序化，但是使用 Promise.all 并行执行可以使执行速度更快。

在上面的例子中，我们可以看到，准备沙拉、炒鸡蛋和面包是相互独立的，可以并行执行。

## 通常的方法

```
async function do() {
 await saladPromise; // Wait 50ms
 await sEggPromise; // then wait another 50ms.await burgerPromise; // wait for 50ms
 return “done!”;
 }
```

上面的代码需要 150 毫秒的时间，对于一个构建良好的 web 应用程序来说，这是一段很长的时间。这是因为所有的承诺都是按顺序一个接一个执行的，只有先承诺后兑现。这不是一个好的做法，而且费时。

我们可以使用 Promise.all()来克服上述挑战。

## 使用 Promise.all

**Promise.all()** 方法返回单个承诺，当作为可迭代对象传递的所有承诺都已解析时，或者当可迭代对象不包含承诺时，该承诺将被解析。它以拒绝的第一个承诺的理由拒绝。

# 包扎

Async/Await 是 JavaScript 引入的新特性，可以像编写同步代码一样简单地编写异步代码。它还提供了以并行和并发方式执行功能的可行性。当您从 web API 检索数据或加载任何图像或任何异步用例时，您可以开始实现这个特性。

到目前为止，我们看到了更多关于 Async/Await 的内容，并且对其进行了总结。请仔细阅读下面的文章，对承诺有一个清晰的了解。

[](/javascript-promises-are-an-ideal-solution-for-asynchronous-programming-e9e530ce4f8c) [## JavaScript 承诺:异步编程的理想解决方案

### 保证！！！这都是关于承诺

levelup.gitconnected.com](/javascript-promises-are-an-ideal-solution-for-asynchronous-programming-e9e530ce4f8c) 

> 希望你喜欢它！！！请对任何疑问或建议发表评论！！！！