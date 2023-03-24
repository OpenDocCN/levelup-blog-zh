# JavaScript 技巧——时差、睡眠等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-date-difference-sleep-and-more-deb5d84dc362>

![](img/b385296ca7a4e4e83ba4e4d806aa9c68.png)

照片由[布鲁斯·马尔斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 暂停节点程序一段时间

我们可以用`setTuimeout`方法暂停一个节点程序一段时间。

例如，我们可以写:

```
setTimeout(() => console.log('pause'), 3000);
```

我们还可以创建一个带有`setTimeout`函数的承诺。

例如，我们可以写:

```
const sleep = (ms) => {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

我们创建了一个`sleep`函数来返回一个承诺。

`resolve`超时后调用。

`ms`是以毫秒为单位的超时持续时间。

然后我们可以通过写来使用它:

```
const run = async () {
  console.log('foo');
  await sleep(1000);
  console.log('bar');
}
```

我们调用`sleep`在两个控制台日志之间暂停`run`功能。

# 在 React 中更新嵌套状态属性

我们可以通过使用扩展操作符来更新嵌套状态属性。

例如，我们可以写:

```
this.setState({ foo: { ...this.state.foo, flag: false } });
```

然后我们用新的值`flag`设置`foo`状态，同时保留剩余的`this.state.foo`对象。

`setState`用于组件类。

如果我们使用钩子，我们可以写:

```
setFoo({ ...foo, flag: false });
```

其中`setFoo`是`useState`返回的状态变化函数。

# 如何通过值获取 JavaScript 对象中的键

我们可以通过反转键和值来获得 JavaScript 对象中的键。

然后我们可以通过反转对象的键得到值。

为此，我们可以使用下划线的`invert`方法。

例如，我们可以写:

```
const obj = {
  foo: 3,
  bar: 4
};

(_.invert(obj))[3];
```

我们翻转了`obj`键和一个值，并用`invert`返回新对象。

然后我们就可以通过原对象的值得到密钥。

Lodash 也有同样的方法。

# 将参数向前传递给另一个 JavaScript 函数

要将参数传递给另一个函数，我们可以使用`apply`方法。

例如，我们可以写:

```
const a = (...args) => {
  b.apply(null, args);
}const b = (...args) => {
  console.log(args);
}
```

然后我们可以调用`a`:

```
a(1, 2, 3);​
```

我们用想要传入的参数调用`a`函数。

然后我们用`apply`用同样的参数调用`b`。

`args`是传入的参数数组。

同样，我们可以使用 spread 操作符来扩展参数，而不是使用`apply`:

```
const a = (...args) => {
  b(...args);
}const b = (...args) => {
  console.log(args);
}
```

# Moment.js 日期时间对比

我们可以通过使用`diff`方法来比较日期和时间。

例如，我们可以写:

```
moment().diff(dateTime, 'minutes')
```

`moment()`和`dateTime`都是矩对象。

我们在`moment()`上调用`diff`来与`dateTime`进行比较，并通过传入`'minutes'`来获得分钟的差异。

`moment()`获取当前日期时间作为 moment 对象。

# Javascript 中默认导入的别名

为了在 JavaScript 中给默认导入起别名，我们可以随意命名它。

例如，我们可以写:

```
import alias from './module';
```

以名称`alias`导入`module`的默认导入。

或者我们可以写:

```
import { default as alias } from './module';
```

做同样的事情。

# 将数组转换为 JSON

要将数组转换成 JSON，我们可以在数组上调用`JSON.stringify`。

例如，我们可以写:

```
const str = JSON.stringify(arr);
```

其中`arr`是数组。

# 从对象数组中获取不同的值

为了从一个对象数组中获得不同的值，我们可以使用`map`方法，将项目放入一个集合中以删除重复项。

例如，我们可以写:

```
const distincts = [...new Set(arr.map((item) => item.id))]
```

我们调用`arr`上的`map`方法将所有的`id`属性值放入一个数组中。

然后我们使用`Set`构造函数从`id`值的数组中创建一个集合来删除重复的值。

最后，我们用 spread 操作符将集合转换回数组。

![](img/df8a424688990e055ea74711164ef5ef.png)

凯特·斯通·马西森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以通过使用承诺来暂停程序。

集合对于删除重复项非常有用。

我们可以使用 rest 操作符来获取一个对象的所有参数。