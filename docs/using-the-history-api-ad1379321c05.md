# 使用浏览器历史 API

> 原文：<https://levelup.gitconnected.com/using-the-history-api-ad1379321c05>

![](img/7a36177299961f5b044963907ff5034a.png)

[汤彦麟](https://unsplash.com/@danieltong?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了在页面之间来回导航，我们可以使用大多数现代浏览器自带的历史 API。

在本文中，我们将研究如何使用它在相同来源的页面之间导航。

# 历史 API 中的方法

History API 有一些在页面之间来回移动的方法。

有一个返回上一页的`back`方法。

例如，我们可以如下使用它:

```
window.history.back()
```

这与单击浏览器上的后退按钮是一样的。

我们可以使用`forward`方法前进，如下所示:

```
window.history.forward()
```

# 移动到历史的特定点

我们可以使用`go`方法从会话历史中加载特定的页面，通过它在当前页面上的相对位置来识别。

当前页面是 0，所以负整数是前面的页面，正数是后面的页面。

例如:

```
window.history.go(-1)
```

和叫`back()`一样。

并且:

```
window.history.go(1)
```

跟打电话给`forward()`是一样的。

不带参数或 0 调用`go`和刷新页面是一样的。

![](img/3655e5286dede427c1c6eca0ac75c16b.png)

Giammarco Boscaro 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 推送状态

`pushState`方法让我们转到指定 URL 的页面。它需要三个参数。它们是:

*   `state` —这是一个 Javascript 对象，与`pushState()`创建的新历史条目相关联。状态对象可以是任何可以被净化的东西。它被保存到用户的磁盘上，以便当用户重新启动浏览器时可以恢复。如果状态对象有一个大于自身的序列化表示，那么这个方法将抛出一个异常。
*   `title` —大多数浏览器都会忽略这个字符串参数。
*   `url` —新历史条目的 URL。调用`pushState`后浏览器不会尝试加载 URL。但是，当我们重新启动浏览器时，它可能会加载。URL 可以是绝对的，也可以是相对的。该 URL 必须与当前 URL 的来源相同。否则，将引发异常。

如果我们不想改变网址，我们不必改变。不管它是什么，它必须和当前的 URL 在同一个原点。

例如，我们可以如下使用它:

```
window.onpopstate = function(event) {
  console.log(
    `location ${document.location} state ${JSON.stringify(event.state)}`
  );
};window.history.pushState(
  {
    foo: "bar"
  },
  "",
  "/foo"
);window.history.pushState(
  {
    bar: "baz"
  },
  "",
  "/bar"
);window.history.back();
window.history.back();
window.history.go(2);
window.history.go(-2);
```

然后我们得到:

```
location [https://ib3i4.csb.app/foo](https://ib3i4.csb.app/foo) state {"foo":"bar"}
location [https://ib3i4.csb.app/bar](https://ib3i4.csb.app/bar) state null
location [https://ib3i4.csb.app/bar](https://ib3i4.csb.app/bar) state {"bar":"baz"}
location [https://ib3i4.csb.app/bar](https://ib3i4.csb.app/bar) state null
```

从`console.log`开始。

每当我们在页面间导航时，就会调用`onpopstate`。

当我们回去的时候，我们只在第一时间记录了`state`。

同样，当我们前进时，我们只记录了一次`state`。

# replaceState

`replaceState`方法类似于`pushState`，但是它修改当前的历史条目，而不是添加一个新的。

它采用与`pushState`相同的参数。

例如，我们可以如下使用它:

```
window.onpopstate = function(event) {
  console.log(
    `location ${document.location} state ${JSON.stringify(event.state)}`
  );
};window.history.pushState(
  {
    bar: "bar"
  },
  "",
  "/foo"
);window.history.go(0);window.history.replaceState(
  {
    bar: "baz"
  },
  "",
  "/bar"
);window.history.go(0);
```

然后我们得到:

```
location [https://ib3i4.csb.app/foo](https://ib3i4.csb.app/foo) state {"bar":"bar"}
location [https://ib3i4.csb.app/bar](https://ib3i4.csb.app/bar) state {"bar":"baz"}
```

正如我们所见，`/bar`的状态条目在我们第二次刷新时从`{“bar”:”bar”}`变成了`{“bar”:”baz”}`，因此状态被替换。

# 结论

我们可以通过调用`window.history`对象中的方法来使用历史 API。

我们可以用`forward`在历史上前进，用`back`在历史上倒退。要进入历史中的任何一页，我们可以用一个数字调用`go`。

此外，我们可以调用`pushState`将一个新条目推送到历史记录中，而不必立即转到它。它可以在重启时或者当我们调用上面的方法时加载页面。

我们可以使用`replaceState`来替换当前的历史条目。它采用与`pushState`相同的参数。

当使用这个 API 时，每个页面必须是相同的来源。