# 创建 Mobx 可观察对象和数组

> 原文：<https://levelup.gitconnected.com/create-mobx-observable-objects-and-arrays-3743bed6366d>

![](img/6c90c141d8d9ec0c69ba9e2d559c8c66.png)

[钱杰夫](https://unsplash.com/@junfungchin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Mobx 是 JavaScript 应用程序的状态管理解决方案。它让我们观察来自商店的值，然后设置这些值，这些值将立即反映在商店中。

在本文中，我们将看看如何用 Mobx 创建可观察对象。

# 可观察的物体

Mobx 为我们提供了`observable.object`方法，可以这样调用:

```
observable.object(props, decorators?, options?)
```

如果一个普通的 JavaScript 对象被传递给`observable`，那么里面的所有属性都将被克隆并变得可见。

普通对象不是由构造函数创建的，而是以`Object`为原型或者没有原型的对象。

默认情况下，`observable`是递归应用的，所以如果遇到的值是一个对象或数组，该值也将通过`observable`传递。

我们可以如下使用`observable.object`方法:

```
import { observable, autorun, observe, action } from "mobx";const person = observable(
  {
    firstName: "Jane",
    lastName: "Smith",
    get fullName() {
      return `${this.firstName}, ${this.lastName}`;
    },
    setAge(age) {
      this.age = age;
    }
  },
  {
    setAge: action
  }
);observe(person, ({ newValue, oldValue }) => console.log(newValue, oldValue));
person.setAge(20);
```

我们用`observable`函数创建了可观察的`person`对象，它将一个普通对象作为第一个参数。然后我们用第二个对象将对象中的`setAge`方法设置为`action`。

然后我们可以用`observe`方法观察值的变化，该方法将可观察的`person`对象作为第一个参数，将一个变化监听器作为第二个参数。

然后当我们跑的时候:

```
person.setAge(20);
```

我们传递给`observe`的监听器将会运行。

在 MobX 4 或更低版本中，当我们将对象传递给`observable`时，只有在使对象可观察时存在的属性才会被观察到。除非使用了`set`或`extendObservable`操作系统，否则以后添加到对象的属性是不可见的。

只有普通的物体才会被观察到。对于非简单对象，初始化可观察属性被认为是构造函数的责任。

我们可以使用`@observale`注释或者`extendObservable`函数。

属性 getters 将自动转换为派生属性。

`oberservable`在实例化时自动递归应用于整个对象，当新值被分配给可观察属性时也是如此。它不会递归地将`observable`应用于非简单对象。

我们可以将`{ deep: false }`传递给`observable`的第三个参数来禁用属性值的自动转换。

同样，我们可以使用`autorun`函数来观察如下变化的值:

```
import { observable, autorun, action } from "mobx";const person = observable(
  {
    firstName: "Jane",
    lastName: "Smith",
    get fullName() {
      return `${this.firstName}, ${this.lastName}`;
    },
    setAge(age) {
      this.age = age;
    }
  },
  {
    setAge: action
  },
  {
    name: "person"
  }
);autorun(() => console.log(person.age));
autorun(() => console.log(person.firstName));person.setAge(20);
person.firstName = "Joe";
```

在上面的代码中，当我们通过`setAge`更新`person.age`时，或者当我们像使用`firstName`一样直接更新属性时，我们将看到传递给`autorun`的回调函数运行。

![](img/cbfc42a0eb383497bfd62f1efd36a667.png)

照片由[罗布·温盖特](https://unsplash.com/@robwingate?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 可观察阵列

我们可以使用`observable.array`方法创建一个可观察的数组。它将一个数组作为第一个参数。

例如，我们可以如下使用它:

```
import { observable, autorun } from "mobx";const fruits = observable(["apple", "orange"]);autorun(() => console.log(fruits.join(", ")));fruits.push("grape");
fruits[0] = "pear";
```

在上面的代码中，我们将数组传递给了`observable`。然后我们运行`autorun`,每次`fruits`改变时都会运行一次回调。

然后，当我们在`fruits`上运行数组操作时，我们在传递给`autorun`的回调中获得最新的值。

以下方法也可用于可观测阵列:

*   `intercept(interceptor)` —截取数组变化操作
*   `observe(listener, fireImmediately? = false)` —监听数组变化
*   `clear()` —从数组中删除所有条目
*   `replace(newItems)` —用新条目替换所有条目。
*   `find(predicate: (item, index, array) => boolean, thisArg?)` —与通常的`Array.prototype.find`方法相同
*   `findIndex(predicate: (item, index, array) => boolean, thisArg?)` —与通常的`Array.prototype.findIndex`方法相同
*   `remove(value)` —从数组中按值删除单个项目。如果找到并移除了该项目，则返回`true`

我们可以将`{ deep: false }`作为`observable.array`的第二个参数传入，以防止将项目设置为递归可见。

此外，我们可以传入`{ name: "my array" }`作为第二个参数，为调试目的添加一个名称。

# 结论

我们可以用`observable.object`和`observable.array`方法创建可观察的对象来创建可观察的数组。

我们可以用`autorun`观察值的变化，当可观察的物体变化时，它会给我们最新的值。

此外，我们可以设置各种选项来控制可观察值的转换方式。