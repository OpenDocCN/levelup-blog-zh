# Angular:在 ControlValueAccessor 中获取控制

> 原文：<https://levelup.gitconnected.com/angular-get-control-in-controlvalueaccessor-b7f09a485fba>

## 角形

## 如何在不创建新组件的情况下获取对传递给自定义组件的控件的引用。

![](img/659fbd08d1fa65256d2163eb8f82f43f.png)

这是一篇关于在 Angular 中将控件放入 *ControlValueAccessor* 的更新和改进文章。([老一](https://medium.com/@toha.marko/get-actual-control-in-controlvalueaccessor-e5bb3bb6710))

当你必须用 Angular 处理表单时，实际上，你不能没有 *ControlValueAccessor* 练习，因为它的灵活性和功能性。您可以从应用程序中的任何元素创建控件。这就是力量。

> 组件变成控件有一个简单的迭代:提供 *NG_VALUE_ACCESSOR* 令牌值，并在其中实现 *ControlValueAccessor* 接口。

具有 ControlValueAccessor 接口的控件组件

该组件现在可以在*表单组*中使用，或者简单地作为应用程序中的独立控件。

这就是这篇文章的重点——我们如何将控件注入到组件中？

首先，让我们找出可以传递给组件的控制指令的类型:

*   **表单控件**
*   **表单控件名**
*   **NgModel**

接下来，我们如何在组件中获得这些控件？解决方法很简单，只需注入 *NgControl* 参考。但是，如果你试图在构造函数中直接注入*ng control**，你会得到一个错误:“*错误:NG0200:检测到 DI 中的循环依赖……”*。*

*怎么才能解决这个问题？没错，我们可以通过*注入器*将 *NgControl* 注入到 *OnInit* 生命周期钩子中，不会出现任何错误。*

*棱角分明超级灵活！*

*向组件正确注入 NgControl*

*现在让我们认识到我们所拥有的那种控制。为此，我为注入类的构造函数定义创建了一个 switch 语句:*

*ControlValueAccessor 组件中正确注入和配置的控件*

*最后，让我们的组件结构更具逻辑性和美观性——将所有这些逻辑放在一个单独的方法中。这里的最后一个组件:*

*具有 ControlValueAccessor 的最终实现组件*

*今天到此为止。感谢您的关注。*