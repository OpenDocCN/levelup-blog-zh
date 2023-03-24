# 一个完全独立的角形按钮，可以隐藏任何父按钮

> 原文：<https://levelup.gitconnected.com/a-completely-self-sufficient-angular-button-that-can-hide-any-parent-7066770c567a>

## 庆祝独立

![](img/061bcdae9510edf257b5675b9e5cec37.png)

图片来源:Freepik

Angular 最有趣的事情之一——是的，我说有趣*和*我不是在讽刺——是想出了创建完全模块化的组件的方法，并且可以以即插即用的态度执行功能。例如，我创建了一个按钮，只需将它的组件包含在您要隐藏的组件中，就可以隐藏任何父组件。

# 从通用按钮组件开始

每一个好的组件的核心都是绝对最低限度的基础。如果您想要利用模块化方法，请确保您使用的构建块足够灵活，能够在其上进行构建，并且是松散耦合的，这样它们就不会在以后变得受限。换句话说，您希望构建可以自信地更改的组件，因为您知道这些更改将被隔离，不会引起意外的连锁反应。

第一步是创建一个按钮组件，它将成为所有其他按钮的基础。例如，下面是我用于我的博客网站的[的基本按钮组件。它给开发者一个选项来为按钮添加他们想要的任何内容，如`<ng-content>`所示。](https://cloudengineering.studio/articles)

**button.component.html**

```
<button [ngClass]="class" [disabled]="disabled">
  <ng-content></ng-content>
</button>
```

# 向按钮添加 CSS

**button.component.scss**

开发人员可以通过将 disabled 属性设置为 true 来禁用按钮。我添加了 CSS 使禁用按钮的不透明度为 0.25，这样它看起来也是禁用的。

我没有为悬停状态硬编码颜色，而是设置了一个过滤器，使亮度为 125%，这样它就可以应用于任何按钮，不管它是什么颜色。我还添加了光标，以便在悬停时变成手形，或者更准确地说是指针，因为这不是默认行为。

开发人员也可以选择没有边框或填充。

**button.component.ts**

# 创建可以隐藏其父级的按钮

我为用户添加了输入来传递标签，如果需要的话可以选择默认图标。如果开发人员想要使用应用于按钮的样式，例如字体大小、填充、图标大小等，这是很有用的。

**button-hide-parent.component.html**

**按钮-隐藏-parent.component.ts**

通过创建一个 [ElementRef](https://angular.io/api/core/ElementRef) 的实例，我可以很容易地获得对组件的引用，从而获得组件的父组件，而不必查询 DOM。通过使用[渲染器 2](https://angular.io/api/core/Renderer2) ，我能够安全地定位父组件并应用样式`display:none`。

我还添加了一个 EventEmitter 以防父母需要知道父母被隐藏了。

# 使用新建按钮

要使用 new 按钮，请在 HTML 模板中包含您想要隐藏的组件的选择器。不需要额外的代码来使它工作。

**注意:**你可以在按钮上指定另一个 click 事件处理程序，以防你想在父的 typescript 文件中添加另一个函数。通过这样做，单击按钮将调用子方法和父方法。