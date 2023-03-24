# LocalStorage 和 SessionStorage 的区别——如何使用它们？

> 原文：<https://levelup.gitconnected.com/difference-between-localstorage-and-sessionstorage-how-to-use-them-5b4cf7f7ce69>

了解如何使用 JavaScript 在 localStorage 和 sessionStorage 中存储数据

`localStorage`和`sessionStorage`都是浏览器中的对象。您可以使用这两个对象来存储数据。

![](img/2a1ef8d4a2bf901eeadc408a73244311.png)

照片由[斯蒂夫·约翰森](https://unsplash.com/es/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# TL；博士:

*   `localStorage`数据没有到期时间，除非用户在匿名模式下浏览。在这种情况下，当页面关闭时，数据会被删除。用户也可以通过清除浏览器缓存来删除数据。
*   `sessionStorage`当页面关闭时，数据被删除。

# 如何使用 LocalStorage？

下面是在`localStorage`中保存数据的通用语法。

```
localStorage.setItem(variableKey, variableValue);
```

## 存储数据

假设你有一个叫做`dataValue`的字符串变量。

您可以使用以下语法将`dataValue`保存在`localStorage`中。

```
localStorage.setItem("dataKey", "dataValue");
```

在本例中，`dataKey`表示将与值`dataValue`相关联的键。你可以随意命名`dataKey`。

如果`dataValue`不是一个字符串，你可以使用`JSON.stringify()`来转换它。

## 读出数据

然后，您可以使用从存储中读取数据

```
const data = localStorage.getItem("dataKey");
console.log("Data in storage: ", data);
```

如果您之前使用了`JSON.stringify()`，那么现在您需要对数据使用`JSON.parse()`方法。然而，这并不是绝对必要的，这取决于您想要对该数据做什么。

可以在 React 和 Angular 中使用`localStorage`。但是，您应该注意前者的组件生命周期。

## 删除数据

最后，您可能想要删除`*localStorage*`和*中的数据。*

有两种方式:`removeItem()`和`clear()`。

通过使用`removeItem()`,您只删除与您传入的键相关的值，例如下面例子中的`'dataKey'`。

```
localStorage.removeItem('dataKey');
```

通过使用`clear()`，您将删除`localStorage`区域中与您正在使用的应用程序相关的所有内容。

```
localStorage.clear();
```

# 如何使用 SessionStorage？

关于`sessionStorage`的几句话。根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) 的报告:

*   **一个选项卡=一个会话存储***——每当在浏览器的特定选项卡中加载文档时，都会创建一个唯一的页面会话并分配给该特定选项卡。该页面会话仅对该特定选项卡有效。*
*   **Tab open = session storage alive***—只要选项卡或浏览器打开，页面会话就会持续，并在页面重新加载和恢复后继续存在。*
*   **标签关闭=清除会话存储—** *关闭标签/窗口结束会话并清除* `*sessionStorage*` *中的对象。*

说到这里，用`sessionStorage`和`localStorage`很像。

## 存储数据

假设你有一个叫做`dataValue2`的字符串变量。

您可以使用以下语法将`dataValue2`保存在`sessionStorage`中。

```
sessionStorage.setItem("dataKey", "dataValue2");
```

与前面的例子一样，`dataKey`表示将与值`dataValue2`相关联的键。你可以随意命名`dataKey`。

如果`dataValue2`不是一个字符串，你可以使用`JSON.stringify()`来转换它。

## 读出数据

然后，您可以使用从存储中读取数据

```
const data = sessionStorage.getItem("dataKey");
console.log("Data in storage: ", data);
```

如果您之前使用了`JSON.stringify()`，那么现在您需要对数据使用`JSON.parse()`方法。然而，这并不是绝对必要的，这取决于您想要对该数据做什么。

## 删除数据

最后，您可能想要删除数据会话存储*。*

有两种方式:`removeItem()`和`clear()`。

通过使用`removeItem()`，您只删除与您传入的键相关的值，例如下面例子中的`'dataKey'`。

```
sessionStorage.removeItem('dataKey');
```

通过使用`clear()`，您将删除`sessionStorage`区域中与您正在使用的应用程序相关的所有内容。

```
sessionStorage.clear();
```

虽然这里你只能看到一个使用 React 和 Angular 的`localStorage`的例子，但是使用`sessionStorage`也遵循类似的模式。