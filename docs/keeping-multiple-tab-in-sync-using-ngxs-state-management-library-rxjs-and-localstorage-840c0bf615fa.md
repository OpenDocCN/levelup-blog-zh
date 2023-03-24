# 使用 NGXS 状态管理库、RxJS 和 localStorage 保持多个选项卡同步

> 原文：<https://levelup.gitconnected.com/keeping-multiple-tab-in-sync-using-ngxs-state-management-library-rxjs-and-localstorage-840c0bf615fa>

![](img/5ba8644cebc88e0a2c2638019700112c.png)

“安全专家建议你在驾驶时持续扫描所有的后视镜”

一周前我在[这里](https://blog.angularindepth.com/keeping-browser-tabs-in-sync-using-localstorage-ngrx-and-rxjs-87de3bca4e2c)看到一篇关于“**保持多个标签同步**”的博文。

作者@ [Tim Deschryver](https://blog.angularindepth.com/@timdeschryver) 是 NGRX 图书馆团队成员之一。他有非常棒的帖子。

在这篇文章中，我决定为 Angular 编写一个与状态管理库相同的概念，名为 [**NGXS**](https://github.com/ngxs/store) 。

我认为 NGXS 为 angular 提供了更好的模式库。

> 有人想知道为什么我更喜欢使用 NGXS 而不是 NGRX，也可以看看这个帖子；"[为什么我更喜欢 NGXS 而不是 NGRX](https://blog.singular.uk/why-i-prefer-ngxs-over-ngrx-df727cd868b5) "

让我们回到正题；

1.  我们将在 angular 应用程序中使用 NGXS 进行状态管理
2.  将我们的操作名称保存到 localStorage
3.  使用`fromEvent`操作员监听`window.storage`事件

**结果；**

1.  我们将在一个选项卡中更改数据，另一个选项卡将自动更新。
2.  举个简单的例子，我们将检查登录状态。如果用户在一个选项卡中注销，其他选项卡也会将他重定向到登录页面。

**Demo App 视频；**

***完整代码在我的 GitHub profile***[**https://github.com/mehmetakifalp/ngxs-keep-browser-tab-sync**](https://github.com/mehmetakifalp/ngxs-keep-browser-tab-sync)

**StackBlitz 演示；**

> **关注我在** [**推特**](https://twitter.com/mhmtakifalp) **和** [**GitHub**](https://github.com/mehmetakifalp)

# 步骤；

**使用 CLI 创建 angular 应用程序**

**向 package.json 添加 NGXS 依赖关系**

**创建登录、注销状态、动作。**

登录状态

登录/注销操作

**使用来自 Rxjs 的 from event**

**登录组件**

**在 App 模块导入**

# 完整演示！

[](https://stackblitz.com/edit/angular-rxjs-localstorage-fromevent?embed=1&file=src/app/app.component.ts&view=preview) [## angular-rxjs-local storage-from event-stack blitz

### 保持多个浏览器标签与 ngxs、localStorage 和 Rxjs 同步

stackblitz.co](https://stackblitz.com/edit/angular-rxjs-localstorage-fromevent?embed=1&file=src/app/app.component.ts&view=preview) 

感谢阅读！下一篇文章我打算写一下 **addEventListener 与 fromEvent** 的比较。

在 github 跟随我:【https://github.com/mehmetakifalp 

跟我来中音:【https://medium.com/@mehmetakifalp 

> 来，[在 Twitter 上向我们问好。](https://twitter.com/ngturkiye)我们是一群来自土耳其的 Angular 爱好者，围绕 Angular 组织聚会、发布文章、贡献开源项目。— NG 土耳其