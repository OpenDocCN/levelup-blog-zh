# 依赖图地狱(iOS)

> 原文：<https://levelup.gitconnected.com/problem-dependency-graph-hell-1405d17dcb92>

![](img/e2d574d78897dd53c092f1578f848e06.png)

由 [charlesdeluvio](https://unsplash.com/@charlesdeluvio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/arrows?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我在拥有数十或数百个依赖项的大型代码库中看到的一个常见模式是，父对象知道依赖图中它下面的所有依赖项。请考虑以下情况:

> 父代:A，B，C，D，E
> —子代 1: A，B，C，E
> — —孙代 1: E
> — —孙代 2: B，C

向一个*子*或*孙*对象添加一个新的依赖项需要对所有知道该对象的对象执行[猎枪手术](https://refactoring.guru/smells/shotgun-surgery)…一个改变可能影响 20 或 30 个文件。

在上面的例子中，在相互之间传递这些对象涉及到许多样板文件。这也导致了我们的测试中有很多模仿和间谍的样板文件。这些对象实际上充当了[中间人](https://refactoring.guru/smells/middle-man)，除了将对象向下传递之外，不做任何事情。

理想情况下，变更应该被隔离以减少合并冲突，对象应该很小以减少认知负荷，因此，随着项目的增长，减少样板代码是必须的。

为了实现良好的模块化，你不应该仅仅为了创建一个使用两个模块的父对象而需要导入每一个模块，所以在编写独立模块时，减少这些未使用的导入是必须的。

## 解决方案:服务定位器模式

这就是 ***服务定位器*** 模式的用武之地，所有对象都可以访问一个[单例](https://refactoring.guru/design-patterns/singleton)依赖容器，并且可以只请求它们需要的依赖，以便正常工作。

测试这些对象仅仅涉及到提供依赖，你甚至可以强制测试不能使用*“真实的”*依赖。

使用服务定位器模式，我们的依赖图看起来更像:

> 父代
> —子代 1: A
> — —孙代 1: E
> — —孙代 2: B，C

特别是在大型代码库中，这可能是天赐之物，我们可以隐藏大量样板代码，并专注于测试较小的代码片段，注入我们需要的依赖关系。

这实际上就是 SwiftUI 中的`@Environment`和`@EnvironmentObject`属性包装器，您可以覆盖预览和测试的默认设置。

## 包:DependencyContainer

我已经创建了一个[*dependency container*](https://github.com/cjnevin/DependencyContainer)包，它使用一个属性包装器`@Dependency`来帮助我们保持依赖图的精简，就像上面的例子一样。

在我们的单元测试中，我们可以提供这些依赖项的模拟版本，例如:

模拟/间谍依赖

而在我们的实现中，我们可以提供真正的依赖关系。

真正的依赖

## 如果你感兴趣的话，我已经创建了一个[示例项目](https://github.com/cjnevin/IdealCleanArchitecture)，它涵盖了这一点和许多其他概念。

## 你也可以在自己的项目中包含[这个包](https://github.com/cjnevin/DependencyContainer)。

*感谢阅读并享受！*