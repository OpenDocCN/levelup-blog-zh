# 在我们的 Vue 应用中采用的最佳 JavaScript 实践

> 原文：<https://levelup.gitconnected.com/best-javascript-practices-to-adopt-in-our-vue-apps-d086436c2b59>

![](img/20723c438af766431ff1293f92af255a.png)

Andrew Small 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究如何为 Vue 应用程序使用正确的 JavaScript 语法。

# 尾随逗号

我们可能希望在对象属性的末尾添加尾随逗号。这使得它们很容易重新排列，区别也更明显。

例如，我们可以写:

```
const foo = {
  bar: "baz",
  qux: "bar",
};
```

然后我们可以重新排列它们，如果没有逗号的话，就不必键入逗号。

# 组件名称大小写

默认情况下，组件名称是 PascalCase。它们也可能是烤肉串大小写，以便与 HTML 语法兼容。

# 点符号

如果我们使用点符号，我们应该有一致的间距和缩进。

例如，我们可以写:

```
const foo = obj
  .prop;
```

或者:

```
const foo = obj.prop;
```

# 使用正确的比较运算符

`===`和`!==`更适合于等式比较。

这是因为它们在进行比较之前不进行任何数据类型转换。这样，我们就不会从比较中得到意想不到的结果。

# 对象中键和值之间的间距

键和值与对象之间的间距应该一致。

例如，我们可以写:

```
const obj = { foo: 2 };
```

冒号前没有空格，冒号后有一个空格。

这样，很容易阅读，我们也不用打那么多字。

# 关键词间距

我们应该始终如一地隔开我们的关键词。

例如，我们可以写:

```
if (baz) {
  //...
} else if (bar) {
  //...
} else {
  //...
}
```

在`if`和`else`之后我们还有一个空位。

这使得我们的代码更容易阅读。

# 让我们的组件匹配我们的文件名

组件名应该与我们的文件名匹配，这样我们就可以根据文件名很容易地找到我们的组件。

例如，如果我们有以下组件:

```
<template>
  <div>//..</div>
</template><script>
export default {
  name: "FooComponent"
};
</script>
```

那么我们应该把它所在的文件命名为`FooComponent.vue`。

# 最大线路长度

我们代码中的最大行长度应该是 100 个字符或更少。这样，我们不必水平滚动来阅读整行。

# 没有布尔默认值

我们不应该为布尔属性设置默认值。设置 HTML 属性默认值的标准方式是将它们设置为`false`。因此，为了一致性，我们应该做同样的事情。

例如，我们不应该写:

```
<template>
  <div>//..</div>
</template><script>
export default {
  name: "FooComponent",
  props: {
    foo: {
      type: Boolean,
      default: true
    }
  }
};
</script>
```

相反，我们写道:

```
<template>
  <div>//..</div>
</template><script>
export default {
  name: "FooComponent",
  props: {
    foo: Boolean
  }
};
</script>
```

# 没有空的析构模式

我们不应该有空的析构模式。

拥有它们对我们没多大帮助。

例如，我们不应该写:

```
const {a: {}} = foo;
```

相反，我们写道:

```
const {a: { b }} = foo;
```

我们也可能将空析构误认为默认值。

例如，我们可能想写:

```
const {a = {}} = foo;
```

而不是写:

```
const {a: {}} = foo;
```

# 不规则空白

我们不想要不规则的空白字符。

如果我们使用它们，那么如果我们在不同的编辑器或平台中打开我们的文件，我们可能会有问题。

# 没有保留的组件名称

我们不应该使用 HTML 元素的名称作为组件名称。

例如，我们不应该使用 div 或 span 作为组件名:

```
<script>
export default {
  name: 'span'
}
</script>
```

# 没有静态内联样式

静态样式应该在`style`部分，而不是作为元素中`style`属性的值。

例如，不写:

```
<div style="color: pink;"></div>
```

我们应该写:

```
<div :style="styleObj"></div>
```

其中`styleObj`是一个对象。

![](img/8a9696b1622bbeab8a555f8436962082.png)

照片由[MIO·伊藤](https://unsplash.com/@mioitophotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 没有模板目标空白

我们不应该在没有`rel='noopener noreferrer'`的`a`标签中有`target='_blank'`。

如果没有它，我们可能会将我们的应用程序暴露于一个安全漏洞，我们可以在未经授权的情况下访问跨来源的资源。

所以我们应该写:

```
<a link="http://example.com" target="_blank" rel="noopener noreferrer">click me</a>
```

如果我们建立联系。

# 结论

我们应该有清晰的语法，不要用保留字来命名我们的组件。

此外，我们不应该有一个没有 rel 属性的目标空白，以避免安全漏洞。

我们也不应该有无用的代码，在我们的应用程序中不做任何事情。