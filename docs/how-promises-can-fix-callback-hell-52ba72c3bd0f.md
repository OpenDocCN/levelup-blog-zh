# 承诺如何修复回调-地狱

> 原文：<https://levelup.gitconnected.com/how-promises-can-fix-callback-hell-52ba72c3bd0f>

![](img/c804cd18795af78658d7bec756e548dc.png)

有过无休止的回调链吗？是的，我也是。我们称之为“回调-地狱”，它被称为“地狱”是有原因的。

很多年来，我忽略了承诺，主要是因为我在几年内都在使用不容易实现的技术。然而，当添加这种支持时，我仍然避开它们，因为我认为创建承诺然后到处使用`.then()`没有任何好处。感觉编写几个函数并使用回调也一样容易。

当然，我避开了无休止的嵌套，并且我经常以这样的代码结束:

```
function wrappedLogic() {
    function a() {
        asyncSomething(b);
    }
    function b(e) {
        // do something with e here
        asyncSomething(c);
    }
    function c(e) {
        // do something with e here
    }
}
```

这只是 3 个不同的函数执行 2 个不同的异步动作，无论是图像操作、API 调用还是线程化的东西。

在我看来，使用承诺，我必须在`asyncSomething`函数中创建这个承诺，然后我将做`asyncSomething().then(b)`而不是`asyncSomething(b)`。虽然这可能读起来更干净(因为这个原因)，我从来没有真正看到它的好处。

当然，我在这里跳过了很多好处，比如[承诺链、](https://javascript.info/promise-chaining)但是如果你的思想没有看到任何好处，那就需要更多一点的东西来最终让事情“点击”。

但最终让我看到光明的是`async/await`。现在我开始自责，我一直以来是多么的愚蠢，我本可以把承诺用在像连锁这样的事情上，但尤其是用在`await`上。

以上面的 3 函数示例代码为例，如果上面的`asyncSomething`函数返回一个承诺，那么它应该是这样编写的。

```
async function wrappedLogic() {
    const result = await asyncSomething();
    // do something with result
    const secondResult = await asyncSomething();
    // do something with secondResult
}
```

那么你脑子里有没有什么东西还想把它写成我之前写的 3 个函数那样？这看起来是不是好多了？

你能想象编写需要 10 个函数的代码逻辑吗？还是只有 10 个在等着？那样看起来会更好吗？

我是这样认为的，这最终使我更深入地研究了承诺，现在我在任何地方都使用它们，并且在过去的几年中，我一直试图在所有代码中实现它们。

## 包装遗留软件

所以现在我们到了让我犹豫了一段时间的点，遗留软件不支持承诺。这并不是说你不能使用承诺。更多的问题是 SDK 没有这些功能，在相当多的方面仍然没有。您可以用 promise 声明包装任何异步功能，并使用该 promise 返回到您想要的任何`async/await`功能。我用自己的应用程序做这个已经有一段时间了，但是最近我也调整了我的一个[开源项目](https://github.com/Topener/XHR)，这样其他人也可以使用它。当然，我几年前就该这么做了，但一直没时间去做。但是根据那些仅仅因为它有前景就想使用它的人的反应，我意识到我应该写一篇关于这个学习的博客。

因为有一件事对我来说是显而易见的，仅仅因为一种语言支持某种使生活变得更容易的东西，并不意味着使用这种语言的每个人实际上都在使用它。我花了很长时间才理解它，而且有很多人在使用 JavaScript，他们甚至从来没有使用过承诺。

那么如何包装遗留软件呢？嗯…基本上是承诺 101。不是在函数中不返回任何内容，而是接受回调作为参数，而是将整个内容包装在 promise 声明中，然后进行 resolve/reject。

拿这个例子来说:

```
function somethingAsync(callback){
    doApiCall(result => {
        callback(JSON.parse(result.response));
    });
}
```

让我们假设`doApiCall`是您使用的 SDK 提供的一个方法，它不容易调整(在我使用[跨平台应用程序开发 SDK](https://titaniumsdk.com/api/titanium/network/httpclient.html) 的情况下就是这样)，那么您可以这样包装它:

```
function somethingAsync(){
    return new Promise((resolve, reject) => {
        doApiCall(result => {
            resolve(JSON.parse(result.response));
        });
    });
}
```

这样，你突然用你的`somethingAsync`函数支持一个承诺，并且你不必调整 SDK 来实现它。这在很多理想世界不存在的情况下非常重要。现在，您可以用`await`调用函数，并且在代码中的任何其他地方都不必担心回调。

## 进一步阅读

所以现在你知道了使用承诺的(众多)理由之一，希望你至少理解了其中一部分的重要性。希望你能看到，就像我几年前看到的一样，如果你还没有在读这篇文章之前看到的话。

现在你可能想开始检查 mdn 上[承诺的文档和 mdn](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 上[等待的文档。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)

**反馈**

我错过了什么明显的东西吗？你最近学到的东西？请回复我或者给我发一条[推文](https://twitter.com/wraldpyk)！