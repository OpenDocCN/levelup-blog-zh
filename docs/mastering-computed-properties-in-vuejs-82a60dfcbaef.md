# 掌握 VueJS 中的计算属性

> 原文：<https://levelup.gitconnected.com/mastering-computed-properties-in-vuejs-82a60dfcbaef>

![](img/0868bafa1339419348022a5fcad88fa2.png)

对于一个程序员来说，没有什么比盯着一段代码，花上几年的时间去破译到底发生了什么更令人沮丧的了。在 VueJS 中工作时，最常见的使代码混乱的方法之一是在模板中添加很长的表达式。例如，假设我们想要呈现一个反转的字符串，没有空格，全部大写。如果我们使用一个模板内表达式来做这件事，它看起来会像这样。

```
<span> {{text.replace(‘ ‘, ‘’).reverse().toUpperCase()}}</span>
```

现在你可能比我更像一个 CS 专业人员，但是这对于我来说太杂乱了，以至于我看一眼就知道它在做什么。现在想象一下，必须在一个模板的多个地方使用这个值，并且在代码中使用这个长表达式。这就是使用 VueJS 的计算属性的时候了。

# 好的，但是它们是如何工作的呢？

现在我们知道了为什么计算属性是有用的，让我们回顾一下如何使用它们。用最基本的术语来说，我们只是在 Javascript 导出中添加一个字段，并在那里定义我们的计算属性。对于上面的例子，它看起来像这样。

```
export default { data () { text: ‘hello world’ }, computed { formattedText: function () { return this.text.replace(‘ ‘, ‘’).reverse().toUpperCase() } }}
```

既然我们已经定义了计算属性，那么在模板中呈现它就变得非常容易了。这就是我们要做的一切。

```
<span> {{ formattedText }}</span>
```

就是这样。现在，在我们的网页上，我们应该以一种更加清晰易读的方式来呈现 DLROWOLLEH。使用计算属性总是使您的项目更具可伸缩性，因为如果我们想要更改呈现的值，我们只需更改计算属性，而不必遍历并更改整个模板中的每个表达式。

# 那么，计算出的属性到底什么时候改变呢？

对于我们的例子，formattedText 总是依赖于 Text 的值。因此，如果我们要将文本更改为“ABCDEF”，那么 formattedText 将返回“FEDCBA”。然而，为了更有效地运行，VueJS 缓存计算的属性值。VueJS 只会在依赖关系改变时重新计算属性。在我们的例子中，它只会在文本改变时重新计算。否则，它将返回上次更改的缓存值。

为了完全理解计算属性何时会改变，我们必须理解它们的依赖关系。简单地说，计算属性的依赖项是帮助属性确定返回值的值。如果这些都没有改变，那么将再次返回缓存的值。

> **如果没有改变依赖关系，则不会重新计算计算属性。**

VueJS 文档中的以下示例显示了一个永远不会重新计算的计算属性。

```
computed: { now: function () { return new Date() }}
```

尽管 computed 属性现在返回一个一直在变化的值，但是 VueJS 并不关注依赖项。因此，它永远不会重新计算。

如果你不喜欢这样，你可以使用常规的 VueJS 方法，默认情况下，每次渲染时都会重新计算。

# 您还可以为计算属性定义一个 setter

默认情况下，计算属性是只读的，不能设置。但是，如果您想为计算属性添加一个钩子，以允许设置它的依赖项。你要做的就是下面这些。

```
computed: { formattedText: function () { get: function () { return this.text.replace(‘ ‘, ‘’).reverse().toUpperCase() }, set: function (value) { this.text = value; } }}
```

# 计算属性与观察器有何不同？

计算属性和观察器都能够基于数据更新值。虽然所有计算属性都可以使用观察器编写，但使用计算属性通常更具可读性和效率。以下面的例子为例，首先编写为观察器，然后编写为计算属性。

```
export { data () { return { name: ‘Matt’, age: 19, output: ‘’ } }, watch: { name: function (val) { this.output = this.name + ‘ is ‘ + this.age + ‘ years old.’ }, age: function (val) { this.output = this.name + ‘ is ‘ + this.age + ‘ years old.’ } }}// Now as a computed propertyexport { data () { return { name: ‘Matt’, age: 19 } }, computed: { nameAndAge: function () { return this.name + ‘ is ‘ + this.age + ‘ years old.’ } }}
```

如您所见，computed 属性要简单得多，冗余性也小得多，同时在数据发生变化时仍能提供相同的响应能力。

# 给你。

您应该具备使用计算属性的所有基础知识。希望这有助于您精简代码，使其更具可读性和易于理解。让我知道你有什么建议！

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/vue-js) [## 学习 Vue.js -最佳 Vue.js 教程(2019) | gitconnected

### 27 大 Vue.js 教程-免费学习 Vue.js。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/vue-js) 

如果你有兴趣了解更多关于 Vue 3 的知识，请下载我的免费 Vue 3 备忘单，里面有一些基本知识，比如合成 API、Vue 3 模板语法和事件处理。