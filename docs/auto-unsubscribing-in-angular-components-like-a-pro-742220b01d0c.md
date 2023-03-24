# 像专业人士一样自动退订角度组件

> 原文：<https://levelup.gitconnected.com/auto-unsubscribing-in-angular-components-like-a-pro-742220b01d0c>

## 快速处理 unsubscribe()调用的 5 个技巧，用于 Angular 组件中的大量订阅，以及为您的项目选择哪一个组件的指南

![](img/b1714601cbe43c7855640fecd1a2e8d6.png)

照片由[万德莱·隆戈](https://www.pexels.com/@derlei?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[像素](https://www.pexels.com/photo/silhouette-photography-of-steel-tower-2081132/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)中截取。

RxJS 和 observables 是每个认真的 Angular 开发者的必备技能。该框架公开了用于许多领域的可观察对象——从基本的 HTTP 通信到高级开发技术，如拦截器、防护器和解析器。迟早，有效处理大量订阅的`unsubscribe()`呼叫的需求将变得显而易见。

# 🧱基础

在角度组件的生命周期中，有时需要订阅可观察值。这样做的每一个动作都会产生一个订阅对象，当组件被 Angular runtime 破坏时，应该以一种不会导致 JavaScript 运行时内存泄漏的方式来处理这个订阅对象——这意味着调用`unsubscribe()`，通常在组件的`ngOnDestroy`方法内部。

对于每个订阅，执行此类任务的最基本方法包括两个步骤:

1.  将订阅保存到组件的私有/受保护成员变量，
2.  在`ngOnDestroy`内部，对每个声明的成员变量调用`unsubscribe()`。

对于大量这样的订阅，这变成了一件乏味的工作。以下方法可能适用于这种情况。

# 🎉@自动退订装饰

这种方法最类似于基本的手动退订方法。您仍然必须将所有订阅声明为组件成员变量(或者将订阅添加到您选择的数组中)，但是，这种方法将节省您在`ngOnDestroy`钩子中的一些输入。

通过使用`@AutoUnsubscribe`类装饰器(可以在 [GitHub](https://github.com/NetanelBasal/ngx-auto-unsubscribe) 上获得或者作为 npm 包`ngx-auto-unsubscribe`获得)，装饰器将基于在组件中定义为成员变量的订阅，在`ngOnDestroy`钩子中生成所有的`unsubscribe()`调用。

它甚至提供了额外的调整，比如订阅黑名单，定制钩子方法名来生成对订阅的成员数组的调用。查看 GitHub 库的自述文件。

```
@AutoUnsubscribe()
@Component(...)
export class MyComponent implements OnDestroy, OnInit {
  protected addressSubscription: Subscription;
  protected userSubscription: Subscription;
  ... public ngOnInit() {
    this.addressSubscription = // subscribe here or wherever else
    this.userSubscription = // subscribe here or wherever else
  }  
  public ngOnDestroy() {} // must be present ...
}
```

这个解决方案就其明确性而言是干净的——`@AutoUnsubscribe()`正好说明了一切。另外，它不会耗尽组件的组件继承能力。并且，*如果您的代码库*需要，它会列出您的组件类中所有使用的订阅。

这种方法的缺点是您必须在组件中的某个地方定义您的`ngOnDestroy`和成员变量/订阅数组。

# ✨ @UntilDestroy 装饰

GitHub 上的这个(或者 npm 包`@ngneat/until-destroy`)是这个问题的其他解决方案中的一个*新手。由*@ auto unsubscribe decorator*的作者创建，这个 decorator *利用 Angular Ivy* 的能力(但是在 Angular ≤ 8 中与旧的代码库一起工作，有一点参与，在[项目自述](https://github.com/ngneat/until-destroy#readme)中有描述)。*

这个解决方案结合了类似于*@ auto unsubscribe decorator*的原理，以及一个新特性——RxJS 操作符`untilDestroyed`。

```
**@UntilDestroy()**
@Component(...)
export class MyComponent implements OnInit {
  ... public ngOnInit() {
    this.userService.getUser()
      .pipe(**untilDestroyed(this)**)
      .subscribe();
  } // ngOnDestroy is not needed here (Angular 9+ only)!
}
```

这里，`untilDestroyed(this)`操作符工厂函数调用基本上将这个订阅标记为当组件被 Angular 运行时破坏时取消订阅。

操作符也可以在 Angular 组件上下文之外使用自定义的显式声明的对象析构函数工作(然而，JavaScript 运行时本质上仍然必须手动调用这些析构函数)，参见[项目自述文件](https://github.com/ngneat/until-destroy#readme)以获取参考。

使用订阅成员变量赋值的旧方法( *@AutoUnsubscribe-esque* )在向装饰工厂提供形式为`{checkProperties: true}`的配置对象时也能工作。

到目前为止，对于 Angular 9+来说，这个解决方案消除了它的前身*@ auto unsubscribe decorator*的主要缺陷。好样的，[项目自述文件](https://medium.com/u/b889ae02aa26#readme))并且为了取消订阅，子链接拥有`unsubscribe()`方法。

```
export class MyComponent implements OnDestroy {
  protected subs = new SubSink(); // adding the subscriptions to the sink - "setter" way
  this.subs.sink = this.userService.getUser().subscribe(...);
  this.subs.sink = fromEvent(...).subscribe(...); // adding the subscriptions to the sink - "collection" way
  this.subs.add(
    this.userService.getUser().subscribe(...),
    fromEvent(...).subscribe(...)
  ); public ngOnDestroy() {
    this.subs.unsubscribe(); // yep, this must be done manually
  }
}
```

从上面的代码片段中可以看出，这种方法需要在组件的`ngOnDestroy`钩子中进行一次手动取消订阅调用，而不是多次调用`unsubscribe()`。如果把它放在组件类定义的第一行，这个解决方案会相当清楚，只要你的代码读者知道什么是子链接。

# 📦合并订阅

这是一个不需要任何额外库或定制基础组件的解决方案——它只是一个普通的 RxJS。有一种不太为人所知的订阅方法叫做`add(aSubscription)`，它本质上将多个订阅组合成一个*超级订阅*，可以通过在超级订阅上的单个`unsubscribe()`调用来一次取消订阅组中的所有订阅。

这种技术可以模仿*订户接收器*，通过订阅一个空的临时可观察对象来创建初始的超级订阅——将这段代码片段与展示子接收器的前一段代码片段进行比较:

```
export class MyComponent implements OnDestroy {
  protected subs = **empty().subscribe();** ...
  this.subs**.add(this.userService.getUser().subscribe(...));**
  this.subs**.add(fromEvent(...).subscribe(...));**
  ... public ngOnDestroy() {
    this.subs.unsubscribe(); // yep, this must be done manually
  }
}
```

这种解决方案与子链接有相同的优点和缺点，但是与子链接相比，对于项目新手来说，这种解决方案可能更难阅读，而且`add(aSubscription)`的 API 不像子链接那样灵活。

# 🎁总结

到目前为止，您已经看到了处理 Angular 组件内部自动退订的 5 种不同技术。

显然，上述解决方案中没有一个是“一刀切”的解决方案。选择哪种技术能够很好地满足您的项目需求将取决于您必须自己解决的几个问题:

1.  我们的组件中可以容忍多少样板代码？
2.  关于*隐含行为*，比如自动退订，我们的代码库应该有多明确？
3.  我们的项目会大量使用组件继承吗？
4.  我们是否希望为此使用第三方解决方案？
5.  …以及更多。

对我来说，我在我的项目中使用了*自动取消订阅基本组件*方法，因为我喜欢解决方案的明确性和我的组件中需要使用它的最少量的样板代码。当需要扩展任何基础组件时，我只是让基础组件继承我的`AutoUnsubscribeComponent`。

然而，当我将我的代码库迁移到 Angular 9+或在 Angular 9+中开始另一个项目时，我会和我的队友一起认真考虑 *@UntilDestroy decorator* 方法——与*自动取消订阅基础组件*方法相反，基于它的解决方案不会遭受继承能力耗尽。

# 👓值得一看的文章

[](/where-shall-i-put-that-core-vs-shared-module-in-angular-5fdad16fcecc) [## “我该把它放在哪里？”-核心模块与角度共享模块

### 核心模块与共享模块的角度，专业的提示和技巧，加上一个经验法则，以确定哪个模块放置您的…

levelup.gitconnected.com](/where-shall-i-put-that-core-vs-shared-module-in-angular-5fdad16fcecc) [](/handling-loading-indicators-in-angular-applications-the-right-way-11ff8b8896ba) [## 正确处理角度应用中的负载指示器

### 一个优雅的，没有麻烦的方式来摆脱无意义的技术管道在您的业务角组件。

levelup.gitconnected.com](/handling-loading-indicators-in-angular-applications-the-right-way-11ff8b8896ba) [](/this-angular-technique-will-significantly-lower-code-duplication-in-big-projects-28fd62c3eadd) [## 这种角度技术将显著降低大型项目中的代码重复

### 告别大型项目中 ng 模板的代码重复和性能问题

levelup.gitconnected.com](/this-angular-technique-will-significantly-lower-code-duplication-in-big-projects-28fd62c3eadd)