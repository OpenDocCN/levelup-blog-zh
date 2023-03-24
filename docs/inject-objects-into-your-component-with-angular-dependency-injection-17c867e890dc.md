# 使用角度依赖注入将对象注入到组件中

> 原文：<https://levelup.gitconnected.com/inject-objects-into-your-component-with-angular-dependency-injection-17c867e890dc>

![](img/c649544df9c9e21a35db16d9c04d8cad.png)

在 [Unsplash](https://unsplash.com/s/photos/order?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[zdenk macha ek](https://unsplash.com/@zmachacek?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

依赖注入是一种常见的处理依赖的设计模式。当您需要依赖项时，您不必自己创建依赖项实例，而是将此任务留给依赖项注入机制。这种机制在需要时实例化依赖关系，并允许您访问这些实例，以避免创建和处理同一事物的太多实例。

Angular 是一个完整的 JavaScript 框架，它有自己的依赖注入机制。大多数时候，我们所说的依赖是服务。要了解更多关于如何注入服务的信息，你可以查看这篇文章。但是依赖注入并不局限于服务。你可以用它来注入(几乎)任何你喜欢的东西，比如物体。然而，虽然语法快捷方式使得注入服务非常直接，但是在注入其他类型的对象时会有更多的工作。

在本文中，我们将展示如何通过将 web 存储注入到 Angular 服务中来实现这一点。

# 将 Web 存储与 Angular 一起使用

`localStorage`和`sessionStorage`是窗口属性，您可以在 Angular 代码中像在控制台中一样访问它们。但是通常更好的做法是将对外部 API 的访问包装在服务中。这样，如果 API 发生变化，或者如果您决定为您的本地属性使用另一个存储，您只需要适应一个地方。这样的服务可能如下所示:

然后，您将通过您的`PersistenceService`从 *localStorage* 中存储和检索属性，而不是直接存储和检索。

但是这样做，你仍然对浏览器 API 有一个隐含的依赖。如果缺少该 API(例如，如果您在服务器上渲染)或在测试时，可能会导致问题。

更干净的选择是为 Web 存储创建一个依赖注入(DI)令牌，并将其注入到我们的持久性服务中。

# 依赖注入令牌

当您在组件中注入服务时，您将传递一个 *DI 令牌*给组件注入器。默认情况下，令牌是服务的类名，因此您可以编写如下代码:

```
@Component({
  selector: 'app-test', 
  templateUrl: './test.component.html', 
  styleUrls: ['./test.component.scss'],
  providers: [TestService]
})
```

但这只是 Angular 让你走的一条舒适的捷径。提供程序数组的完整语法如下:

```
providers: [{ provide: TestService, useClass: TestService }]
```

provider 对象定义了如何获取与阿迪令牌相关联的依赖关系。在上面的例子中，我们告诉 Angular 在请求由“name”`TestService`标识的依赖项时使用类`TestService`。服务的令牌是它们自己的类名，这导致了冗余，这也是 Angular 允许捷径的原因。对象没有类名，要注入一个对象，我们必须首先为它创建阿迪令牌。

# 使用注入令牌注入存储

您可能会想到使用接口名`Storage`作为令牌，但这只能用于具有运行时表示的对象。Web 存储 API `Storage`是一个**接口**，它只存在于 TypeScript 中，但是在代码被传给 JavaScript 之后。因此我们不能直接注射。

对于这种情况，您必须使用构造函数创建一个`InjectionToken`对象。当创建我们的令牌时，我们指定一个工厂函数，它将返回一个类型为`Storage`的对象:属性`localStorage`。这将是默认值。

然后我们可以使用`@Inject`装饰器注入存储。

# 重写提供程序中的注入标记

以这种方式注入`Storage`接口还有另一个好处。您可以用不同的存储注入`PersistenceService`。假设有这样一种情况，您希望将数据存储在`sessionStorage`而不是`localStorage`中。你可以在你想要的组件中使用一个提供者，并在请求使用`STORAGE`时返回`sessionStorage`。

在这个例子中，组件将拥有自己的`PersistenceService`实例，它使用`sessionStorage`作为存储，而同一服务的其他实例将使用`localStorage`。

从 Angular 6.0.0 开始，可以通过在装饰器中声明`providedIn: 'root'`来在`root`中提供服务。手动在一个模块、另一个服务或组件中提供一个服务已经变得越来越少，因为在`root`中提供一个服务对于大多数用例来说已经足够了。但是你几乎可以注入任何东西，不仅仅是服务，在某些情况下，比如`localStorage`这是一种让你的代码更干净、更易维护的方式。要注入一个对象，您必须创建自己的 DI 令牌。然后，您将能够使用`@Inject`装饰器注入您的对象，并在`providers`数组中提供它。