# 如何管理 VueJS 中的 Mixins

> 原文：<https://levelup.gitconnected.com/how-to-use-mixins-in-vuejs-a40cc3fb4428>

![](img/b04203f556544a9a0a2c9dea52c4e6e3.png)

当您的 Vue 项目开始增长时，如果您有类似的组件，您可能会发现自己一遍又一遍地复制和粘贴相同的数据、方法和观察器。当然——你可以将所有这些独立的文件作为一个单独的组件来编写，并使用 props 来定制它们，但是使用这么多的 props 很容易造成混乱。为了避免这项艰巨的任务，大多数人只是继续添加重复的代码，尽管感觉有更好的解决方案。

![](img/3649d2754033ad81eb970ceb4224f7eb.png)

谢天谢地，VueJS 神保佑我们有 mixin——这是在不同组件之间共享可重用代码的最简单的方法之一。Mixin 对象可以使用任何组件选项——数据、挂载、创建、更新等——当组件使用 mixin 时，mixin 对象中的所有信息都会混合到组件中。然后，组件将可以访问 mixin 中的所有选项，就好像它是在组件本身中声明的一样。这将是一个更有意义的例子。

```
*// mixin.js file*export default { data () { msg: ‘Hello World’ }, created: function () { console.log(‘Printing from the Mixin’) }, methods: { displayMessage: function () { console.log(‘Now printing from a mixin function’) } }} // ----------------------------------------------------------- *// main.js file*import mixin from ‘./mixin.js’new Vue({ mixins: [mixin], created: function () { console.log(this.$data) this.displayMessage() }})// => "Printing from the Mixin"// => {msg: ‘Hello World’}// => "Now printing from a mixin function"
```

正如您所看到的，使用 mixin 之后，组件包含了 mixin 中的所有数据，并且通过使用`this` *来访问它。*你也可以使用一个变量而不是一个单独的文件来定义 mixin。老实说，这是你需要了解的关于 mixins 的大部分内容，但是我认为了解更多关于某些用例以及角落用例是有用的。

# 如果有命名冲突会发生什么？

当 mixin 中的数据、方法或任何组件选项与组件中的选项同名时，组件与其 mixin 之间可能会发生命名冲突。如果出现这种情况，组件本身的属性将优先。例如，如果组件和 mixin 中都有一个`title`数据变量，那么`this.title`将返回组件中定义的值。在代码中，这看起来像:

```
*// mixin.js file*export default { data () { title: ‘Mixin’ }} // ----------------------------------------------------------- *// main.js file*import mixin from ‘./mixin.js’export default { mixins: [mixin], data () { title: ‘Component’ }, created: function () { console.log(this.title) }}// => "Component"
```

# 你完了！呃，好吧……暂时的。

和其他东西一样，VueJS Mixins 还有很多东西可以学习，但是这应该足够让你开始学习，并且涵盖了大部分用例。如果你想了解更多高级的主题，比如 Vue 中的全局混音和自定义合并设置，这些信息可以在他们的[混音文档](https://vuejs.org/v2/guide/mixins.html)中找到。我喜欢 Vue 文档，我认为它写得非常好，很容易理解。

快乐混合！

如果你有兴趣学习更多关于 Vue 3 的知识，下载我的免费的 Vue 3 备忘单，里面有一些基本的知识，比如组合 API、Vue 3 模板语法和事件处理。

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com/)[](https://gitconnected.com/learn/vue-js) [## 学习 Vue.js -最佳 Vue.js 教程(2019) | gitconnected

### 27 大 Vue.js 教程。课程由开发者提交并投票，让你找到最好的 Vue.js…

gitconnected.com](https://gitconnected.com/learn/vue-js)