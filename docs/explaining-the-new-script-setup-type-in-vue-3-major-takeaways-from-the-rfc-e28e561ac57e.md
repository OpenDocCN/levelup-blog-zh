# 解释 Vue 3 中新的脚本设置类型 RFC 的主要优点

> 原文：<https://levelup.gitconnected.com/explaining-the-new-script-setup-type-in-vue-3-major-takeaways-from-the-rfc-e28e561ac57e>

如果你最近一直在使用 [Vite 和 Vue 3](https://learnvue.co/2020/12/setting-up-your-first-vue3-project-vue-3-0-release/) ，你会注意到当你开始一个新项目时，你的脚本部分看起来像这样，在你的 Vue 组件中有这个语法。

你可能会疑惑，*“这是什么？这是选项 API 吗？作文 API？设置方法在哪里？”*

[<脚本设置>类型是在 Vue 的 Git RFC](https://github.com/vuejs/rfcs/blob/script-setup-2/active-rfcs/0000-script-setup.md)中提议的变更。需要明确的是，这并不是要完全取代当前任何一种编写代码的方式。它的目的是为开发人员提供一个更简洁的语法来编写他们的单个文件组件。

在这篇文章中，我们将看看它是如何工作的，以及它的一些有用之处。

好吧——我们走。

# 脚本设置的概要

在`<script setup>`中，我们不必声明一个`export default`和一个`setup`方法——相反，**所有的顶级绑定都暴露给模板**

在 Composition API 中，我们习惯于创建我们的设置方法，然后返回我们想要公开的任何内容。大概是这样的:

但是有了`<script setup>`，我们可以像这样重写相同的代码..

而且不仅仅是数据、[计算属性](https://learnvue.co/2019/12/mastering-computed-properties-in-vuejs/)和方法！甚至[导入的指令](https://learnvue.co/2020/01/creating-your-first-vuejs-custom-directive/)和我们设置范围顶层的组件在我们的模板中也自动可用。

看看这个导入组件的例子。

很神奇，对吧？

# 所以…这有什么意义？

长话短说，这种语法使单个文件组件更简单。

用 RFC 的原话来说:

> *“该提案的主要目标是通过将* `*<script setup>*` *的上下文直接暴露给模板来减少 sfc 中组合 API 使用的冗长性。”*

这正是我们刚刚看到的，通过不必担心创建一个`setup`方法并返回我们想要公开的内容，我们可以简化我们的代码。

加上**——没有忘记从我们的设置方法中返回一些东西**(我知道我一直在做的事情)。

既然我们知道了 even 是什么以及它为什么有用，让我们看看它的一些更高级的特性。

# 访问道具，发射事件等。

在 Composition API 中，这些只是我们设置方法的参数，

```
setup (props, context) { // context has attrs, slots, and emit }
```

然而，在脚本设置语法中，我们可以从 Vue 的 3 个导入中访问这些相同的选项。

*   `defineProps`——顾名思义，允许我们为组件定义道具
*   `defineEmits`——让我们定义组件可以发出的事件
*   `useContext` -允许我们访问组件的插槽和属性

有了这 3 个导入，我们可以获得我们习惯于传统设置方法的功能。

# 创建异步设置功能

脚本设置的另一个很酷的特性是创建一个异步设置函数是多么容易。

这对于在创建组件时加载 API 非常有用，甚至可以将代码与[实验性<悬念>特性](http://link)捆绑在一起。

要使我们的设置函数异步，我们所要做的就是在脚本设置中使用一个顶级的 await。

例如，如果我们正在使用 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ，我们可以像这样使用 await

…我们得到的`setup()`函数也将是异步的。

就这么简单。

# 使用普通

`<script setup>`为其顶级绑定创建自己的脚本范围。但在某些情况下，有代码规定**必须在模块范围内执行。**

本 RFC 中的两个具体示例是…

1.  申报指定出口
2.  产生只执行一次的全局副作用。

这可以通过像这样在脚本设置旁边添加一个普通的`<script>`块来实现。

# 现在你知道了！

目前，这个脚本设置是自愿加入的，所以如果你想尝试一下，**只需将设置添加到你的脚本标签。**

或者…

如果你不想去想它，只想用你习惯的方式写代码，那就去做吧。选择权在你。

要了解更多关于脚本设置的信息，[这里是完整 RFC](https://github.com/vuejs/rfcs/blob/script-setup-2/active-rfcs/0000-script-setup.md) 的链接，包括它的动机、精确语法和更多技术实现。

这就是这篇文章的全部内容，我希望它能帮助你弄清楚你的 Vite 应用程序中的新语法是什么！

如果你有任何问题，请在下面的评论中留言！

[***如果你有兴趣了解更多关于 Vue 3 的知识，请下载我的免费 Vue 3 备忘单，里面有一些基本知识，比如组合 API、Vue 3 模板语法和事件处理。***](https://learnvue.co/vue-3-essentials-cheatsheet/)

![](img/3d50ac4ec6748bd4c29bd83276873799.png)

*原载于 2021 年 5 月 14 日 https://learnvue.co**[*。*](https://learnvue.co/2021/05/explaining-the-new-script-setup-type-in-vue-3-major-takeaways-from-the-rfc/)*