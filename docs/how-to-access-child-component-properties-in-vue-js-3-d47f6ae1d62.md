# 如何在 Vue.js 3 中访问子组件属性

> 原文：<https://levelup.gitconnected.com/how-to-access-child-component-properties-in-vue-js-3-d47f6ae1d62>

## 从子组件访问方法和函数的有用技巧

![](img/62a9a12e9756390d22122f7fa6b5481b.png)

Pixabay 在 Pexelry 上拍摄的照片

当构建 Vuejs 应用程序时，您可能需要从外部组件访问子组件属性。这可能是在不需要发出事件的情况下。

由于 Vuejs2 中组件的开放性，可以使用 refs 访问组件。虽然在 Vuejs 3 中组件在默认情况下是关闭的，但是我们不能使用 refs 来访问组件属性。

我们可以访问的一些 Vuejs 组件属性包括函数、对象、变量等

**在 Vuejs 2(选项 API)中访问组件属性**

我们可以使用组件模板引用来访问 Vuejs 2 中的组件属性。Ref 是 Vuejs 中的一个特殊属性，在挂载之后，我们可以利用它来获得对特定 DOM 元素或子组件实例的直接引用。在下面的代码片段中，我们将看看这是如何实现的。

为了访问 Vuejs 组件属性，我们将为子组件分配一个引用 ID。参见下面的例子，我们从父组件访问子组件上的`doSomething`函数。

**子组件**

**父组件**

**访问 Vuejs 3(组合 API)中的组件属性**

默认情况下，带有脚本设置的组件是关闭的，我们无法使用 ref 访问它们的属性绑定。这意味着我们不能像 Options API 那样直接使用模板引用来访问组件属性。

在 Vuejs 3 中，我们可以利用`[defineExpose](https://vuejs.org/api/sfc-script-setup.html#defineexpose)`宏来公开一个组件实例。`defineExpose`向外部组件公开组件实例，并使用引用使它们可访问。然后父组件可以访问这些公开的属性。

**注意:**为了访问子组件属性，父组件需要通过模板引用获得子组件实例。参见下面的例子。

**用**暴露组件方法`**defineExpose**`

请记住，您可以公开任意多的属性，并且不限制要公开的属性的数量。

**从父组件访问暴露的子属性**

## **最终想法**

虽然这可能是一个很好的使用技巧，但对于一些替代方案有效的情况要谨慎。您可以研究其他替代方案，例如:

*   使用状态管理——有时使用[状态管理](https://vuejs.org/guide/scaling-up/state-management.html)更好，即(Vuex，Pinia)来防止直接在子组件上访问这些属性的开销。
*   [将事件](https://vuejs.org/guide/components/events.html)发射回父组件。如果方法是由用户操作(如单击按钮等)调用的，这可能会非常有用

偶尔，我会发送一封独家电子邮件，里面有技巧、文章、应用程序、书籍和关于技术写作的想法👇

[](https://artisanal-thinker-2556.ck.page/6e2ba71172)*】加入其他像你一样的人*

## ***更多阅读量***

*[](/handling-props-in-vue-3-composition-api-8242ee07d089) [## 在 Vue 3 合成 API 中处理道具

### Vuejs 3 中的 props 与 Composition API 简介

levelup.gitconnected.com](/handling-props-in-vue-3-composition-api-8242ee07d089) [](/vuejs-3-migrating-from-options-to-composition-api-e8a765e57b8d) [## Vuejs 3:从选项迁移到组合 API

### 轻松切换到组合 API

levelup.gitconnected.com](/vuejs-3-migrating-from-options-to-composition-api-e8a765e57b8d)*