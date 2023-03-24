# 如何在不添加任何第三方的情况下，在 Angular 中检测和处理离线模式

> 原文：<https://levelup.gitconnected.com/how-to-detect-and-handle-offline-mode-in-angular-without-using-any-library-36a108d015b9>

![](img/2eee9224144d0e58fac072ef58e3243b.png)

在本文中，我将带您了解如何检测离线模式，以及在 Angular 应用程序中处理离线模式的可能方法

# 如何检测离线模式？

为了检测浏览器的离线模式，我们可以使用 Javascript 窗口的在线和离线事件。

来自 [Mozilla MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Window):

> 当浏览器失去对网络的访问时，触发`[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)`界面的`**offline**`事件
> 
> 当浏览器访问网络时，触发`[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)`界面的`**online**`事件

# 如何在应用程序中处理脱机模式？

当失去与网络的连接时，我们可能想要做两种用例——在模型中进行更改或者对 UI 元素进行更改。

## 1.在连接模式更改后对模型进行更改

有时我们想改变组件，比如显示警告，更新逻辑等等..

为了实现这个功能，我们将使用`fromEvent`来监听 window 离线和在线事件

## 2.在连接模式更改后对用户界面进行更改

有时我们想在 UI 中做一些改变，比如在没有连接时禁用元素。为了实现这一功能，我们可以使用一个指令，通过将它添加到元素中，当没有连接时，它会将其禁用。

在本例中:

在选择器中，我们只选择按钮元素。

我们使用`HostListener`来监听窗口离线和在线事件，并更新`isOffline`变量。

我们使用`HostBinding`将按钮元素的禁用属性绑定到`isOffline`变量。

要使用它，我们只需将指令添加到`button`元素中

为了简单起见，我只使用该指令来更新按钮元素的 disabled 属性，但是它可以用于更新任何元素中的任何属性。

**Github 源代码链接**:[https://Github . com/saifabusaleh/angular-detect-connectivity-status](https://github.com/saifabusaleh/angular-detect-connectivity-status)

**其他有用资源**:

[主机绑定文档](https://angular.io/api/core/HostBinding)

[主机监听器文档](https://angular.io/api/core/HostListener)

希望这篇文章对你有帮助:)，有什么反馈请告诉我。

*跟我上* [*中*](https://medium.com/@saif.as) *求更多！*