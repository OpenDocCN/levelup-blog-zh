# JavaScript 面试问题:承诺

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-promises-400c51805cbe>

![](img/f282df36c6702e2ad7d966413780fe6a.png)

莎拉·塞万提斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

由于异步编程的普遍使用，承诺是 JavaScript 的重要部分。

在本文中，我们将研究一些关于 JavaScript 承诺的常见问题。

# 什么是承诺？

承诺是 JavaScript 中处理异步操作的一种方式。每个承诺代表一次行动。

它旨在以一种干净的方式解决组合多段异步代码的问题。

有了承诺，我们可以避免回调嵌套太深。

例如，不用编写如下深度嵌套的回调:

```
const fs = require('fs');fs.readFile('somefile.txt', function (e, data) {

  fs.readdir('directory', function (e, files) {

    fs.mkdir('directory', function (e) {

    })
  })
})
```

我们写道:

```
const fs = require('fs');const promiseReadFile = (fileName) => {
  return new Promise((resolve) => {
    fs.readFile('somefile.txt', function(e, data) {
      resolve(data);
    })
  })
}const promiseReadDir = (directory) => {
  return new Promise((resolve) => {
    fs.readdir(directory, function(e, data) {
      resolve(data);
    })
  })
}const promiseMakwDir = (directory) => {
  return new Promise((resolve) => {
    fs.mkdir(directory, function(e, data) {
      resolve(data);
    })
  })
}promiseReadFile('somefile.txt')
.then((data)=>{
  console.log(data);
  return promiseReadDir('directory')
})
.then((data)=>{
  console.log(data);
  return promiseMakwDir('directory')
})
.then((data)=>{
  console.log(data);  
})
```

我们将承诺串连起来，而不是嵌套起来，如下所示:

```
promiseReadFile('somefile.txt')
.then((data)=>{
  console.log(data);
  return promiseReadDir('directory')
})
.then((data)=>{
  console.log(data);
  return promiseMakwDir('directory')
})
.then((data)=>{
  console.log(data);  
})
```

这就是为什么我们要为长长的承诺链编写承诺，而不是深度嵌套的回调。读起来就容易多了。

承诺有三种状态。它们可能是待定的，这意味着结果还不知道，因为操作还没有开始。

可以实现，也就是说异步操作成功了，我们有结果了。

也可以拒绝，表示失败了，有失败的原因。

一个承诺可以被解决，这意味着它要么被实现，要么被拒绝。

同样，在上面的代码中，我们调用了`resolve`函数来返回承诺的值。为了获得承诺的解析值，我们从回调的参数中获得解析值。

`data`在每个回调中都有从它上面的承诺解析的数据。

为了在回调中运行下一个承诺，我们返回了下一个承诺。

为了拒绝一个承诺，我们在回调中调用`reject`函数，并将其传递给`Promise`构造函数。

例如，我们可以将上面的代码更改为:

```
const fs = require('fs');const promiseReadFile = (fileName) => {
  return new Promise((resolve, reject) => {
    fs.readFile('somefile.txt', function(e, data) {
      if (e){
        reject(e);
      }
      resolve(data);
    })
  })
}const promiseReadDir = (directory) => {
  return new Promise((resolve, reject) => {
    fs.readdir(directory, function(e, data) {
      if (e){
        reject(e);
      }
      resolve(data);
    })
  })
}const promiseMakwDir = (directory) => {
  return new Promise((resolve, reject) => {
    fs.mkdir(directory, function(e, data) {
      if (e){
        reject(e);
      }
      resolve(data);
    })
  })
}promiseReadFile('somefile.txt')
.then((data)=>{
  console.log(data);
  return promiseReadDir('directory')
})
.then((data)=>{
  console.log(data);
  return promiseMakwDir('directory')
})
.then((data)=>{
  console.log(data);  
})
.catch((err)=>{
  console.log(err);
})
```

一旦承诺被拒绝，随之而来的承诺就不会兑现。

我们可以得到错误的详细信息，并像上面那样在`catch`函数的回调中对它做些什么。

在上面的代码中，我们通过创建一个函数将每个异步代码转换成一个承诺，然后在函数中返回一个承诺，调用`resolve`来实现承诺，调用`reject`来拒绝带有错误值的承诺。

![](img/79177ee8187139a24f279f6739c8bee6.png)

照片由 [christian buehner](https://unsplash.com/@christianbuehner?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是异步/等待？

是一种以更短的方式编写承诺链的新方法。

例如，我们没有像上面那样用回调来调用`then`函数，而是将其重写为:

```
try {
    const readFileData = await promiseReadFile('somefile.txt');
    console.log(readFileData);
    const readDirData = await promiseReadDir('directory');
    console.log(readDirData);
    const makeFileData = await promiseMakwDir('directory');
    console.log(makeFileData);
    return makeFileData;
  }
  catch(err){
    console.log(err);
  }  
})();
```

这与以下内容相同:

```
promiseReadFile('somefile.txt')
.then((data)=>{
  console.log(data);
  return promiseReadDir('directory')
})
.then((data)=>{
  console.log(data);
  return promiseMakwDir('directory')
})
.then((data)=>{
  console.log(data);  
})
.catch((err)=>{
  console.log(err);
})
```

正如我们所见，`async/await`比用回调写`then`要短得多。

我们没有回调，也不需要在每次回调时都做出承诺。

`readFileData`、`readDirData`和`makeFileData`与每个`then`回调中的`data`参数相同。

`async`中的`try...catch`功能与`catch`中的调用和回调结束相同。`err`在两个例子中有相同的错误对象。

`async`函数只能返回承诺，所以`makeFileData`实际上是承诺的解析值，而不是实际值。

尽管它看起来像一个同步函数，但它的行为并不相同。

# 结论

承诺是一种干净地链接异步操作而无需深度嵌套回调的方式。

我们可以用`async/await`语法让它们更短更简洁。