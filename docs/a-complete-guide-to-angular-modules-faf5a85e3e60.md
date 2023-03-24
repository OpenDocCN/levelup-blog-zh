# 角度模块完全指南

> 原文：<https://levelup.gitconnected.com/a-complete-guide-to-angular-modules-faf5a85e3e60>

## 有角的

## 学习 Angular 的 NgModule

![](img/1b5fd8613830e5350f6e4d87fabb767b.png)

由 [Sushant Vohra](https://unsplash.com/@sushant_vohra?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/vietnam-cafe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在本文中，我将通过以下方式分享我对角度模的理解:

1.  什么是 NgModule？
2.  ng 模块代码示例
3.  NgModule 范围(角度)
4.  流行的角度模块
5.  使用通用模块进行共享
6.  加载时间的延迟加载模块

*更多类似内容，请查看*[*https://betterfullstack.com*](https://betterfullstack.com)

# 什么是 NgModule？

一个**模块**是一种将相关的**组件**、**指令**、**管道**和**服务**进行分组的机制，通过这种方式可以与**其他模块**组合起来创建一个应用。

你可以理解 ngModule 是一个**类**，它允许我们定义私有和公共方法。此外，Angular 模块可以导出或隐藏组件、指令、管道和服务。

导出的元素被其他模块使用，而没有导出的元素只是在模块本身内部使用，不能被其他模块直接访问。

NgModule 元数据执行以下操作:

*   声明哪些**组件**、**指令**和**管道**属于该模块。
*   公开一些**组件**、**指令**和**管道公共**，以便**其他模块**的组件模板可以使用它们。
*   **用**当前模块中的组件需要**的**组件**、**指令**和**管道**导入**其他模块。
*   提供其他应用程序组件可以使用的服务。

# ng 模块代码示例

在这一节，我想谈谈 Angular 中 ngModule 的结构。

## 根模块

创建新应用时， [Angular CLI](https://angular.io/cli) 生成以下基本`AppModule`。

创建角度项目时的默认 AppModule

为了引导我们基于模块的应用程序，我们需要通知 Angular 哪个是我们的根模块，以便在浏览器中执行编译。

自举角度应用的主要技术

## 创建特征模块

通过`ng g module MODULE_NAME`创建一个特征模块。该语法将使用以下元数据在`app`文件夹中为您生成一个模块:

ng 模块元数据

下面是一段描述:

1.  **声明**
    属于该模块的可声明*类*、*组件*、*指令*和*管道*的列表。
2.  **提供者**
    依赖注入提供者的列表。
3.  **导入**
    一列*模块*应该折叠到这个模块中。
4.  **导出**
    一个声明列表——*组件*、*指令*和*管道*类。
5.  **entryComponents**
    可以被*动态加载*到视图中的组件列表。

# NgModule 范围(角度)

为了讨论角度模块中的范围，我们将提到四件事:

1.  **声明** 组件、指令和管道必须属于*恰好*一个模块。它们都在该模块的局部范围内。
2.  **提供者** *依赖注入提供者*的列表将可用于注入模块中的任何组件、指令、管道或服务。
    如果是在角度应用中全球可用的根注射器，则无需注册这些提供者。

根注射器服务

如果您想要**在**之外的模块中使用一个声明元素，您**必须导出它，并在您想要使用的模块中导入** **它**。

# 流行的角度模块

我认为有 6 个主要的角度模块，在角度应用中随处可见。

1.  [BrowserModule](https://angular.io/api/platform-browser/BrowserModule)
    当你想在浏览器中运行你的应用时。你可以从`main.ts`中看到。
2.  [CommonModule](https://angular.io/api/common/CommonModule)
    要用`[NgIf](https://angular.io/api/common/NgIf)`、`NgFor`的时候。
3.  [表单模块](https://angular.io/api/forms/FormsModule)
    当你想要构建模板驱动的表单时(包括`[NgModel](https://angular.io/api/forms/NgModel)`)。
4.  [ReactiveFormsModule](https://angular.io/api/forms/ReactiveFormsModule)
    当您想要构建反应式表单时。
5.  [RouterModule](https://angular.io/api/router/RouterModule)
    当你想使用`[RouterLink](https://angular.io/api/router/RouterLink)`、`.forRoot()`和`.forChild()`时。
6.  [HttpClientModule](https://angular.io/api/common/http/HttpClientModule)
    当你想向服务器发送请求时。

# 使用通用模块进行共享

创建共享模块可以让您更好地组织代码，降低复杂性，因为您可以将常用的指令、管道和组件放入一个模块中，然后在应用程序的其他部分中您需要的任何地方导入该模块。

angular.io 的样本共享模块

有三件事需要你注意:

1.  它导入了`[CommonModule](https://angular.io/api/common/CommonModule)`，因为模块的组件需要公共指令。
2.  它声明并导出实用管道、指令和组件类。
3.  它重新输出`[CommonModule](https://angular.io/api/common/CommonModule)`和`[FormsModule](https://angular.io/api/forms/FormsModule)`。

另一个案例研究:

当我们使用 angular 材质时，我们必须创建一个材质模块，并将该模块共享给 Angular 应用程序的其他模块。

# 加载时间的延迟加载模块

**默认**，NgModules 被急切加载，这意味着只要角度应用**加载**，所有 NgModules 也会加载**，无论它们是否是立即需要的，这将导致性能问题。**

对于有很多路由的大型应用程序，考虑惰性加载——一种设计模式，即**按需加载 ng modules**。惰性加载**有助于保持初始包尺寸更小**，这反过来又有助于**减少加载时间。**

角度应用中的延迟加载模块

注意:

1.  所有惰性加载模块都是**路由特征模块**。
2.  任何模块都不应导入懒惰加载的路由特征模块**。**

如果你觉得这篇文章有用！你可以在[媒体](https://medium.com/@transonhoang?source=post_page---------------------------)上关注我。我也在[推特](https://twitter.com/transonhoang)上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

[](https://betterfullstack.com/stories/) [## 故事-更好的全栈

### 关于 JavaScript、Python 和 Wordpress 的有用文章，有助于开发人员减少开发时间并提高…

betterfullstack.com](https://betterfullstack.com/stories/)