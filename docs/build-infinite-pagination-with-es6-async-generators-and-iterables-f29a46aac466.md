# 使用 ES6 异步生成器和 iterables 构建无限分页

> 原文：<https://levelup.gitconnected.com/build-infinite-pagination-with-es6-async-generators-and-iterables-f29a46aac466>

![](img/a5110b7b7e10f27d114b0a37216e4be8.png)

# 搞什么🐠是一台发电机。

嗯，根据定义:

> *发电机是可以退出并在以后重新进入的功能。它们的上下文(变量绑定)将在重入时被保存。*

# 真想不到，这会带来什么？💭

啊，再次定义..💤

> *JavaScript 中的生成器——特别是当与承诺结合使用时——是异步编程的一个非常强大的工具*

# 真实世界的场景？

在阅读了所有的定义之后，让我们进入它的核心。:octocat:

我们有一个有趣的问题要解决。这是为了在向右滑动时为我们的一个移动应用程序分页。

# 所以我们用发电机来发电？

还有其他可能的解决方案，发电机只是一个更清洁的解决方案。

# 怎么做？

创建发生器

```
const asyncGetContent = async function* (){
  const limit = 10; /* content per page */
  let offset = 0; /* index of item to start from */
  let totalCount = -1; /* -1 signifies failure */
  while (offset === 0 || offset < totalCount) {
    try {
      const response = await (await fetch(<<url>>)).json();
      offset = offset + limit;
      totalCount = response["total-count"];
      console.log(`offset + totalCount`, offset, totalCount);
      yield response;
    } catch (e) {
      console.warn(`exception during fetch`, e);
      yield {
        done: true,
        value: "error"
      };
    }
  }

}
```

要理解的内容太多了，让我们仔细阅读每一行。

⛄:我们已经定义了一个变量来限制你选择的结果(不一定是常量)

⛄准备一个 fetch/ axios/ some API 调用，用一个`await`来处理它，这样我们就可以解析结果承诺。

⛄ `yield`回应。返回将是一个承诺，由异步调用者使用`.next()`(我们将在下一节中讨论)

⛄，我们来处理捡球的事吧

> 我如何使用这个？

```
const contentGen = await asyncGetContent(); /* initate the generator */
```

剩下要做的就是在异步函数中初始化和调用它，如下所示:

启动后，我们可以在任何需要的地方调用(例如:右击/点击按钮)来产生结果

```
const content = await contentGen.next();
```

必要时使用`content`【在这种情况下来自 api 的响应】

如果您有任何问题，请在评论中告诉我们，我们期待您的反馈🍻

*原载于 2020 年 5 月 7 日*[*https://dev . to*](https://dev.to/jeevankishore/build-pagination-with-es6-async-generators-and-iterables-4l4p)*。*