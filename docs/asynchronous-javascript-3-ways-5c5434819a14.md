# 异步 JavaScript 3 种方式

> 原文：<https://levelup.gitconnected.com/asynchronous-javascript-3-ways-5c5434819a14>

## 回调、承诺和异步/等待

![](img/3f3b9aa19813db0b34d98518a0a055a0.png)

由[马库斯·斯皮斯克](https://unsplash.com/photos/AaEQmoufHLk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

JavaScript 引擎是单线程的，并利用事件循环。简单地说，这意味着您运行的任何语句都将在单个进程中一个接一个地执行。为了避免阻塞调用，JavaScript 采用了许多技术来避免计算时的等待。这些是**异步函数**。

你可以在这里阅读更多关于事件循环的内容，因为这个话题太深了，在这篇文章中无法涉及。

JavaScript 提供了三种处理异步代码的方法:回调，它允许您在异步方法完成运行后提供要调用的函数；承诺，它允许您将方法链接在一起；和 async/await 关键字，它们只是承诺之上的一些语法糖。

## 复试

处理异步的原始方法。回调允许你提供一个函数在异步代码完成后执行。在下面的例子中，`functionWithACallback`将一个函数作为参数，并在完成后调用该函数。

如果您需要将许多这样的调用链接在一起，这种来回传递函数的方法会变得非常混乱。回调需要沿着执行链向下传递，以便在最终流程结束时调用。

```
const functionWithACallback = (callback) => {
  //do some work then call the callback once done
  console.log('You called this function!');
  setTimeout(() => {
    callback('I am done');
  }, 1000)
};const myFunction = () => {
  // the function we want to call when the work is done
  const myCallback = console.log // this will call myCallback once it is finished 
  functionWithACallback(myCallback);
};myFunction();
//  You called this function
//  I am done
```

## 承诺

回调的一个主要问题是，当将许多函数调用链接在一起时，跟踪执行流程会变得越来越困难。承诺旨在通过允许您使用`.then()`语法将承诺链接在一起来解决这个问题。下面的例子与回调例子的工作方式相同，但是更容易理解:等到`getPromise()`完成，然后**调用包含`console.log()`的函数**

承诺的错误处理也不太复杂。promises 提供了一个`.catch()`包装器来帮助管理错误状态，而不是用错误对象调用回调。下面，如果上面的任何一个承诺出现错误，就会执行`catch`块。

```
const getPromise = () => Promise.resolve('My return value');const myFunction = () => {
  getPromise()
    .then(val => { 
      console.log(val); // prints 'My return value'
    }) // If there is an error in any of the above promises, catch
       // it here
    .catch(error => {   
      console.error(error.message);
    });
}
```

## 异步/等待

在 JavaScript 的最新版本中，添加了关键字`async`和`await`。这提供了一种更简洁的书写承诺的方法，并让用户对执行顺序有更多的控制。下面的例子在功能上与 promises 例子相同，但是使用了`async`和`await`关键字。

使用`try/catch`模块提供对`async`函数调用的错误处理。

```
const getPromise = () => Promise.resolve('My return value');// the function is marked with the async keyword
const myFunction = async () => {  
  // tell the interpreter we want to wait on the response
  try {
    const val = await getPromise(); // execute when the promise above is resolved
    console.log(val); // prints 'My return value'
  } catch (error) {
    console.error(error.message);
  }
}
```

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### 50 大 JavaScript 教程-免费学习 JavaScript。课程由开发人员提交并投票，从而实现…

gitconnected.com](https://gitconnected.com/learn/javascript)