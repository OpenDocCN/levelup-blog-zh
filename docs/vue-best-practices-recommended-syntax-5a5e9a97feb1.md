# Vue 最佳实践—推荐语法

> 原文：<https://levelup.gitconnected.com/vue-best-practices-recommended-syntax-5a5e9a97feb1>

![](img/3589df9c171464a477f0a099f58b448c.png)

克劳迪娅·克雷斯波在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue 是一个易于使用的前端框架。它让我们轻松地创建应用程序。

然而，当我们用它们编写应用程序时，仍然有许多事情需要我们注意。

在本文中，我们将研究使用推荐语法的最佳实践。

# 属性连字符

我们可以用连字符连接我们的属性，这样它就和 HTML 属性一致了。

例如，我们可以写:

```
<FooComponent foo-prop="prop" />
```

而不是:

```
<FooComponent fooProp="prop" />
```

# 组件名称大小写

组件名称应为 PascalCase 或 kebab case。

Vue 可以在两种情况之间转换。

例如，我们可以写:

```
<FooComponent />
```

或者:

```
<foo-component />
```

# 在新的一行中关闭 HTML 括号

在新的一行中使用 HTML 括号可以更容易地修改和阅读。

因此，我们可以考虑将它们放在一个新的行上。

例如，我们可以写:

```
<div
  id="foo"
  class="bar"
> 
  <!-- //... -->
</div>
```

而不是写:

```
<div
  id="foo"
  class="bar"> 
  <!-- //... -->
</div>
```

# 结束括号标记前没有空格

在结束括号标记之前，我们不应该有空格。

例如，我们不应该:

```
<div ></div>
```

相反，我们写道:

```
<div></div>
```

# HTML 结束标记

对于非自闭标签，我们应该有结束标签。

例如，我们应该写:

```
<div></div>
```

而不是:

```
<div>
```

# 刻痕

我们应该在 HTML 中有一些缩进。

这使得代码更容易阅读和修改。

例如，我们可以写:

```
<div class="bar">
  Hello.
</div>
```

# 引用

HTML 属性值应该加上引号。

例如，我们可以写:

```
<img src="./photo.png">
```

或者:

```
<img src='./photo.png'>
```

单引号或双引号都可以。

这样，当我们传递属性而不是设置 HTML 属性值时，就不会有语法错误。

# 自动结束标签

如果一个组件没有任何内容，它可以是自关闭的，也可以不是。

HTML 规范不支持自结束语法，所以我们在编写没有构建步骤的 Vue 代码时不应该使用它。

例如，我们可以写:

```
<BarComponent></BarComponent>
```

不是自动关闭的或者:

```
<BarComponent />
```

这是自动关闭的。

# 每行的最大属性数

我们不希望一行中有太多的属性，以至于人们不得不水平滚动来阅读所有内容。

因此，我们可能希望将属性限制在一行中。

例如，我们可能希望每行有一个属性，因此我们编写:

```
<FooComponent
  foo="1"
  bar="2"
  baz="3"
/>
```

# HTML 内容换行符

HTML 内容应该在标签之间自成一行。

这样，我们不用太费力就能知道内容的确切位置。

例如，我们可以写:

```
<div>
  multiline
  content
</div>
```

# 胡子插值间距

我们应该在胡子和里面的表情之间留一些空间。

如果在表达式的前后有一个空格，会更容易阅读。

例如，我们可以写:

```
<div>{{ msg }}</div>
```

这样，表达式很容易找到。

# 没有多个空格

有一点空间是好的，但是我们不应该有太多的空间，因为这只是浪费屏幕空间。

例如，以下是好的”

```
<i
  :class="{
    'fa-angle-up': isExpanded,
    'fa-angle-down': !isExpanded,
  }"
/>
```

冒号后面有一个空格，这足以使键和值易于阅读。

# 属性中等号两边没有空格

HTML5 允许等号周围有空格，但是没有空格会更容易阅读，而且没有空格会更容易分组。

因此，我们写道:

```
<div class="foo"></div>
```

而不是:

```
<div class = "foo"></div>
```

![](img/e0f4c83a9a6c41b02dbd6792e08a5c03.png)

[黄宏](https://unsplash.com/@andrew_wong?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 没有模板变量隐藏

我们不应该让嵌套元素或组件中的变量声明与外部元素或组件中的变量声明相同。

例如，我们不应该有如下内容:

```
<div>
  <div v-for="x in 5">
    <div v-for="x in 10"></div>
    <div slot-scope="{ x }"></div>
  </div>
</div>
```

我们有`x`，它在外部`v-for`和内部`v-for`中都声明了。

那只会混淆我们自己和 Vue。

我们应该给它们起不同的名字，尽可能减少嵌套。

所以我们应该写:

```
<div>
  <div v-for="a in 5"></div>
  <div v-for="b in 10"></div>
</div>
```

# 结论

模板中有很多我们应该避免的语法错误。

此外，坚持间距和缩进约定可以更好地格式化属性。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)