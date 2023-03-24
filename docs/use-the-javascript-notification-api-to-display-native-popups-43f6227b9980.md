# 使用 JavaScript 通知 API 显示本地弹出窗口

> 原文：<https://levelup.gitconnected.com/use-the-javascript-notification-api-to-display-native-popups-43f6227b9980>

![](img/7e391b86ac93c58234b405738667767f.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

通知 API 允许我们显示弹出窗口，这些窗口显示为本机桌面或移动通知。功能因平台而异，但它们通常提供了一种向用户异步提供信息的方式。

# 创建新通知

我们可以用`Notification`构造函数创建一个新的通知。它需要两个参数。第一个是标题，第二个是具有各种属性的对象，是可选的:

*   `dir`:显示通知的方向。默认值为`auto`，但也可以是从右到左的`rtl`或从左到右的`ltr`。
*   `lang`:语言的字符串值。可能的值是 BCP 47 语言标签。
*   `badge`:包含图像 URL 的字符串，当没有足够的空间显示它时，该图像用于表示通知。
*   `body`:通知文本的字符串。
*   `tag`:带有通知标识标签的字符串
*   `icon`:图标 URL 的 URL 字符串
*   `image`:要显示的图像的 URL 字符串。
*   `data`:我们希望与通知关联的数据。
*   `vibrate`:振动设备的振动模式。
*   `renotify`:布尔值，指定在新通知替换旧通知后是否应通知用户。默认值为`false`。
*   `requireInteraction`:指示通知是否应该保持活动状态，直到用户点击或取消它。默认值为`false`。
*   `actions`:一个`NotificationAction`数组，当显示通知时，用户可以使用这些动作。它是一个具有`name`、`title`和`icon`属性的对象。

我们可以定义一个简单的通知，如下所示:

```
const options = {
  body: "body",
  icon:
    "[https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png](https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png)"
};const n = new Notification("title", options);
```

要查看通知，我们必须将`Notification`设置为总是显示在浏览器中。

我们应该会看到我们在`icon`属性中设置的文本和指定的图标。

# 通知对象的方法

## 请求许可

我们可以用`requestPermission`静态方法请求许可。它返回一个承诺，该承诺在允许或拒绝显示通知的许可时解决。

它使用具有权限数据的对象进行解析。

当我们运行这个方法时，浏览器将请求允许显示域的通知。

例如，我们可以如下使用它:

```
(async () => {
  try {
    const permission = await Notification.requestPermission();
    console.log(permission);
    const options = {
      body: "body",
      icon:
        "[https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png](https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png)"
    };
    const n = new Notification("title", options);
  } catch (error) {
    console.log(error);
  }
})();
```

如果许可被授予，在`try`块中的`console.log`将记录`granted`。否则，它将从`catch`块中的`console.log`开始记录`denied`。

## 以编程方式关闭通知

我们可以用`close`方法以编程方式关闭通知，这是一个`Notification`对象的实例方法。

例如，我们可以如下使用它:

```
(async () => {
  try {
    const permission = await Notification.requestPermission();
    console.log(permission);
    const options = {
      body: "body",
      icon:
        "[https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png](https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png)"
    };
    const n = new Notification("title", options);
    await new Promise(resolve => {
      setTimeout(() => {
        n.close();
        resolve();
      }, 5000);
    });
  } catch (error) {
    console.log(error);
  }
})();
```

在上面的例子中，我们在`setTimeout`方法的回调中调用了`close`。这使得它在 5 秒钟后自动关闭。

![](img/febd719ad8a3c047f13c528d67df640a.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 事件处理程序

`Notification`对象也有自己的事件处理程序。这些事件是`onclick`、`onclose`、`onerror`和`onshow`。我们可以给它们分配我们自己的事件处理函数。

## onclick

当我们想要在通知被点击时做一些事情时，我们可以给`onclick`属性分配一个事件处理程序。例如，我们可以写:

```
(async () => {
  try {
    const permission = await Notification.requestPermission();
    console.log(permission);
    const options = {
      body: "body",
      icon:
        "[https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png](https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png)"
    };
    const n = new Notification("title", options);
    n.onclick = () => {
      alert("Notification clicked");
    };
  } catch (error) {
    console.log(error);
  }
})();
```

当我们的通知被点击时，在浏览器标签中显示一个警告。事件处理函数可以接受一个参数，即事件对象。

默认行为是将焦点移动到通知的相关浏览上下文的视口。我们可以在传入的`event`参数上调用`preventDefault()`来防止这种情况，如下所示:

```
(async () => {
  try {
    const permission = await Notification.requestPermission();
    console.log(permission);
    const options = {
      body: "body",
      icon:
        "[https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png](https://www.iconninja.com/files/926/373/306/link-chain-url-web-permalink-web-address-icon.png)"
    };
    const n = new Notification("title", options);
    n.onclick = event => {
      event.preventDefault();
      alert("Notification clicked");
    };
  } catch (error) {
    console.log(error);
  }
})();
```

我们可以通过给`onclose`属性分配一个事件处理函数，让通知在关闭时做一些事情。

同样，我们可以对处理错误的`onerror`属性和处理显示通知时触发的`show`事件的`onshow`属性做同样的事情。

# 结论

正如我们所看到的，通知 API 是一种非常简单的方式来显示我们编写的 web 应用程序的本地通知。我们请求允许用静态的`Notification.requestPermission`方法显示通知。

当用户允许显示通知时，一旦承诺得到解决，那么我们只需用我们想要的选项创建一个`Notification`对象。然后将显示通知。