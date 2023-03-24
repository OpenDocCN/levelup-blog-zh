# 角度元件生命周期挂钩概述

> 原文：<https://levelup.gitconnected.com/overview-of-angular-component-lifecycle-hooks-e33b169edd12>

![](img/d1a25c0a4fa783ba6c5aefe4e834c9d2.png)

由[zdenk Macháek](https://unsplash.com/@zmachacek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Angular 是 Google 制作的一个流行的前端框架。像其他流行的前端框架一样，它使用基于组件的架构来构建应用程序。

在这篇文章中，我们将看看一个角度组件的生命周期挂钩。

# 生命周期挂钩

Angular 零部件的生命周期由 Angular 管理。

Angular 提供了生命周期挂钩，以提供对组件关键事件的可见性，并在生命周期事件发生时采取行动。

我们可以在组件中实现一个或多个生命周期接口，这样我们就可以确保组件在特定的生命周期事件中运行代码。

没有任何指令或组件会实现所有的生命周期挂钩。Angular 只调用已定义的指令或组件。

例如，我们可以实现`OnInit`接口，在组件初始化时运行一些代码，如下所示:

```
import { Component, OnInit } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent implements OnInit {
  ngOnInit() {
    console.log("init");
  }
}
```

在上面的代码中，我们导入了`OnInit`接口，然后实现了`ngOnInit`方法。

# 生命周期序列

Angular 在特定时刻按以下顺序调用生命周期挂钩方法。

## `ngOnChanges()`

当 Angular 设置或重置数据绑定输入属性时做出响应。这个方法接收一个`SimpleChanges`对象的当前和以前的属性值。

在`ngOnInit`之前以及每当一个或多个数据绑定输入属性改变时调用它。

## `ngOnInit()`

在 Angular 显示数据绑定属性并设置指令或组件的输入属性后，初始化组件或指令。

在第一个`ngOnChanges`之后调用一次。

## `ngDoCheck()`

检测 Angular 自身无法或不愿检测到的变化并采取相应措施。

在每次变化检测运行期间以及在`ngOnChanges`和`ngOnInit`之后立即调用。

## `ngAfterContentInit()`

在 Angular 将外部内容投影到组件视图或指令所在的视图后运行。

在第一个`ngDoCheck`之后调用一次。

## `ngAfterContentChecked()`

在角度检查投射到指令或组件中的内容后运行。

它在`ngAfterContentInit`和随后的`ngDoCheck`之后被调用。

## `ngAfterViewInit()`

在 Angular 初始化组件的视图和子视图或指令所在的视图后运行。

在第一个`ngAfterContentChecked`之后调用一次。

## `ngAfterViewChecked()`

在 Angular component 检查组件的视图和子视图或指令所在的视图后运行。

它以`ngAfterViewInit`和随后的`ngAfterContentChecked`命名。

## `ngOnDestroy()`

在 Angular 销毁指令或组件之前进行清理。取消订阅观察对象并分离事件处理程序以避免内存泄漏。

在 Angular 销毁指令或组件之前调用它。

# 接口是可选的

接口是可选的，因为它没有出现在 JavaScript 中，因为 JavaScript 没有接口。

如果钩子被定义了，它们就会被调用。

然而，实现它们意味着我们不会忘记实现我们想要实现的钩子。

# 其他角度生命周期挂钩

其他角度子系统可以实现它们自己的钩子。

第三方库也可以实现它们的钩子。

# 奥尼特()

使用`ngOnInit`在构建后不久执行复杂的初始化，或在角度设置输入属性后设置组件。

因此，我们不应该在构造函数中获取数据。我们应该做的只是在构造函数中用简单的值初始化局部变量。

# OnDestroy()

我们应该将清理逻辑放在`ngOnDestroy`中。在 Angular 破坏指令之前必须运行的任何东西都应该在这里。

这是一个释放不会被自动垃圾收集的资源的地方。

我们应该在这里取消订阅 Observable 和 DOM 事件。

![](img/97afe0051eba14524218235aacbe41de.png)

[Alistair Dent](https://unsplash.com/@alistairdent?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# OnChanges()

每当输入属性改变时，就会调用`ngOnChanges`钩子。

旧值和新值都在传递给参数的`SimpleChange`对象中。

# 多切克()

使用`ngDoCheck`检测 Angular 自身无法捕捉的变化并采取相应措施。

通常，Angular 不会检测到页面上其他地方不相关数据的变化，所以我们必须在这里运行一些东西来更新这些值。

# `AfterViewInit()`和`AfterViewChecked()`

`AfterViewInit`在组件或指令与子视图一起加载后被调用。

`AfterViewChecked`紧接在`ngAfterViewInit`之后被调用。

# 内容后挂钩

AfterContent 挂钩类似于 AfterView 挂钩。不同之处在于 AfterView 挂钩关注的是`ViewChildren`，它是子组件，其元素标签出现在组件的模板中。

AfterContent 挂钩关注的是`ContentChildren`，它是投射到组件中的子组件。

# 结论

我们可以使用钩子在 Angular 组件或指令生命周期的不同阶段运行代码。

它们按顺序运行。我们可以通过实现接口来实现它们，或者在组件或指令类中用给定的名称定义方法。