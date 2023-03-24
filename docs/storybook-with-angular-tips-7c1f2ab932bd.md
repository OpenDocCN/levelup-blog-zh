# 有棱角的故事书

> 原文：<https://levelup.gitconnected.com/storybook-with-angular-tips-7c1f2ab932bd>

## 如何更有效地使用故事书和角状图的技巧

![](img/cb560f3efde9cdbed665ada4bd23d163.png)

[杰斐逊·桑托斯](https://unsplash.com/@jefflssantos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

**先决条件:**

*   角度项目设置
*   故事书已安装(查看我的[安装文章](https://medium.com/swlh/setup-storybook-with-angular-10-d4203099a2f)

下面是我如何格式化我的`.stories.ts`文件的例子:

如您所见，我导入了`TodoModule`,而不是声明`TodoComponent`,然后导入它的依赖项。这使得故事更易于维护，因为每当您更改模块中的导入时，您不必返回并更新它们。

我知道有些开发人员不会为每个组件创建模块，但是我发现这使得组件更加易于移植，因为它们不与父组件模块耦合(例如`AppModule`)。对于 Storybook 来说，它也使设置变得容易得多。

# **温馨提示:**

*   你可以为你的故事创建文件夹(例如`storiesOf('Components/Todo', module)`)
*   您可以使用`template`属性(例如),而不是为故事使用`component`属性。`<app-todo [todo]="todo" (deleteTodo)="deleteTodo($event)"></app-todo>`。值`"todo"`和`"deleteTodo($event)"`仍然来自于`props`对象，但是如果需要的话，这允许您在组件周围添加更多样式
*   签出[故事书控件](https://storybook.js.org/docs/react/essentials/controls)来改变从故事书用户界面传递给你的组件的值

**查看我在 Angular 和 Storybook 上的其他文章:**

 [## 设置角度为 10°的故事书

### Storybook 允许您在传递不同的输入()和……时，在您的角度组件上编写各种“测试用例”

medium.com](https://medium.com/swlh/setup-storybook-with-angular-10-d4203099a2f)  [## 使用 Angular Mock 服务在没有后端的情况下开发

### 开发一个没有后台运行的数据驱动的 UI 通常是不可能的，除非你手工换出 API。这个…

levelup.gitconnected.com](/use-angular-mock-services-to-develop-without-a-backend-9eb8c5eef523)  [## 记录角度应用

### Docker 允许开发人员在所有类型的操作系统上轻松传输和执行应用程序，而无需…

jakecyr.medium.com](https://jakecyr.medium.com/dockerize-an-angular-application-a45fbbf46de2) [](/preventing-angular-subscription-memory-leaks-65f18eb9cb8e) [## 防止角度订阅内存泄漏

### 不要让你的应用程序变慢，处理好你的订阅

levelup.gitconnected.com](/preventing-angular-subscription-memory-leaks-65f18eb9cb8e)