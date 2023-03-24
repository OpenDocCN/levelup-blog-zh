# 更多前端开发新手的错误

> 原文：<https://levelup.gitconnected.com/more-front-end-developer-newbie-mistakes-c24f899b99d6>

![](img/0e67b4651a80b667b19f1f40dce2bd5e.png)

索菲亚·米勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

开发人员很容易犯错误，但如果我们事先知道这些错误，我们就可以防止它们。

在这篇文章中，我们将看看新手前端开发人员犯的错误，我们都可以避免。

# 过于依赖框架

前端框架很棒。但是我们不应该过分依赖它。如果我们不使用我们习惯使用的框架，我们仍然可以自己做事情。

此外，只有当我们精通普通 JavaScript 结构和 DOM 操作时，我们才应该学习框架。

否则，我们不知道为什么所有的东西都工作。

# 使用引导程序进行布局

当 flexbox 和 grid 发布的时候，或者当它还没有在浏览器中广泛使用的时候，Bootstrap 是很棒的。

现在我们可以使用这两者来创建页面布局。甚至 Bootstrap 本身也使用 flexbox 和 grid 进行布局。

因此，我们应该使用 flexbox 和 grid 进行布局，而不是 Bootstrap。过于依赖 Bootstrap 对我们没有太大的帮助。我们会忘记基本原则。

相反，我们应该坚持使用 flexbox 和 grid，这使得布局非常容易。

# 将我们的代码放在一个地方

JavaScript 将模块作为标准已经有几年了，所以没有理由再使用长脚本了。

相反，我们应该使用模块将代码分成易于管理的部分。

此外，我们不应该再有全局变量了。相反，我们有我们想要在模块中导出的变量。

如果我们想把所有东西打包成一个定制的组件，我们可以使用 web 组件 API 来实现。这样，我们可以在任何地方使用它们。

用 framework 创建的项目都把我们的代码分成模块。他们通过将事物分成模块来鼓励最佳实践。

# 没有优化图像

许多人没有快速的互联网连接。即使地球上很多地方都有宽带，但质量可能不是最好的。

因此，我们应该确保压缩和缩小图像，使它们的文件大小不会太大。

我们不能有很多兆字节大小的图像。用较慢的宽带连接下载它们仍然需要很长时间。

为了测试较慢连接的性能，我们可以在 Chrome 控制台的网络选项卡上模拟加载我们的网站或应用程序。

我们可以选择 3G、4G 和普通连接。

# 使用内嵌样式

内嵌样式是不好的，因为它们把我们的页面弄得乱七八糟。相反，我们应该通过将它们移动到 CSS 文件中来清理它们。

这样，我们就不会用样式把页面弄得乱七八糟，而且我们可以在多个地方重用相同的样式。

# 不使用标题标签进行样式设计

标题标签是很好的样式，告诉我们它们是标题。合适的标题对 SEO 有影响。

因此，我们应该把标题改成 h1，h2，…，h6 标签，如果它们实际上是标题的话。

![](img/fb92bef138ecb3091f64f5c0aa176354.png)

由 [Alvan Nee](https://unsplash.com/@alvannee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 不删除多余的样式

多余的风格是一种痛苦。像 div 这样的块元素有 100%的宽度是多余的。

我们不需要将 div 的宽度设置为 100%,因为默认宽度是父 div 宽度的 100%。

Chrome 经常告诉我们哪些样式是多余的，这样如果我们在控制台中看到警告，我们就可以删除它们。

# 以错误的方式嵌入字体

@font-face 用于为必须导入的字体指定字体名称。

但是，我们也可以在@font-face 块中指定字体的粗细，以控制字体的字体粗细，并在常规字体块和字体粗细不同的块中保持相同的名称。

例如，不要写以下内容:

```
@font-face {  
  font-family: 'Open Sans';  
  src: url('opensans.woff2');
}@font-face {  
  font-family: 'Open Sans Bold';  
  font-weight: bold;
  src: url('opensans-bold.woff2');
}
```

我们应该写:

```
@font-face {  
  font-family: 'Open Sans';  
  src: url('opensans.woff2');
}@font-face {  
  font-family: 'Open Sans';  
  src: url('opensans-bold.woff2');
}
```

然后，我们可以在我们的样式中设置字体粗细，然后从列表中具有正确字体粗细的字体或其他样式中选择正确的字体。

例如，我们可以如下使用开放式 San:

```
.foo {  
  font-family: 'Open Sans';  
  font-weight: bold;
}
```

然后，浏览器将选择将`font-weight`设置为粗体的开放 Sans 字体。

# 结论

内嵌样式是不好的，我们应该把它们移到一个 CSS 文件中，这样我们就可以重用它们了。

此外，我们应该通过将样式放在@font-face 块中来适当地嵌入字体。

最后，我们应该考虑连接速度较慢的用户，优化我们的资产。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)