# 带有错误处理和验证的动态 Javascript 表单

> 原文：<https://levelup.gitconnected.com/dynamic-javascript-form-with-error-handling-and-validation-a4b96604940d>

## 一个表单，用 React、Typescript、hooks、react-hook-form、material UI 和 Yup 来管理它们。

![](img/3b1e843f1113ac79e87ed6f18bda2aeb.png)

表单是大多数 web 应用程序的重要组成部分，也是用户输入和提交数据的主要方式。这意味着它经常出现在整个应用程序的多个页面和组件上。

这些表单可能包含许多类似的输入字段和预期行为，涉及到**验证、类型检查、表单提交和错误处理。**

> 所有这些重复的代码会增加技术债务，并且更难以创建测试。

我创建的动态表单解决方案使得在不同的屏幕和视图上重用表单变得简单。每个表单的属性都存储在一个对象数组中，可以作为 props 传递给自定义表单组件。

表单组件使用 [Yup](https://www.npmjs.com/package/yup) 进行表单验证，使用 [react-hook-form](https://react-hook-form.com/get-started) 进行表单提交和错误处理。 [Typescript](https://www.typescriptlang.org/) 用于类型检查， [material UI](https://mui.com/) 组件库提供表单组件和样式。

希望对你有帮助

## 动态表单、验证、类型检查和错误处理。

## 动态输入字段

## 表单属性和实例化

我希望你已经发现这是有用的，感谢您的阅读。如果你喜欢这个，你可能也会喜欢我们在[创造的其他一些博客帖子和创意！！书呆子](https://www.notnotnerdy.com/)。每个月都会推出新的设计。