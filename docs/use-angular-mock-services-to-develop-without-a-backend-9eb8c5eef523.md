# 使用 Angular Mock 服务在没有后端的情况下开发

> 原文：<https://levelup.gitconnected.com/use-angular-mock-services-to-develop-without-a-backend-9eb8c5eef523>

![](img/b02429a64c3635a9d4f1fda7de099d4b.png)

[埃琳娜·乔兰](https://unsplash.com/@labf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

开发一个没有后台运行的数据驱动的 UI 通常是不可能的，除非你手工换出 API。这导致开发人员运行两个终端，一个用于 UI，一个用于后端。让我们从 Angular 中的模拟服务开始，在生产模式下，使用文件替换来自动将模拟服务替换为真实服务。

1.  首先，确保您的服务使用的接口描述了服务中所有必需的方法。这将确保模拟服务和真实服务是相同的。例如:

2.在您的真实服务和您制作的副本中实现您创建的接口。下面是实际服务的例子:

3.将真实服务的文件副本重命名为类似于`todo.mock.service.ts`的名称

4.实现您的模拟服务，首先实现您创建的接口，并用模拟数据替换所有实际的 API 调用。下面是模拟服务的示例:

5.接下来在您的`src/environment.prod.ts`文件中执行覆盖。只有在使用生产配置(- prod flag)构建应用程序时，才会使用该文件。

6.最后，将一个空的`providers`数组添加到您的`src/environment.ts`文件中，然后在您的`app.module.ts`中，将来自环境文件的提供者添加到提供者数组中:

你都准备好了！现在，当您想在您的一个组件中使用服务时，请确保导入**模拟版本！**然后，当你进入生产时，那些模拟服务将自动被真正的服务所取代。

这种方法将帮助你独立于后端开发你的用户界面，用户界面和后端必须符合相同的 API。

你也可以在下面我的视频中查看完整教程:

快乐编码:)

如果您有任何问题，请随时回复，并点击👏按钮，如果你喜欢这篇文章。别忘了关注我，这样你就不会错过未来的棱角文章了！

[**学会棱角分明！**](https://click.linksynergy.com/link?id=Wp9ai2DqW4E&offerid=507388.756150&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fcourse%2Fthe-complete-guide-to-angular-2%2F)