# JavaScript 对初学者的承诺是什么

> 原文：<https://levelup.gitconnected.com/what-is-a-promise-in-javascript-for-beginners-5dde3fbf31c2>

![A](img/e335c4dacc10fa5bf9454d1eacca89b2.png)  A   re 用 JavaScript 给你难以把握的承诺？说实话，一开始我也在承诺中挣扎，因为它们太令人困惑了。难怪，它们让异步代码看起来是同步的。如果你和我当时的情况一样，不要害怕。事实证明它们并没有那么难。

如果你对更多关于 JavaScript 或软件开发的有趣文章感兴趣，别忘了订阅并关注 pandaquests。

![](img/d7ba0aefed4ea70b6747eef96fdc6805.png)

照片由[好色玩具](https://unsplash.com/@womanizer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，promise 是一个表示异步操作结果的对象。它允许您指定当异步操作完成或失败时应该发生什么。

承诺用于处理 JavaScript 中的异步操作，比如发出网络请求或读取文件。它们提供了一种在单一位置处理操作结果的方法，无论操作是成功还是失败，而不必为成功和失败的情况编写单独的回调函数。

下面是一个使用承诺包装异步操作的简单示例:

```
function getDataFromServer(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = function() {
      if (xhr.status === 200) {
        resolve(xhr.response);
      } else {
        reject(new Error(xhr.statusText));
      }
    };
    xhr.onerror = function() {
      reject(new Error("Network error"));
    };
    xhr.send();
  });
}
getDataFromServer("/data.json")
  .then((response) => {
    // do something with the response
  })
  .catch((error) => {
    // handle the error
  });
```

在这个例子中，getDataFromServer 是一个向服务器发出 HTTP 请求并返回承诺的函数。如果请求成功，则通过服务器的响应解析承诺，如果请求失败，则通过错误拒绝承诺。then 方法用于指定解析承诺时应该发生什么，catch 方法用于指定拒绝承诺时应该发生什么。

在 JavaScript 中使用 promises 的一些优点包括:

*   提高可读性:承诺允许您以更线性的风格编写异步代码，这可以使它更容易理解和调试。
*   更好的错误处理:承诺提供了一种集中的方法来处理错误，这使得编写健壮的代码更加容易。
*   更好地管理异步代码:Promises 允许您将异步操作链接在一起，这使得管理复杂的异步代码流变得更加容易。
*   取消操作的能力:可以取消承诺，这在某些不再需要异步操作的情况下很有用。

在 JavaScript 中使用异步操作时，承诺可以帮助您编写更简洁、更易维护的代码。虽然 promises 是 JavaScript 中处理异步代码的有用工具，但是使用它们也有一些潜在的缺点:

*   复杂性:承诺会给你的代码增加额外的复杂性，尤其是当你不熟悉它们的时候。这使得理解和调试你的代码变得更加困难，特别是对于那些不熟悉承诺的开发人员。
*   旧浏览器中缺乏支持:并非所有浏览器都支持承诺，这意味着您可能需要使用聚合填充或其他解决方法来确保您的代码在旧浏览器中工作。
*   性能问题:在某些情况下，与其他异步模式(如回调或异步/等待)相比，使用承诺会导致性能下降。这是因为承诺涉及创建额外的对象和函数调用，这会增加代码的开销。
*   在某些情况下缺乏取消支持:虽然承诺在某些情况下可以取消，但这并不总是可能的。例如，如果某个承诺已经结算(即已解决或已拒绝)，则不能取消。

尽管 promises 是 JavaScript 中处理异步代码的有用工具，但考虑潜在的缺点并选择正确的异步模式也很重要。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

如你所见，这并不难。如果你还有问题，我很乐意收到你的来信。写下评论，我会尽快回复你。没有问题吗？关注或订阅我们，获取更多关于 JavaScript 或软件开发的精彩内容。我们每周都在写作。期待收到你的来信。