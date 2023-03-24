# 承诺的常见陷阱

> 原文：<https://levelup.gitconnected.com/common-gotcha-with-promises-693a993568c2>

理解使用嵌套异步代码链接在一起的多个`.then`调用的问题。

![](img/cfab3b0f7cd810c4e35ad288fcf78b2d.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你能预测下面代码的输出吗？

输出如下

```
first
third
second
```

你可能已经预料到了

```
first
second
third
```

需要理解的关键点是:

> 当您附加多个`.then`处理程序时，如果处理程序中有额外的异步代码，并不能保证每个`.then`处理程序都会在前一个`.then`处理程序完成后被调用。

在上面的代码中，第二个`.then`处理程序有一个`setTimeout`函数调用，超时时间为 2000 毫秒，所以不会立即执行，但是第三个`.then` 会先执行，因为它超时时间为 1000 毫秒。

如果任何一个`.then`处理程序正在进行 API 调用或执行一些长时间运行的过程，那么如果承诺没有被正确处理，下一个`.then`处理程序可能会被执行。

***有一种方法可以修复这种默认行为。***

> 我们可以从第二个`.then`处理程序返回一个新的承诺，并且一旦我们完成了 API 调用或任何其他长时间运行的过程，就只调用`resolve`或`reject`。

这将保证下一个`.then`总是在前一个完成后执行。

下面是维护订单顺序的代码:

因此，如果您的下一个`.then`调用依赖于前一个`.then`调用结果，理解这一点将有助于您避免代码中的异常行为。

今天就到这里。希望你今天学到了新东西。

不要忘记订阅我的每周简讯，里面有惊人的技巧、窍门和文章，直接在这里的收件箱里。