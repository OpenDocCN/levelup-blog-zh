# JavaScript 提示—添加秒数、格式化日期、遍历对象等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-add-seconds-format-dates-traverse-objects-and-more-17d8a673881f>

![](img/f87659d84f61b26f15cd895a12d44f7a.png)

照片由[奥兹古尔·卡拉](https://unsplash.com/@karaozgur?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 给日期加上 x 秒

我们可以通过使用`getSeconds`方法来获取`date`对象中的秒数，从而将`x`的秒数添加到`Date`实例中。

例如，我们可以写:

```
const date = new Date('2020-01-01 10:11:55');
date.setSeconds(date.getSeconds() + 10);
```

我们创建一个`Date`实例。

然后我们调用`setSeconds`来设置秒。

我们把从`getSeconds`开始的秒数加上我们想要增加的秒数。

在这个例子中，我们增加了 10 秒。

我们也可以使用`getTime`并将毫秒数添加到对象中。

例如，我们可以写:

```
let date = new Date('2020-01-01 10:11:55');
date = new Date(date.getTime() + 10000);
```

`getTime`获取自 UTC 时间 1970 年 1 月 1 日午夜以来的毫秒数。

10000 毫秒等同于 10 秒。

因此，我们可以将它放在`Date`构造函数中，并获得第一个`Date`实例后 10 秒的新日期。

# 存储键值对

我们可以用普通对象或`Map`实例存储键值对。

例如，我们可以写:

```
const hash = {};
hash['james'] = 123;
hash['joe'] = 456;
```

或者我们可以写:

```
const map = new Map();
map.set('james', 123);
map.set('joe', 456);
```

`'james'`和`'joe'`都是键，在两个例子中数字都是它们的值。

我们可以用 for-of 循环遍历地图。

例如，我们可以写:

```
for (const key of map) {
  console.log(key);
}
```

循环播放按键。

此外，我们可以写:

```
for (const [key, value] of map) {
  console.log(key, value);
}
```

循环访问键和值。

它还有`keys`和`values`方法，让我们循环访问键和值。

例如，我们可以写:

```
for (const key of map.keys()) {
  console.log(key);
}
```

循环播放按键。

我们写道:

```
for (const value of map.values()) {
  console.log(value);
}
```

循环遍历这些值。

# 禁用和启用 HTML 输入按钮

我们可以使用`disabled`属性来禁用或启用 HTML 输入按钮。

例如，我们可以写:

```
document.querySelector("button").disabled = true;
```

禁用按钮。

此外，我们可以写:

```
document.querySelector("button").disabled = false;
```

启用按钮。

# 显示对象的所有方法

我们可以使用`getOwnPropertyNames`来获取一个对象的所有方法。

例如，我们可以写:

```
const objMethodNames = Object.getOwnPropertyNames(obj).filter((p) => {
   return typeof obj[p] === 'function';
})
```

我们用`obj`得到属性名。

然后我们调用`filter`来返回属性名，也就是函数名。

# 获取 UTC 时间戳

为了获得一个`Date`实例的 UTC 时间戳，我们可以调用`getTime`方法。

例如，我们可以写:

```
const date = new Date().getTime();
```

或者我们可以写:

```
const date = Date.now();
```

# 用 JavaScript 输出 ISO 8601 格式的字符串

我们可以使用`toISOString`方法将`Date`实例转换成 ISO 8601 格式的字符串。

例如，我们可以写:

```
const date = new Date();
date.toISOString();
```

然后我们得到:

```
"2020-06-03T23:29:15.666Z"
```

# 在 setTimeout 回调中更改“this”上下文

我们可以使用`bind`方法在回调中设置`this`的值。

它只适用于常规功能。

例如，我们可以写:

```
const obj = {
  destroy(){
    //...
  }
}setTimeout(obj.destroy.bind(obj), 1000);
```

我们有`obj`和`destroy`方法。

我们调用`bind`将`destroy`方法绑定到`obj`。

那么`obj.destroy`中`this`的值肯定应该是`obj`。

# 让 Google Chrome JavaScript 控制台日志持久化

默认情况下，当我们刷新页面或加载新页面时，Google Chrome 控制台日志会被清除。

为了防止这种情况发生，我们可以在控制台中单击 Preserve log 来阻止它在我们刷新页面或加载新页面时被清除。

![](img/004914b9b78bad80e1a4d39544413284.png)

照片由[斯里坎特·皮塔](https://unsplash.com/@srikanth262?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以让 Chrome 控制台日志持久化。

`bind`用我们想要的`this`的值返回一个新函数。

我们可以使用`toISOString`将日期时间格式化成一个字符串。

同样，我们可以使用`getOwnPropertyNames`获取所有的键，使用`filter`返回方法的键。