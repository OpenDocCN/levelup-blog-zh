# Vue.js 面试问题帮你顺利获得工作机会

> 原文：<https://levelup.gitconnected.com/vue-js-interview-questions-to-help-you-get-job-offers-smoothly-d42b06a2a48b>

## 了解这些知识点有助于你通过面试，获得 offer！

![](img/0acad01cf13c76c8faabe63cdaf25038.png)

最近朋友的公司在招聘 Vue 前端工程师，让我帮忙整理一些面试问题，于是我就从前端面试官的角度出发，把 Vue 框架的一些重要特性，框架的原理以问题的形式来组织总结。了解这些知识点有助于你通过面试，获得 offer！

## 1.说说你对 SPA 单页的理解，它的优缺点是什么？

单页应用程序(SPA)是一种 web 应用程序或网站，它通过使用来自 web 服务器的新数据动态重写当前网页来与用户交互，而不是 web 浏览器加载整个新页面的默认方法。目标是更快的过渡，使网站感觉更像一个本地应用程序。

在 SPA 中，页面永远不会刷新；相反，所有必需的 HTML、JavaScript 和 CSS 代码要么由浏览器通过一次页面加载来检索，要么根据需要动态加载适当的资源并添加到页面中，通常是为了响应用户操作。

**优势:**

*   良好快速的用户体验，内容更改不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
*   基于以上观点，SPA 对服务器的压力相对较小；
*   前端和后端职责分离，结构清晰，前端负责交互逻辑，后端负责数据处理；

**缺点**

*   **初始加载需要大量时间:**为了实现单页面 web 应用的功能和显示效果，在加载页面时需要统一加载 JavaScript 和 CSS，有些页面是按需加载的。
*   **前进后退路由管理:**由于单页面应用在一个页面上显示所有内容，浏览器的前进后退功能无法使用，所有页面切换都需要自己建立堆栈管理。
*   **SEO 难度更大:**由于所有内容都是动态替换显示在一个页面上，所以在 SEO 上有天然的弱点。

## 2.`v-show`和 v-if 有什么区别？

`v-if`是真正的条件呈现，因为它确保了条件块内的事件监听器和子组件在切换期间被适当地销毁和重新创建；同样懒惰:如果初始渲染时条件为假，则什么也不做——条件块不会开始渲染，直到条件第一次变为真。

`v-show`要简单得多——无论初始条件如何，元素总是被呈现，并且简单地基于 CSS 的“显示”属性进行切换。

因此， `v-if` 适用于运行时很少改变条件，不需要频繁切换条件的场景；`v-show`适用于需要非常频繁切换条件的场景。

## 3.Vue 可以通过直接给数组项赋值来检测变化吗？

由于 JavaScript 限制，Vue 无法检测到对以下数组的更改:

当你通过索引直接设置一个数组项时，例如:`vm.items[indexOfItem] = newValue`
当你修改数组的长度时，例如:`vm.items.length = newLength`

## **4。父组件可以监听子组件的生命周期吗？**

如果父组件监听子组件的挂载，它会做一些逻辑处理，这可以通过下面的写法来实现:

以上需要通过`$emit`手动触发父组件的事件。更简单的方法是，当父组件引用子组件时，可以通过`@hook`进行监控，如下图所示:

## 5.谈谈你对 keep-alive 的理解？

`keep-alive`是 Vue 的内置组件，可以保持所包含组件的状态，避免重新渲染。它具有以下特点:

通常与路由和动态组件结合使用来缓存组件。
提供 include 和 exclude 属性，都支持字符串或正则表达式，include 表示只缓存名称匹配的组件，exclude 表示不缓存任何名称匹配的组件，其中 exclude 的优先级高于 include。
对应激活和停用两种钩子功能，当组件激活时，触发已激活的钩子功能，当组件移除时，触发已停用的钩子功能。

## 6.为什么组件中的数据是函数？

为什么组件中的数据必须是函数然后返回对象，而在新的 Vue 实例中，数据可以直接是对象？

因为组件是用于复用的，而 JS 中的对象是引用关系，如果组件中的数据是对象，那么作用域不是孤立的，子组件中的数据属性值会相互影响。如果组件中的数据选项是函数，那么每个实例都可以维护返回对象的独立副本，组件实例之间的数据属性值不会相互影响；而且新 Vue 的实例不会被重用，所以不存在引用对象的问题。

## 7.v-model 的原理？

我们主要使用 vue 项目中的 v-model 指令在表单输入、textarea、select 等元素上创建双向数据绑定。我们知道 v-model 本质上是语法糖，v-model 在内部用于不同的输入元素、不同的属性并抛出不同的事件:

*   text 和 textarea 元素使用 value 属性和输入事件；
*   复选框和单选按钮使用选中的属性和更改事件；
*   “选择”字段的值为“属性”,而“更改”为“事件”。

以输入表单元素为例:

`<input v-model=’something’>`

相当于:

`<input v-bind:value=”something” v-on:input=”something = $event.target.value”>`

如果在一个定制组件中，v-model 将默认使用一个适当的命名值和一个名为 input 的事件，如下所示:

## 8.vue-router 的路由方式有哪些？

vue 路由器有三种路由模式:哈希、历史、抽象。对应的源代码如下:

**hash:** 使用 URL 哈希值进行路由。支持所有浏览器，包括不支持 HTML5 历史 Api 的浏览器；

**历史:**取决于 HTML5 历史 API 和服务器配置。详见 HTML5 历史模式；

**摘要:**支持 Node.js 服务器端等所有 JavaScript 运行时。如果没有找到浏览器 API，路由将自动强制进入此模式。

**好了，今天我们就整理这些，剩下的以后再写，希望今天整理的 8 个面试问题对你有帮助！谢谢大家！**

推荐阅读 JavaScript 设计模式系列文章，如果对我的文章感兴趣，可以在 [Medium](https://hyhwell.medium.com/) 或者 [Twitter](https://twitter.com/Maxwell_hyh) 上关注我。

[](/javascript-design-patterns-observer-pattern-1cf90cffb1e2) [## JavaScript 设计模式:观察者模式

### 观察者

图案 Observerlevelup.gitconnected.com](/javascript-design-patterns-observer-pattern-1cf90cffb1e2) [](/javascript-design-patterns-strategy-pattern-c013d3dbc059) [## JavaScript 设计模式:策略模式

### 学习设计模式的目的是代码的可重用性，使代码更容易被其他人理解，并且…

levelup.gitconnected.com](/javascript-design-patterns-strategy-pattern-c013d3dbc059) [](/javascript-design-patterns-singleton-pattern-7ada98be9a10) [## JavaScript 设计模式:单例模式

### Singleton 模式:将类实例化的次数限制为一次，一个类只有一个实例，并且…

levelup.gitconnected.com](/javascript-design-patterns-singleton-pattern-7ada98be9a10)