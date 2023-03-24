# 使用权限 API 获取浏览器用户权限

> 原文：<https://levelup.gitconnected.com/getting-browser-user-permission-with-the-permissions-api-eafbc9c7f4d7>

![](img/e558a7953ab912474856fe69c6ab09d6.png)

本·怀特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当我们想做任何改变用户体验的事情时，我们必须得到用户的同意。例如，我们需要获得用户许可来显示通知或访问他们的硬件。

现代浏览器在如何获得权限方面是标准化的。这些都在权限 API 中。

依赖权限 API 获取权限的 API 包括剪贴板 API、通知 API、推送 API 和 Web MIDI API。

在标准浏览器和工作环境中，`navigator`对象都有`permissions`属性。这给了我们一个获得许可的方法。然而，对于每个 API，我们请求权限的方式是不同的。

这个 API 是实验性的，所以检查你的目标浏览器是否受支持。

# 查询权限

`navigation.permissions`对象让我们查询用`query`方法设置的权限。

它返回一个承诺，让我们得到许可的状态。例如，我们可以编写以下代码来检查地理定位 API 的权限:

```
(async () => {
  const result = await navigator.permissions.query({
    name: 'geolocation'
  });
  console.log(result.state);
})();
```

`result.state`的可能值是`'granted'`、`'denied'`或`'prompt'`。

也有一个为网络工作者做同样的事情。

我们传递给`query`方法的对象是权限描述符对象，它具有以下属性:

*   `name` —我们想要获得许可的 API 的名称。火狐只支持`geolocation`、`notifications`、`push`和`persistent-storage`
*   `userVisibleOnly` —表示我们是否希望显示每条消息的通知，或者能够发送静默推送通知。默认值为`false`。
*   `sysex` —仅适用于 MIDI。指示我们是否需要接收系统独占消息。默认值为`false`。

# 请求许可

请求权限在 API 之间是不同的。例如，当我们调用`getCurrentPosition()`时，地理定位 API 将显示一个权限提示，如下所示:

```
navigator.geolocation.getCurrentPosition((position) => {
  console.log('Geolocation permissions granted');
  console.log(`Latitude: ${position.coords.latitude}`);  
  console.log(`Longitude: ${position.coords.longitude}`);
});
```

通知 API 有一个`requestPermission`方法来请求权限:

```
Notification.requestPermission((result) => {
  console.log(result);
});
```

# 监视权限更改

这个`navigator.permissions.query`承诺解析为一个`PermissionStatus`对象。我们可以用它来设置一个`onchange`事件处理函数来监视权限的变化。

例如，我们可以对此示例进行扩展，添加一个事件处理函数来监视地理位置 API 权限的变化:

```
(async () => {
  const result = await navigator.permissions.query({
    name: 'geolocation'
  });
  console.log(result.state);
})();
```

我们可以写:

```
(async () => {
  const permissionStatus = await navigator.permissions.query({
    name: 'geolocation'
  });
  permissionStatus.onchange = () => {
    console.log(permissionStatus.state);
  }
})();
```

`permissionStatus.state`将拥有地理定位 API 的权限。

每当权限改变时，例如当我们在浏览器中改变权限设置时，它就会记录下来。

![](img/9ff4c928c7ff7ff776934f1ba0d32d07.png)

照片由 [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 撤销权限

我们可以通过使用`revoke`方法来撤销许可。

要调用它，我们可以从`navigator.permissions`对象访问它，如下所示:

```
navigator.permissions.revoke(descriptor);
```

其中`descriptor`是我们上面描述的许可描述符。

它返回一个承诺，用指示请求结果的权限状态对象进行解析。

如果获取权限描述符失败，或者权限不存在或不受浏览器支持，此方法将引发 TypeError。

权限描述符的`name`属性可以接受以下值之一`'geolocation'`、`'midi'`、`'notifications'`和`'push'`。

其他属性保持与`query`方法相同。

例如，我们可以这样使用它:

```
(async () => {
  const permissionStatus = await navigator.permissions.revoke({
    name: 'geolocation'
  });
  console.log(permissionStatus);
})();
```

注意，我们必须在 Firefox 51 或更高版本中将`dom.permissions.revoke.enable`设置为`true`才能使用这个方法。Chrome 中没有启用。

权限 API 让我们以统一的方式检查和撤销浏览器权限。

对于每个 API，请求权限是不同的。这种情况短期内不会改变。

这是一个实验性的 API，所以在生产中使用它可能还为时过早。