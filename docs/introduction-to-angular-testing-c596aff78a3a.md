# 角度测试简介

> 原文：<https://levelup.gitconnected.com/introduction-to-angular-testing-c596aff78a3a>

## 隔离、浅层、集成和端到端测试。是什么，什么时候用？

![](img/84f89578e36a0aa170620045d57e600d.png)

西蒙·艾布拉姆斯在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 Angular 中，我们有许多不同类型的测试。可以想象你看到的时候有很多疑问。我有这个问题，所以我深入了解了一下。在这篇文章的最后，我希望这些对你也有意义。

# 角度测试工具

在 Angular 中，我们使用一些不同的工具来建立自动化测试。

如果您已经使用 CLI 创建了 Angular 项目，并且没有说它应该忽略测试，那么 CLI 的测试功能已经为您设置好了。

默认情况下，Angular CLI 将 Jasmin 设置为我们的测试框架，将 Karma 设置为我们的测试运行程序。但是如果你想利用 Jest 或者其他测试框架，你可以自由地这样做。

# 角度测试的类型

在 Angular 中，我们有 4 种不同的主要测试类型。

*   独立单元测试
*   浅层单元测试
*   集成测试
*   端到端测试

# 独立单元测试

一个单元可能包含需要隔离测试的业务逻辑。

有几个角度单位可以单独测试。

*   管
*   服务
*   班级
*   成分
*   指令

在孤立中，我们应该总是嘲笑我们的依赖，否则，就不再是孤立了。

```
import { FormComponent } from './my-form.component'describe('NgqFormComponent', () => {
	let component: FormComponent
	let mockApiService beforeEach(() => {
		mockApiService = jasmine.createSpyObj([
			'logout',
			'init',
			'getApiJson',
			'getCurrentRoute',
			// add morehere
		]) component = new FormComponent(mockApiService)
	}) it('should be defined', () => {
		expect(component).toBeDefined()
	})
})
```

例如，FormComponent 依赖于`apiService`。所以我们用 Jasmine 的 createSpyObj 来模拟它，它用我们真正的服务所拥有的所有公共方法来模拟服务。

由于这个方法，我们还可以测试某个方法是否被调用。当需要重写一个方法时，我们也可以这样做。

在独立单元测试中，我们不测试组件的模板部分，只测试它背后的逻辑。在这个测试中，我们测试了所有具有预期行为的方法。

# 浅层单元测试

使用浅层单元测试，我们用模板测试一个组件，但是我们忽略了子组件的渲染。通过将 NO_ERRORS_SCHEMA 传递到我们的配置中，我们可以实现这一点。否则你会得到很多错误。

```
beforeEach(async(() => { TestBed.configureTestingModule({
   declarations: [
    FormComponent,
  ],
   imports: [
    BrowserModule,
   ],
  ],
  schemas: [NO_ERRORS_SCHEMA]
  })
  .compileComponents();
 }));
```

# 集成测试

在集成测试中，我们测试两个或更多的组件如何相互协作。当组件相互依赖时，我们可以这样做。

当您想要一起测试更多的组件时，您可以通过测试模块导入它们。

```
beforeEach(async(() => { const todo1 = new TODOItem('Buy milk', 'Remember to buy milk');
  todo1.completed = true;
  const todoList = [
   todo1,
   new TODOItem('Buy flowers', 'Remember to buy flowers'),
  ]; TestBed.configureTestingModule({
   declarations: [
    AppComponent,
    NavbarComponent,
    TodoListComponent,
    TodoItemComponent,
    FooterComponent,
    AddTodoComponent,
    TodoListCompletedComponent,
  ],
   imports: [
    BrowserModule,
    NgbModule.forRoot(),
    FormsModule,
    appRouterModule
   ],
   providers: [
    {provide: APP_BASE_HREF, useValue : completedTodoPath },
    {
     provide: TodoListService,
     useValue: {
      todoList: todoList
     }
    }
   ]
  })
  .compileComponents();
}));
```

# 端到端测试

使用端到端测试，我们在一个工作的应用程序中测试应用程序的各个部分。我们可以测试前端和后端的工作组合。

对于这种类型的测试，我们可以使用 [Selenium](https://www.selenium.dev/) 、[量角器](https://www.protractortest.org/#/)、 [Cypress](https://www.cypress.io/) 、 [Webdriver.io](https://webdriver.io/) 。

另一种选择，检查这个伟大的职位。

[](https://medium.com/@aswinkumar4018/which-e2e-testing-framework-to-use-for-js-based-client-applications-fbcac9aab680) [## 哪个 E2E 测试框架用于基于 JS 的客户端应用程序？

### 让我们为基于 Javascript 的客户端应用程序确定合适的 E2E 测试框架，并提出有效的 E2E…

medium.com](https://medium.com/@aswinkumar4018/which-e2e-testing-framework-to-use-for-js-based-client-applications-fbcac9aab680) 

端到端测试也是测试不同设备上不同浏览器之间差异的好方法。有多种服务可以帮助您在 CI/CD 中进行设置。

*   [浏览器堆栈](https://www.browserstack.com/)
*   [酱油实验室](https://saucelabs.com/)
*   [测试机器人](https://testingbot.com/)

# 谢谢

我们了解了 Angular 中有哪些类型的测试以及何时使用。对我来说，这很有意义。

如果你对 Angular 的一些测试有疑问，请在评论中告诉我。我会尽全力进一步帮助你👍

> 最终，我们都需要别人的帮助。所以不要害羞！只是问问😉

*快乐编码🚀*

# 阅读更多

[](https://medium.com/dev-together/3-todos-before-applying-for-a-junior-developer-job-26fc0d8ba2b9) [## 申请初级开发人员工作前的 3 件事

### 我见过很多初级开发人员的求职申请。大多数申请都是出于好意…

medium.com](https://medium.com/dev-together/3-todos-before-applying-for-a-junior-developer-job-26fc0d8ba2b9) [](https://medium.com/dev-together/how-to-learn-javascript-the-easy-way-2aa37007c481) [## 如何以最简单的方式学习 JavaScript？

### 1.先从理论开始！

medium.com](https://medium.com/dev-together/how-to-learn-javascript-the-easy-way-2aa37007c481) [](https://medium.com/better-programming/tips-to-create-developer-tutorials-62cb3a25b8e5) [## 创建开发人员教程的技巧

### 想写更多的教程，但你不知道从哪里开始？从这里开始

medium.com](https://medium.com/better-programming/tips-to-create-developer-tutorials-62cb3a25b8e5) [](https://medium.com/dev-together/use-cli-tools-from-mac-linux-on-windows-subsystem-for-linux-37d16f012489) [## 在适用于 Linux 的 Windows 子系统上使用 Mac/Linux 中的 CLI 工具

### WSL2 如此强大，使得从 Mac 到 wsl 2 的转换轻而易举。

medium.com](https://medium.com/dev-together/use-cli-tools-from-mac-linux-on-windows-subsystem-for-linux-37d16f012489) [](https://medium.com/better-programming/why-tutorials-wont-make-you-a-professional-developer-271108c74ddb) [## 为什么教程不能让你成为专业的开发者

### 精通不仅仅来自于做教程。尝试，失败，学习，重复！

medium.com](https://medium.com/better-programming/why-tutorials-wont-make-you-a-professional-developer-271108c74ddb)