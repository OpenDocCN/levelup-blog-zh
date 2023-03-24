# 在 React 应用程序中改变我的依赖注入方法

> 原文：<https://levelup.gitconnected.com/changing-my-approach-to-dependency-injection-in-a-react-app-4f73e1efe82f>

![](img/47f523872b0716d531eb4f2008c5a967.png)

戴安娜·波列希纳在 Unsplash 上的照片

我最近从下面的帖子中受到启发，使用 [React Context](https://reactjs.org/docs/context.html) 将一种依赖注入形式合并到 ReactJS 应用程序中。

[](https://blog.testdouble.com/posts/2021-03-19-react-context-for-dependency-injection-not-state/) [## 为依赖注入而不是状态管理反应上下文

### 在 React 应用程序架构的讨论中，React 上下文经常被作为一种实现“状态……

blog.testdouble.com](https://blog.testdouble.com/posts/2021-03-19-react-context-for-dependency-injection-not-state/) [](https://dev.to/dansolhan/simple-dependency-injection-functionality-for-react-518j) [## React 的依赖注入/服务模式(受 Angular 启发)

### 在 Angular-development 团队中工作了几年之后，学习 React 对我来说是一件令人兴奋的事情，而且它更…

开发到](https://dev.to/dansolhan/simple-dependency-injection-functionality-for-react-518j) 

然而，我最终简化了方法，并且*而不是*合并了上面的模式。让我们从我最初的实现开始，它非常接近其他作者:

然后，为了使用我的服务，我将它包装在一个子组件中，如 EmployeeSearchForm:

最后，我在 EmployeeSearchForm 组件中使用它:

这一切都很好，我很高兴在 React 中获得了类似依赖注入的感觉，那么我为什么要把它去掉呢？

在使用它之后，我并没有感觉到我比仅仅将我的服务作为一个常规文件导入然后像上面那样使用得到了更多。我担心未来的应用程序开发者会被这种模式所迷惑，最终会把它剥离出来，或者至少不会复制它，把这种孤儿模式留在应用程序中。

所以最终我将我的 employee_data_client 拆分成一个 EmployeeDataClient 服务，并简单地导入它。该服务看起来像这样:

然后，我只需导入该服务，并在需要获取结果时调用 EmployeeDataClient.get_results()。我仍然能够隔离和测试/模拟客户端，尽管这并不是真正的“依赖注入”。