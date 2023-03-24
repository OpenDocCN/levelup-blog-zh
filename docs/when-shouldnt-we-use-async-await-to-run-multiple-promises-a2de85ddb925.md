# 什么时候不应该使用 Async/Await 运行多个承诺？

> 原文：<https://levelup.gitconnected.com/when-shouldnt-we-use-async-await-to-run-multiple-promises-a2de85ddb925>

![](img/ed550edf5006b411e5bc08ece7bcf8e8.png)

照片由[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，`async`和`await`语法非常适合运行多个承诺。

在这篇文章中，我们看看什么时候我们不应该使用它们，我们应该用什么来代替。

# 什么时候应该使用异步和等待？

`async`和`await`应该只在我们需要运行连续运行的承诺时使用。

否则，我们不应该使用它们。例如，下面是第二个承诺依赖于第一个承诺的结果的情况，因此它们应该按顺序运行:

```
(async () => {
  const breedsRes = await fetch('https://dog.ceo/api/breeds/list/all');
  const {
    message
  } = await breedsRes.json();
  const breed = Object.keys(message)[0]; const imgRes = await fetch(`https://dog.ceo/api/breed/${breed}/images/random`)
  const img = await imgRes.json();
  console.log(img.message);
})();
```

在上面的代码中，第一个请求从 Dog API 返回品种，然后将结果用于对 Dog API 的第二个承诺以获取图像。

在这种情况下，使用`async`和`await`是有意义的，因为我们确实需要等待第一个承诺完成后再继续第二个承诺。

`for...await...of`循环只是循环形式的`async`和`await`。因此，它也在 iterable 对象中顺序运行承诺。

它们做同样的事情，但是`for..await...of`循环允许我们在循环中连续运行承诺，这在没有外部库的情况下是无法实现的。

因为我们是按顺序运行它们的，所以我们必须等待第一个任务完成，然后第二个任务才开始。因此，等待他们两个完成需要更长的时间。

# 不相关的承诺不应该使用异步和顺序等待

另一方面，如果我们正在运行不相关的承诺，我们不应该使用`async`和`await`，因为我们没有必要在运行下一个之前等待上一个承诺完成。

例如，以下代码在一个`async`函数中运行两个不相关的承诺:

```
(async () => {
  const res = await fetch('https://dog.ceo/api/breeds/image/random')
  const {
    message
  } = await res.json()
  console.log(message);const useRes = await fetch('https://randomuser.me/api/')
  const {
    results
  } = await useRes.json()
  console.log(results);
})();
```

第一个从 Dog API 获取一些数据，第二个获取一些随机生成的用户数据。

它们互不依赖，因此它们不必依次使用`async`和`await`。

这两个请求在普通宽带连接上总共需要 510 毫秒，在慢速 3G 连接上需要 4 秒钟才能完成。

这肯定比应该的要慢，因为我们可以通过并行运行它们来优化，因为它们是不相关的。

我们可以用`Promise.all`来做，如下所示:

```
(async () => {
  const [res, userRes] = await Promise.all([
    fetch('https://dog.ceo/api/breeds/image/random'),
    fetch('https://randomuser.me/api/')
  ])
  const [{
    message
  }, results] = await Promise.all([res.json(), userRes.json()]);
  console.log(message, results);
})();
```

上面的代码在正常的宽带连接上运行需要 360 毫秒，而在使用慢速 3G 连接时，运行需要 3 秒钟。

正如我们所看到的，差异是显著的，并且我们得到了与上一个例子相同的数据，因为这两个承诺不相关。

因此，我们决不能把`async`和`await`与不相关的承诺连用。

相反，我们应该将`async`和`await`与`Promise.all`一起使用，然后将我们的承诺放入传入的数组中。

我们将`fetch`承诺分组到一个数组中，并将其传递到`Promise.all`，然后在`fetch`承诺完成后，我们对`json`调用做同样的事情。

这是因为`json`承诺依赖于`fetch`承诺，而`fetch`承诺并不相互依赖，`json`承诺也是独立的。

现在我们使用了`Promise.all`，我们的代码运行得更快了，同时从使用`async`和`await`中获得了同样的好处。

![](img/dcc820e7291e8854a6f879e93aa5e1b9.png)

照片由 [christian buehner](https://unsplash.com/@christianbuehner?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以使用`async`和`await`来链接依赖于另一个的承诺。

同样，`for...await...of`循环也在一个 iterable 对象中顺序运行承诺。因此，它的行为与`async`和`await`相同。

如果我们在履行不相关的承诺，我们应该同时履行

为此，我们应该使用`Promise.all`与`async`和`await`并行运行不相关的承诺。

这样，我们就不必等待一个又一个不相关的承诺，造成不必要的等待。