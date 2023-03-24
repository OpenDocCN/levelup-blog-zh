# Angular 使用自定义指令优化*ngFor

> 原文：<https://levelup.gitconnected.com/angular-optimize-ngfor-with-a-custom-directive-3e39504000cd>

![](img/75a2e66330e3256b0c8d0fac49988ccd.png)

这是一篇关于如何增强 Angular 内置 [*ngFor](https://angular.io/api/common/NgForOf) -directive 的短文。`*ngFor`是一个广泛使用的角度指令，通过迭代来呈现列表或集合。一个缺点是这会影响应用程序的性能。让我们探究一下原因:

我们迭代这个团队并输出一个球员列表。现在，如果我们的集合的数据改变了(例如，通过 API 请求)，Angular 不知道哪个项目被添加或删除了。因此，Angular 将重新呈现完整的列表。随着列表变得越来越大，这会降低我们的应用程序的速度。

为了解决这个问题，我们可以添加一个`trackBy`函数。`trackBy`函数将索引和当前条目作为参数，并返回一个唯一的标识符。

为了避免给每个组件添加一个`trackBy`函数，我们可以实现一个自定义指令，只对整个应用程序执行一次:

由于`*ngFor`只是该指令的速记语法，它将扩展为长格式(参见[正式文档](https://angular.io/api/common/NgForOf#description)，我们可以使用`ngForTrackById`作为选择器来选择 app 中的所有`*ngFor`指令。该指令有一个通用类型`T`,它扩展了一个自定义的`Item`,可以用于所有具有`id`属性的集合。

现在只需在`*ngFor`上添加一个`trackById`，我们的指令就会被添加:

我们可以通过添加一个定制属性来代替`id`来进一步增强该指令

然后我们可以跟踪玩家，例如，通过`name`

在 [Medium](https://saackef.com/) 或 [Twitter](https://twitter.com/sw3eks) 上关注我，阅读更多关于 Angular 的内容！