# 开始实现您自己的 Typescript 属性装饰器

> 原文：<https://levelup.gitconnected.com/start-implementing-your-own-typescript-property-decorators-ae1e7025fefe>

![](img/fd85a8c35f4208d6c1838111f7b83de5.png)

物业装饰

# 什么是室内设计师？

它是一种结构设计模式，通过将这些对象放在包含行为的特殊包装器对象中，允许您将新的行为附加到这些对象上([引用](https://refactoring.guru/design-patterns/decorator))。

# 什么是物业装饰？

在 Typescript 文档中没有对属性修饰符的具体定义，您可以在那里找到以下内容:

```
A *Property Decorator* is declared just before a property declaration. A property decorator cannot be used in a declaration file, or in any other ambient context
```

在文档的一个注释中，您可以找到这个，它为我们提供了更多信息:

```
A property decorator can only be used to observe that a property of a specific name has been declared for a class.
```

我们将使用的模式是为当前对象设置一些元数据，并在其他功能中使用它们。

# 幕后是怎么回事？

您的属性装饰器实际上是一个简单的函数，在运行时作为函数调用，它有两个参数:

*   如果属性是*静态*，则为类的构造函数，如果属性是*实例*成员，则为类原型
*   成员的姓名

你知道我们无权访问`property`吗？我们在这个函数中返回的内容也将被忽略！这就是为什么我们之前说这个装饰器只是用来观察的。

# 设置

为了运行 Typescript 代码，我们需要使用 Typescript 编译器来编译它们。

我们需要一个`tsconfig.json`文件:

tsconfig.json

我们必须启用`experimentalDecorators`。还有，目标不能少于`ES5`。也使用`reflect-metadata`类型，否则你会得到一些类型错误。

如果您不想使用`tsconfig`文件，您可以直接传递这些选项:

```
tsc --experimentalDecorators // If you installed tsc globaly
npx tsc --experimentalDecorators // If you installed tsc in your current directory
```

现在，通过在当前目录中运行`tsc`，typescript 文件将被编译成 Javascript 文件，我们可以使用 Node 运行它们。([参考](/start-writing-your-own-typescript-method-decorators-c921cdc3d1c1))

# 定义我们的属性装饰器

## 1-示例:只记录参数

让我们首先记录一个简单示例中的参数:

日志属性参数

我们有一个拥有三个成员的用户，其中一个成员是静态的:`maxDailyUsage`。让我们看看运行这段代码后的日志:

```
{ firstArgument: {}, propertyName: 'email' } // line 10
{
  firstArgument: [class User] { maxDailyUsage: 12 }, // line 13
  propertyName: 'maxDailyUsage'
}
```

在静态情况下，我们可以看到**用户类构造函数**和**初始化值**。如果我们在别的地方初始化这个静态成员的值，我们会在日志中看到 undefined。

例如 member，我们可以看到属性和类原型的名称，它在控制台中显示一个空对象，但是类成员在这个对象中是可用的。

下一个例子是真实用法。

## 2-示例:审查用户的敏感数据

在这个例子中，我们有一个用户，它有一些敏感的字段，如密码和卡号。当有人在用户对象上调用`toString()`函数时，我们想要审查一些字段。

对于这个例子，我们使用**反射 API** 来设置元数据并读取元数据。

它被称为反射，因为它反映了关于对象的信息。它仍然没有在原生 Javascript 中实现，有一个为 ES7 添加 decorators 和 reflect API 的[提议。由于不受支持，Typescript 团队为反射 API](https://github.com/jonathandturner/decorators/blob/master/specs/metadata.md) 构建了一个 [polyfill，现在通过启用实验特性，它可以在 Typescript 中使用。](https://github.com/rbuckton/reflect-metadata)

让我们来看看实现:

我们需要构建的第一件事是我们的属性描述符:

属性描述符

我们在这个描述符中所做的只是设置元数据。

为了在对象上设置元数据，我们使用了`Reflect.defineMetadata` ,我们可以向该函数传递 3 个参数:

*   **键**:我们使用了一个模式，后来我们使用了同样的模式来获取元数据`sensitive:${propertyName}`
*   **值**:我们将其设为 true，以了解该属性是否敏感。
*   这是目标对象，我们传递属性装饰器的第一个参数，它是类的实例。

现在我们可以轻松地在我们的字段前使用`@sensitive`。

让我们一起来看看整个实现:

属性装饰器—完整的实现

两个领域是敏感的。我们不想在`toString`函数中暴露它们，所以我们应该检查它们。我们在第 31 行用同样的模式使用`Reflect.getMetadata`来检查它是否是敏感字段。

```
const isSensetiveField = Reflect.getMetadata(`sensitive:${iterator}` , this)
```

如果字段不敏感，我们不会将其添加到`userJSON`对象，因此，这些字段不会被暴露。让我们看看日志结果:

![](img/fdcc585ed221f7242ca35c6e9107fd3b.png)

日志结果

正如我们所料，带有`@sensitive`装饰的字段没有被暴露。

*****重要的是要明白，我们用另一种方法实现了我们的逻辑，我们使用了属性装饰器来设置元数据，后来我们使用了那个元数据。我们无法在属性装饰器中访问属性值。*****

## **有没有办法摆脱我们用的** `sensitive:${iterator}` 这种模式？

是的，我们使用的两个函数都有另一个重载来为特定的`property`而不是整个对象定义元数据。让我们来看看实现:

属性描述符

函数`defineMetadata`现在有 4 个参数，最后一个是属性名。此外，键不需要在整个对象中是唯一的，它只需要在这个属性中是唯一的。

函数`getMetadata`现在有 3 个参数，最后一个是属性名 key。

## 3-打字稿文档示例的说明

Typescript doc 属性示例

## 为什么从这个例子中很难理解 property decorator？

在跳到这个例子之前，你需要知道一些概念。

**1-什么是** **符号？** Symbol 是一个内置对象，其构造函数返回一个符号原语，也称为符号值或符号，它保证是唯一的([参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol))

**2-为什么装饰函数的参数只有一个，而且是一个字符串？**这是因为这个函数不是装饰器，它只是创建一个装饰器*。* `Reflect.metadata(…)`本身返回一个函数，可以用作我们的装饰器。这些函数被称为**装饰工厂**，因为它们可以用来创建装饰器。在这种情况下，它返回一个具有这些参数的函数:`(target: Object, propertyKey: string | symbol)`。

**3-同一个键如何用于设置元数据？它在不同的领域工作吗？**是的，它正在工作，原因是它使用了`Reflect.metadata`，并且它只为那个*特定属性*自动定义元数据。我们使用的函数也有另一个重载，只为该属性定义元数据。在这种情况下，当我们想要获取元数据时，我们将使用函数`getMetadata`的另一个重载，我们可以传递包含该元数据的*属性键*。

我认为有了这些答案，问题就像我们在第 2 部分中解决的一样。

# 结论

属性描述符只能让我们知道这个装饰器是用在特定的字段上的，我们不能访问字段值，也不能改变它(在某些情况下，我们可以改变字段值，但这可能会导致错误的代码，查看[这篇文章和评论](https://dev.to/danywalls/using-property-decorators-in-typescript-with-a-real-example-44e))。

使用它的一种方法是设置元数据，并在其他地方使用该元数据。

另一个使用示例是根据属性定义一些函数。[查这篇文章找那个](https://saul-mirone.github.io/a-complete-guide-to-typescript-decorator/)。

如果你想了解更多关于装饰者的知识，你可以看看我的其他文章:

[](/start-implementing-your-own-typescript-class-decorators-84a49f560dea) [## 开始实现你自己的类型脚本类装饰器

### 什么是班级装饰者？

levelup.gitconnected.com](/start-implementing-your-own-typescript-class-decorators-84a49f560dea) [](/start-writing-your-own-typescript-method-decorators-c921cdc3d1c1) [## 开始编写自己的类型脚本方法装饰器

### 什么是室内设计师？

levelup.gitconnected.com](/start-writing-your-own-typescript-method-decorators-c921cdc3d1c1) [](https://www.typescriptlang.org/docs/handbook/decorators.html#property-decorators) [## 文档-装饰者

### TypeScript 装饰程序概述

www.typescriptlang.org](https://www.typescriptlang.org/docs/handbook/decorators.html#property-decorators) [](http://blog.wolksoftware.com/decorators-metadata-reflection-in-typescript-from-novice-to-expert-part-4) [## 装饰者&TypeScript 中的元数据反射:从新手到专家(第四部分)* Wolk 软件

### 深入了解 decorators 的 TypeScript 实现，以及它们如何使令人兴奋的新 JavaScript 成为可能…

blog.wolksoftware.com](http://blog.wolksoftware.com/decorators-metadata-reflection-in-typescript-from-novice-to-expert-part-4) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**软件工程师的热门职位**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)