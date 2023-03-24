# JavaScript 中的异步编程

> 原文：<https://levelup.gitconnected.com/asynchronous-programming-in-javascript-27c554d5a718>

![](img/cf81168aab6d2b7449a039e43567a8c9.png)

eberhard grossgasteiger 在 [Unsplash](https://unsplash.com/s/photos/bulb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当一个程序被处理器执行时，它直接运行，一次只发生一件事。有许多程序与处理器之外的东西进行交互，比如网络请求、从硬盘读取的数据。在这个计算机拥有多个处理器内核的时代，当您可以让另一个任务在另一个处理器内核上执行并让您知道它何时完成时，坐在那里等待是没有意义的。

这让我们可以同时完成其他工作，这是**异步编程的基础。**由我们使用的编程环境(在我们的例子中是 web 浏览器/节点)为我们提供 API，允许我们异步运行这些任务。

在 JavaScript 代码中可以遇到两种主要类型的异步代码风格:旧式回调和新式承诺式代码。让我们依次回顾一下这些问题。

**回调:**

回调是一个在另一个函数完成执行后执行的函数，因此被称为“回调”。

它是作为参数传递给另一个函数的函数，稍后执行。

将回调函数作为参数的函数称为高阶函数。

回调是一种确保某个代码在另一个代码完成执行之前不会执行的方法。

理想情况下，执行异步操作的函数需要一个额外的参数，一个*回调函数*。当操作完成时，回调函数被调用并得到结果。

让我们看一个例子:

```
fs.readFile('/input.json', function (error, data) {
  if(error) {
    //handle error logic
    console.log(error);
    return;
  }//no error process data
  console.log(data);
})
```

在上面的代码片段中，我们使用了 fs 类中的 readFile 方法(作为节点 API 提供)。此方法异步读取文件。它接受一个回调函数作为参数(这里是第二个参数),当文件读取完成或者过程中有错误时，调用这个函数。

只有当一个回调函数将另一个回调函数作为参数创建了一个嵌套的回调结构(称为“ **Callback Hell** ”)时，回调的问题才会出现。随着嵌套数量的增加，管理和理解变得越来越困难。它看起来像这样。

```
firstFunction(args, function() {
  secondFunction(args, function() {
    thirdFunction(args, function() {
      // And so on…
    });
  });
});
```

**承诺:**

承诺是未来可能实现的价值的第一代表。

它允许我们将处理程序与异步操作的最终成功值或失败原因相关联。

这使得异步方法像同步方法一样返回值:异步方法不是立即返回最终值，而是返回一个在未来某个时间提供该值的承诺。

一个很好的例子是 fetch() API，它是基于回调的旧 XMLHttpRequest 的现代版本。

```
fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'))
  .then(response => response.json())
  .then(json => console.log(json))
  .catch(function(error) {
    console.log('Fetch problem: ' + error.message);
  });
```

上面的 fetch()将我们希望从网络上获取的资源的 URL 作为单个参数，并返回一个承诺。只有当我们从服务器获得响应时，才会执行 then 块中的代码。

一个承诺在任何时候都可以有三种状态中的任何一种:

**待定:**初始状态，既不履行也不拒绝。

**完成:**表示操作成功完成。(然后执行 then 块中的代码)

**拒绝:**表示操作失败(执行 catch 块内的代码)

我们还可以使用 **Promise** 构造函数在 JavaScript 中创建自己的 Promise 对象。

```
function squareNumberAsync(input) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
       if (input) {
         resolve(input * input);
        } else {
         reject("Please provide an input.");
        }
      }, 3000);
    });
 }
```

在 squareNumberAsync 函数中，我们确保返回一个新的承诺。Promise 构造函数将一个名为 executor 的函数作为输入。执行器函数需要两个参数(这里是 resolve 和 reject)

如果异步代码已经成功执行，我们将调用 *resolve* 。这使得承诺状态被**解析**并在消费站点执行**然后** **块**中的代码。

如果出现错误，我们将调用*剔除*功能。这使得承诺状态为**拒绝**并在消费站点执行 **catch 块**内的代码。

当承诺被解决(解决/拒绝)时，finally 块被调用。

消费网站:

```
squareNumberAsync(50)
 .then((result) => {
    console.log(result);
 })
 .catch((error) => {
    console.log(error);
 })
 .finally(() => {
    console.log("Completed");
 });
```

**异步/等待**

async 和 await 关键字使异步的、基于承诺的行为能够以更简洁的方式编写，避免了显式配置承诺链的需要。

async/await 的目的是简化使用基于承诺的 API 所需的语法。

异步函数总是返回一个**承诺**。如果异步函数的返回值不是显式的承诺，它将被隐式地包装在承诺中。

```
*async function foo() 
{ return 1}**//The above function works same as the below
function foo()
{ return Promise.resolve(1) }*
```

异步函数可以包含零个或多个 await 表达式。

await 关键字只在异步函数中有效。如果你在一个异步函数体之外使用它，你会得到一个语法错误。

关键字 await 让 JavaScript 一直等到承诺完成并返回结果。

例如，我们有两个网络 API 调用。第二个 API 调用依赖于第一个 API 调用的响应。

如果我们不使用 async/await 来编写它，它看起来会像下面这样。我使用 Axios HTTP 客户端库来处理网络 API 请求。

```
function fetchFirstUserDetails() {
  axios.get('[https://jsonplaceholder.typicode.com/users'](https://jsonplaceholder.typicode.com/users'))
    .then((usersResponse) => {
      //passing the first user id to second api call
         axios.get(`[https://jsonplaceholder.typicode.com/users/${usersResponse.data[0].id}`](https://jsonplaceholder.typicode.com/users/${usersResponse.data[0].id}`))
        .then((userResponse) => console.log(userResponse.data))
        .catch((error) => console.log(error));
    })
    .catch((error) => console.log(error));
};
```

我们需要将第二个 API 调用嵌套在第一个 API 调用的 then 块中。

如果我们使用 async/await，它会使我们的代码看起来更简单，如下所示。

```
async function fetchFirstUserDetails() {
  try {
    let usersResponse = await     axios.get('[https://jsonplaceholder.typicode.com/users'](https://jsonplaceholder.typicode.com/users'));
    let userResponse = await axios.get(`[https://jsonplaceholder.typicode.com/users/${usersResponse.data[0].id}`](https://jsonplaceholder.typicode.com/users/${usersResponse.data[0].id}`));
    console.log(userResponse.data);
  } catch (error) {
    console.log(error);
  }
}
```

异步函数中的 await 表达式等待一个承诺被解决。

如果承诺实现了，它就继续执行。

如果承诺被拒绝，await 表达式将抛出被拒绝的值。

我们应该始终确保在 try-catch 块中编写 await 表达式，以便捕获被拒绝的值。

暂时就这些了…谢谢。

## **参考文献:**

[https://developer . Mozilla . org/en-US/docs/Learn/JavaScript/Asynchronous/Introducing](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Introducing)