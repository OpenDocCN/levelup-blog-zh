# Lightning Web 组件中的装饰者和混合者

> 原文：<https://levelup.gitconnected.com/decorators-and-mixins-in-lightning-web-components-18589f39b27e>

![](img/69419263d30c14331ee9521812980751.png)

照片由[史蒂夫·哈维](https://unsplash.com/@trommelkopf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

可以肯定地说，如今每个现代 web 应用程序在某种程度上都依赖于三个基本的 web 标准:HTML、CSS 和 JavaScript。虽然 HTML 自 HTML5 标准以来已经基本稳定，但 CSS 和 JavaScript 都在继续发展，以满足开发人员和用户的需求。

这三种技术不断发展的本质导致了 [web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)的引入，这是一种用于构建复杂 web 应用的跨浏览器解决方案。在这个开源标准的基础上，Salesforce 开发了[Lightning Web Components](https://lwc.dev/)(LWC)作为一个快速的企业级包装器，包装普通的 Web 组件。结果是一个瘦的，高性能的，功能齐全的框架，完全建立在开放的网络上。

LWC 不仅建立在 ECMAScript 标准之上，它还提供了一些漂亮的语法糖，可以转换成标准的 JavaScript。正因为如此，LWC 框架能够整合[提议的语言特性](https://github.com/tc39/proposals)，从而通过在不断发展的 JavaScript 生态系统中让您的代码经得起未来考验来简化应用程序开发。在本帖中，我们将仔细研究两个相对较新的特性——mixins 和 decorator——并看看它们如何应用在你的 LWC 应用中。

# 什么是 Mixin？

在许多面向对象的编程语言中，类可以通过一个称为继承的特性“接收”额外的方法。例如，如果你有一个带有方法`go`和`stop`的`Vehicle`类，子类`Bicycle`和`Car`可以直接实现它们:

```
class Vehicle {
  void go();
  void stop();
}class Bicycle < Vehicle {
  void go() {
    usePedal();
  } void stop() {
    stopPedal();
  }
}class Car < Vehicle {
  void go() {
    useEngine();
  } void stop() {
    stopEngine();
  }
}
```

继承通过改变对象的层次来影响对象的组成。每一个`Bicycle`和`Car`现在也是一个`Vehicle`。但是，如果您只想在对象中添加公共方法，而不处理任何父类，该怎么办呢？这就是一个 [mixin](https://en.wikipedia.org/wiki/Mixin) 做的事情。

在 JavaScript 上下文中，mixin 可以向 JavaScript 类添加行为，这很有用，因为类只能从一个其他类扩展，而多个 mixin 可以添加到一个类中。Mixins 利用了`[Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)`方法，该方法将一个对象的所有属性复制到另一个对象上:

```
// mixin
let greetingsMixin = {
  sayHi() {
    alert(`Hello ${this.name}`);
  },
  sayBye() {
    alert(`Bye ${this.name}`);
  }
};class User {
  constructor(name) {
    this.name = name;
  }
}// copy the methods
Object.assign(User.prototype, greetingsMixin);
```

`User`现在可以原生调用`sayHi`和`sayBye`。根据 JavaScript 规则，`User`也可以只继承一个类，同时包含任意数量的 mixins 的属性和函数:

```
class User extends Person {
  // ...
}Object.assign(User.prototype, greetingsMixin);
Object.assign(User.prototype, someOtherMixin);
```

然而，写出`Object.assign`有点类似于乱丢代码。更糟糕的是，弄清楚这个方法在做什么并不是很直观。通过一些本地 JavaScript 语法，您实际上可以用 mixin 创建一个“子类工厂”,并在顶部声明您正在使用的 mixin:

```
class User extends greetingsMixin(Person) {
  // ...
}
```

(关于这项技术的更多信息，请查看本文。)

现在，`User`包含了`greetingsMixin`并继承了`Person`类，所有这些都在一行中。

这种技巧不仅仅是语法上的糖:它实际上是 LWC 经常喜欢的一种。例如，`[Navigation Mixin](https://developer.salesforce.com/docs/component-library/bundle/lightning-navigation/documentation)`提供了对导航 UI 元素有用的方法，但是最终，包含它的每个类也应该从普通的`LightningElement`派生:

```
import { LightningElement } from 'lwc';
import { NavigationMixin } from 'lightning/navigation';export default class TestComponent extends NavigationMixin(LightningElement) {
  // ...
}
```

`NavigationMixin`为处理页面导航的组件提供关键功能，而`LightningElement`为每个组件提供所有基本功能。因此，`TestComponent`将需要包含`NavigationMixin`和`LightningElement`的子类，并且可以以易于查看的单行格式来实现。

# 什么是室内设计师？

装饰者目前是添加到 JavaScript 的提议，但是它们非常有用，许多框架已经支持它们。本质上，装饰器是一个可以修改类或者它的任何属性和方法的函数。这是一个相当高层次的定义，所以让我们看看它在实践中意味着什么。

假设我们有这样一个类:

```
class User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}
```

现在，任何使用这个类的代码都可以创建一个用户:

```
let user = new User("Jane", "Eyre");
user.getFullName(); // returns "Jane Eyre"
```

但是由于 JavaScript 的设计方式，开发人员可能会无意中更改`getFullName`方法，如果他们愿意的话:

```
let user = new User("Jane", "Eyre");
user.prototype.getFullName = function() {
  return "not the name!;"
}
user.getFullName(); // returns "not the name!"
```

这显然是一个老生常谈的例子，但危险依然存在。您可以编写代码将类属性设置为只读，如下所示:

```
Object.defineProperty(User.prototype, 'gettFullName', {
  writable: false
});
```

这是可行的，但是为多个属性编写显然很麻烦。

进入装饰者。您可以定义一个装饰函数，将您想要的任何行为应用于目标属性。例如，要将目标设置为`writable: false`，您可以这样做:

```
function readonly(target) {
  target.descriptor.writable = false;
  return target;
}
```

我们刚刚定义了一个名为`readonly`的装饰器，当传递一个目标时，它会将其`descriptor.writable`属性设置为`false`。这个可以这样应用到我们的`User`类:

```
class User {
  // ...
  @readonly
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}
```

瞧啊。在一行代码中实现相同的功能。

LWC 提供[几个装饰器](https://developer.salesforce.com/docs/component-library/documentation/en/lwc/lwc.reference_decorators)给开发者使用。它们是:

*   默认情况下，每个属性都是隐藏的和私有的。`@api`公开曝光。
*   `@track`:这将一个属性标记为 reactive，这意味着当它的值改变时，web 组件将重新呈现并显示新的值。
*   `@wire`:这是一个装饰器，表示我们想要读取 Salesforce 数据。

这三个装饰器是 LWC 独有的，旨在帮助减少相同代码的重写，同时轻松提供通用功能。

# 结论

由于 LWC 是建立在 web 标准之上的，它可以利用本地 API 和语言来让开发人员立即提高工作效率，因为他们使用的是现有的技能，而不是学习专有技术。

如果你想仔细看看 [Lightning Web Components](https://lwc.dev/) ， [Salesforce 有一个内置在 TypeScript](https://github.com/diervo/lwc-typescript-boilerplate) 中的样板应用。还有一个[先导课程](https://trailhead.salesforce.com/quests/web-components?&utm_source=event&utm_medium=paid&utm_campaign=codemotion&utm_content=webinar-promo_web_components)可以帮助你在不到一个小时的时间里了解 web 组件。或者，随意查看[LWC 开发文档](https://lwc.dev/)以获取更多具体参考信息。