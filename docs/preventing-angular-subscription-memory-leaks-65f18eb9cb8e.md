# 防止角度订阅内存泄漏

> 原文：<https://levelup.gitconnected.com/preventing-angular-subscription-memory-leaks-65f18eb9cb8e>

不要让你的应用程序变慢，处理好你的订阅

![](img/8ce495a5f59c4076c528d9652ea56ede.png)

凯文·Ku 在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 处理订阅的方式不正确

在我早期的 Angular days，我会在应用程序被破坏之前订阅 observables 而不退订，这可能会导致内存泄漏。例如:

# 更好的方法

避免这种情况的一个简单方法是在`ngOnDestroy`生命周期方法中保存对订阅和取消订阅的引用。例如:

这更好，但是如果您的组件中有一堆订阅呢？你可以使用一个漂亮的`rxjs`操作符。

您可以创建一个主题`ngDestroy$`，并用`takeUntil`操作符来管理每个订阅。现在，当组件被销毁时，您可以在想要取消的所有订阅前添加`.pipe(takeUntil(this.ngDestroy$))`。

**警告:**如果您有一个为每个输入更改创建订阅的方法，例如下面的组件:

您可能会重叠请求，先前的请求在后续请求之后完成，可能会覆盖您的新数据。在这种情况下，您希望确保如果您第二次调用`loadData`方法，您首先取消任何先前的请求。

要解决这个问题，您可以在`loadData`方法中使用一个变量来存储请求的订阅引用(解决方案 1 ),或者您可以在每次调用再次发出 HTTP 请求之前重置`ngDestroy$`订阅:

如果您也将变量`ngDestroy$`用于其他请求，那么上述方法在某种程度上限制了它的用处，因为如果输入值改变，这些请求将被重置。

请随意尝试不同的方法，看看哪些方法适合你的特殊情况！

如果你喜欢这篇文章，请查看我在 Angular 上的其他文章，并随时关注我，以免错过新文章。

我在 YouTube[上的角度教程](https://www.youtube.com/channel/UCTSZi5p3dzPfDYArmxDUJZA)