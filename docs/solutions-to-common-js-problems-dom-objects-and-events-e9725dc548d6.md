# 常见 JS 问题的解决方案— DOM、对象和事件

> 原文：<https://levelup.gitconnected.com/solutions-to-common-js-problems-dom-objects-and-events-e9725dc548d6>

![](img/0c84acea60ea083c8eadaee05300a43c.png)

照片由 [Aranxa Esteve](https://unsplash.com/@aranxa_esteve?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

经常会出现 JavaScript 问题。经常出现的问题包括 DOM 问题、字符串格式、数组等等。

在本文中，我们看看如何用 JavaScript 解决我们在 web 开发中遇到的常见问题。

# **我们如何判断设备是移动设备、台式机还是笔记本电脑？**

我们可以通过检查设备的用户代理来实现。

它可以从`navigator.userAgent`属性中获得。

例如，我们可以创建以下函数来实现这一点:

```
const detectDeviceType = () =>
  /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) ?
  'mobile' :
  'desktop';detectDeviceType();
```

在上面的代码中，我们有一个包含所有可能的移动用户代理的正则表达式。然后我们检查它们，如果为真，则返回`'mobile'`。否则，我们返回`'desktop'`。

# **如何获取当前网址？**

我们可以用`window.location.href`属性获取当前 URL。

# **如何创建一个包含当前 URL 参数的对象？**

我们可以解析一个`URLSearchParams`对象的 URL。

为此，我们可以编写以下代码:

```
const getURLParameters = url => {
 const searchParams = new URL(url).searchParams
 return [...searchParams]
}console.log(getURLParameters('http://url.com/page?n=Adam&s=Smith'));
```

在上面的代码中，我们将`url`传递给`URL`构造函数，并获取所创建对象的`searchParams`属性

然后我们可以将结果 iterable 对象分散到一个数组中并返回它。

然后当我们调用`getURLParameters`函数时，我们得到:

```
["n", "Adam"]
["s", "Smith"]
```

其中第一项是键，第二项是值。

# **如何将一组表单元素编码为一个对象？**

我们可以在`Formdata`构造函数中添加一个表单元素来提取输入到表单中的数据。

例如，假设我们有以下形式:

```
<form>
  <input name='firstName'>
  <input name='lastName'>
  <input type='submit' value='Submit'>
</form>
```

我们可以得到如下形式的值:

```
const form = document.querySelector('form');
form.onsubmit = (e) => {
  e.preventDefault();
  const formData = new FormData(form);
  console.log([...formData.entries()]);
}
```

在上面的代码中，我们用`querySelector`获取表单元素，将其传递给`FormData`构造函数，然后我们传播由`formData.entries()`返回的 iterable 对象，将其转换为数组。

然后，当我们在表单中输入一些内容时，我们会得到类似这样的结果:

```
["firstName", "john"]
["lastName", "smith"]
```

其中，每个数组的第一个条目是键，它是字段的 name 属性的值，第二个条目是输入的值。

# **如何从对象中检索给定选择器指示的一组属性？**

我们可以通过编写以下函数来实现这一点:

```
const get = (from, selector) => {
  try {
    return selector
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter(t => t !== '')
      .reduce((prev, cur) => prev && prev[cur], from)
  } catch (ex) {
    return undefined;
  }
};
```

`get`函数使用字符串选择器，用点替换任何像括号这样的字符，然后用点分隔它们，过滤掉空字符串，然后使用`reduce`通过拆分选择器到达拆分选择器条目，直到结束。

如果它在任何级别出错，那么我们返回`undefined`。

我们可以用下面的对象和函数调用来测试`get`:

```
const obj = {
  foo: {
    bar: {
      baz: 'abc'
    }
  },
  nums: [1, 2, {
    a: 'test'
  }]
};console.log(get(obj, 'nums[0]'));
console.log(get(obj, 'foo.bar'));
console.log(get(obj, 'foo.abc'));
```

前两个是有效的，因此它们返回:

```
1
{baz: "abc"}
```

最后一个无效，所以它返回`undefined`。

![](img/19100f35e1b440998bac8df6a0271c27.png)

照片由[尼古拉斯·格林](https://unsplash.com/@nickxshotz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 如何在给定元素上触发特定事件，可选地传递自定义数据？

我们可以向自定义对象中的`CustomEvent`控制器传递第二个参数。

例如，我们可以这样写:

```
document.addEventListener('build', (e) => {
  console.log(e.detail);
})document.dispatchEvent(new CustomEvent('build', {
  detail: 'bar'
}));
```

在上面的代码中，我们传递了一个带有`detail`属性的对象。然后我们通过编写`e.detail`在事件监听器中访问它。

我们应该在`console.log`里看到`'bar'`。

该对象必须具有`detail`属性。否则不会通过。

# 如何从元素中移除事件监听器？

我们可以在元素上调用`removeListener`来移除附加到元素的事件监听器。

例如，我们可以编写以下代码:

```
const fn = () => {};document.body.removeEventListener('click', fn);document.body.addEventListener('click', fn);
```

`fn`是我们的事件监听器。我们可以通过调用`removeEventListener`来移除`fn`事件监听器。

# 结论

我们可以使用`navigator.userAgent`来检查用户代理。

我们可以通过传入一个带有`detail`属性的对象来将对象传递给事件。

为了获取表单数据，我们可以使用带有表单元素的`FormData`构造函数。

我们可以使用`URL`构造函数的`searchParams`属性来获取查询字符串，并用`entries`方法提取它。