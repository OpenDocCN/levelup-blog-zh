# 用 Mobx 创建可观察对象

> 原文：<https://levelup.gitconnected.com/creating-observables-with-mobx-e333f7798fa4>

![](img/b2ed4166100c0e33663d3ffec9f9901d.png)

照片由 [Freddy Marschall](https://unsplash.com/@freddymarschall?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Mobx 是 JavaScript 应用程序的状态管理解决方案。它让我们观察来自商店的值，然后设置这些值，这些值将立即反映在商店中。

在本文中，我们将看看如何创建一个可观察对象，让我们订阅它，然后改变它的值。

# 可观察函数

Mobx `observable`函数接受一个对象。它也可以以 decorator 的形式提供，我们可以直接设置它的值。

它可以按如下方式使用:

*   `observable(value)`
*   `@observable classProperty = value`

它根据传入的值返回各种类型的值。`value`可以是 JavaScript 原始值、引用、普通对象、类实例、数组和映射。

有一些转换规则适用于传递给`observable`函数的值。它们是:

*   如果`value`是 ES6 `Map`，那么将返回一个新的可观测地图。如果我们想要对特定条目中的变化以及条目的添加或删除做出反应，可观察地图是非常有用的。
*   如果`value`是 ES6 `Set`，那么将返回一个新的可观测集合。
*   如果`value`是一个数组，那么将返回一个新的可观测数组。
*   如果`value`是一个没有原型的对象，那么它的所有当前属性都将是可观察的
*   如果`value`是一个带有原型、JavaScript 原语或函数的对象，那么`observable`将抛出。我们应该使用盒装可观察的可观测量。

例如，我们可以如下使用`observable`:

```
import { observable } from "mobx";
const map = observable.map({ foo: "value" });
map.observe(({ oldValue, newValue }) => console.log(oldValue, newValue));map.set("foo", "new value");
```

在上面的代码中，我们使用了`observable.map`函数来创建一个可观察的地图。

然后我们给它附加一个监听器，用`observe`函数来观察这些值。侦听器给我们一个带有`oldValue`和`newValue`属性的对象，这样我们就可以检索它们。

当我们打电话时:

```
map.set("foo", "new value");
```

然后我们将看到侦听器记录的值。

我们也可以对数组做类似的事情:

```
import { observable } from "mobx";
const array = observable([1, 2, 3]);
array.observe(({ oldValue, newValue }) => console.log(oldValue, newValue));array[1] = 5;
```

我们向函数`observable`传递了一个数组，该函数返回一个可观察的数组。然后我们可以像以前一样在它上面调用`observe`来观察它的值。

然后，当我们对`array`进行更改时，我们传递给`observe`的监听器将记录新值和旧值。

为了使原语可观察，我们可以使用如下的`box`方法:

```
import { observable } from "mobx";
const num = observable.box(3);
num.observe(({ oldValue, newValue }) => console.log(oldValue, newValue));num.set(10);
```

在上面的代码中，我们用`box`方法创建了一个可观察的原语。观察值变化的代码与前面相同。

然后我们可以使用`set`为`num`可观察原语设置一个新值。

![](img/027773f2c4a703109463139fa2e4f3e6.png)

照片由 [Yolanda Sun](https://unsplash.com/@iyolanda?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# @可观察的装饰者

如果我们的代码库支持 ES7 或 TypeScript，那么我们可以使用`@observable` decorator 来使类属性可见。

我们可以如下使用它们:

```
import { observable, computed } from "mobx";class Person {
  @observable firstName = "Jane";
  @observable lastName = "Smith"; @computed get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}const person = new Person();
console.log(person.firstName);
console.log(person.lastName);
console.log(person.fullName);
```

在上面的代码中，我们创建了`Person`类，它具有可观察的属性，如使用`observable`装饰器所指示的。我们还有一个属性`computed`是 getter。

然后我们创建一个新的`Person`实例，我们可以记录这些值。

我们也可以像往常一样通过赋值来设置这些值。

要使用 decorators，我们可以使用一个类似 packages 的构建系统，然后将`@babel/core`、`@babel/plugin-proposal-class-properties`、`@babel/plugin-proposal-decorators`和`@babel/preset-env`包添加到`plugins`部分的`.babelrc`中，如下所示:

```
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ],
    [
      "@babel/plugin-proposal-class-properties",
      {
        "loose": true
      }
    ]
  ]
}
```

# 结论

我们可以用`observable`对象创建可观察的值。只要使用正确的方法来创造可观察的价值，它可以用于各种各样的对象。

然后我们可以使用`observe`方法来观察返回的可观察值，并对其进行操作。

我们也可以使用带有类的`observable`装饰器来创建一个具有可观察属性的类。

要使用 decorators，我们要么使用 Babel，要么使用 TypeScript。