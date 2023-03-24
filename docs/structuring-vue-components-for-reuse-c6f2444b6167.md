# 构建 Vue 组件以供重用

> 原文：<https://levelup.gitconnected.com/structuring-vue-components-for-reuse-c6f2444b6167>

VueJS 是一个用于单页应用的超灵活的前端 JavaScript 框架，下面是一个关于如何构建你的应用的小建议，使它们更易于管理。

![](img/6b5c3fd40df4c4316a4feaed8a2d7fc8.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

VueJS 在如何构建应用程序方面非常宽松，尤其是与 Angular 相比，Angular 对于使用模块和组件创建应用程序有着相对严格的指导原则。

另一方面，VueJS 不在乎。开箱即用的 Vue CLI(带有路由器选项)为您创建了一个非常基本的文件夹结构。它添加了一个 src 文件夹，其中包含视图和组件的子文件夹。

存在于任一文件夹中的组件在结构和工作方式上都是相同的。这两个文件夹纯粹是作为如何最好地分离组件的指南而存在的。

# 数据组件

存储在`src/views/`

这是您的布线元件。导入路由器文件并用作入口点的组件都应该放在这里。

这里的组件应该访问数据，不管它是在你的 Vuex 存储中还是通过直接访问你的 API。

我倾向于让这些组件给页面布局添加结构。他们设置各种容器、行、列和总体布局。然后，他们导入表示组件，并将所有收集到的数据传递给它们。

这些组件通常也是用于处理交互的组件。对数据的任何更改(比如表单提交)都将被传递回这些组件，然后这些组件与任何 API 或商店进行交互。

# 演示组件

存储在`src/components/`

这些就像一袋锤子一样愚蠢！他们不知道数据来自哪里，也不知道他们用这些数据做什么。它们所做的就是通过 props 接收数据，并通过事件发出结果。

这就是你的重用的来源。因为这些组件应该完全没有对状态的访问，所以它们变得超便携，不仅在数据源之间，而且在应用程序之间。

像 Vuetify 这样的框架是纯粹表示组件的很好的例子。它们通过 props 接受数据，并在点击或搜索等事件发生时发出，供您在应用程序中处理。

在一个理想的世界中，一切最终都会归结为一个表象的组成部分。

`App.vue`将设置初始布局并创建路由器视图...

`views/SomePage.vue`将检索它需要的数据，并将其传递给`SomeTable.vue`...

当排序改变时，`components/SomeTable.vue`将显示数据并将事件传递回来...

![](img/15c490fb786326309ba9597557e22e27.png)

想在另一个项目中重用`SomeTable.vue`？去吧！和第一个项目的数据完全解耦！

*原载于*[*https://mattlaw . dev*](https://mattlaw.dev/blog/structuring-vue-components-for-reuse/)*。*