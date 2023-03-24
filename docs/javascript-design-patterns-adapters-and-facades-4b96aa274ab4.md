# JavaScript 设计模式—适配器和外观

> 原文：<https://levelup.gitconnected.com/javascript-design-patterns-adapters-and-facades-4b96aa274ab4>

![](img/9684a90c52b84a121c1d74ed1aead53a.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

设计模式是任何好软件的基础。JavaScript 程序也不例外。

在本文中，我们将研究适配器和外观模式。

# 适配器

适配器模式对于为另一个对象创建一致的接口很有用。

我们正在为其创建界面的对象不符合它的使用方式。

因此，我们创建了一个适配器，以便我们可以轻松地使用它们。

它还隐藏了实现，这样我们就可以轻松地使用它，并且耦合度很低。

例如，我们可以如下创建一个适配器对象:

```
const backEnd = {
  setAttribute(key, value) {
    //.,,
  },
  getAttribute() {
    //...
  }
}const personAdapter = {
  setFirstName(name) {
    backEnd.setAttribute('firstName', name);
  },
  setLastName(name) {
    backEnd.setAttribute('lastName', name);
  },
  getFirstName() {
    backEnd.getAttribute('firstName');
  },
  getLastName() {
    backEnd.getAttribute('lastName');
  }
}
```

我们创建了一个名为`personAdapter`的适配器对象，这样我们就可以使用`backEnd`来设置所有的属性。

适配器有一个人们更容易理解的接口，因为方法名是显式的。

其他对象或类可以使用对象来操作属性。

我们可以使用适配器来创建我们想以任何方式向公众公开的接口。

# 用适配器继承对象

有了适配器，我们还可以为多个类或对象创建一个接口。

例如，我们可以写:

```
const backEnd = {
  setAttribute(key, value) {
    //.,,
  },
  getAttribute() {
    //...
  }
}const parentAdapter = {
  //...
  getAdapterName() {
    //...
  }
}const personAdapter = Object.create(parentAdapter);personAdapter.setFirstName = (name) => {
  backEnd.setAttribute('firstName', name);
};personAdapter.setLastName = (name) => {
  backEnd.setAttribute('lastName', name);
};personAdapter.getFirstName = () => {
  backEnd.getAttribute('firstName');
};personAdapter.getLastName = () => {
  backEnd.getAttribute('lastName');
}
```

在上面的代码中，我们用`Object.create`创建了`personAdapter`，这允许我们从另一个父对象继承。

所以我们从`parentAdapter`原型对象得到了`getAdapterName`方法。

然后我们给`personAdapter`加上自己的方法。

现在，我们可以从另一个对象继承方法，再加上适配器中我们自己的适配器对象中的方法。

同样，我们可以对类语法做同样的事情。

例如，我们可以创建一个适配器类来代替一个对象。

我们可以写:

```
const backEnd = {
  setAttribute(key, value) {
    //.,,
  },
  getAttribute() {
    //...
  }
}class ParentAdapter {
  //...
  getAdapterName() {
    //...
  }
}class PersonAdapter extends ParentAdapter {
  setFirstName(name) {
    backEnd.setAttribute('firstName', name);
  } setLastName(name) {
    backEnd.setAttribute('lastName', name);
  } getFirstName() {
    backEnd.getAttribute('firstName');
  } getLastName() {
    backEnd.getAttribute('lastName');
  }}const personAdapter = new PersonAdapter();
console.log(personAdapter);
```

上面的代码有一个`PersonAdapter`和`ParentAdapter`类而不是对象。

我们使用`extends`关键字从`ParentAdapter`继承成员。

这是我们从父实体继承成员的另一种方式。

# 立面图案

我们可以使用 facade 模式来创建一个类或对象，以隐藏其背后复杂内容的实现。

这用于为外部创建一个易于使用的接口，同时保留内部的复杂实现。

例如，我们可以写:

```
const complexProduct = {
  setShortName(name) {
    //...
  }, setLongName(name) {
    //...
  }, setPrice(price) {
    //...
  }, setDiscount(discount) {
    //...
  },
  //...
}const productFacade = {
  setName(type, name) {
    if (type === 'shortName') {
      complexProduct.setShortName(name);
    } else if (type === 'longName') {
      complexProduct.setLongName(name);
    }
  }
  //...  
}
```

我们创建了一个`productFacade`，它有一些调用`complexProduct`对象中各种方法的方法。

这样，我们可以使用 facade 来简化我们所做的事情，并且减少与`complexProduct`对象的耦合。

我们让`productFacade`对象与`complexProduct`对象通信。

它还用于减少来自复杂对象的耦合。

此外，一个门面也可以为一些不会像它背后的东西那样经常变化的东西创建一个接口。

一个不经常改变的界面是好的，因为更多的改变意味着更多的错误风险。

同样，我们可以创建一个 facade 类来做同样的事情，如下所示:

```
const complexProduct = {
  setShortName(name) {
    //...
  }, setLongName(name) {
    //...
  }, setPrice(price) {
    //...
  }, setDiscount(discount) {
    //...
  },
  //...
}class ProductFacade {
  setName(type, name) {
    if (type === 'shortName') {
      complexProduct.setShortName(name);
    } else if (type === 'longName') {
      complexProduct.setLongName(name);
    }
  }
  //...  
}
```

然后我们可以创建一个`ProductFacade`的实例，而不是直接使用 object literal。

一个外观可以用来操作一个或多个对象。这与适配器模式不同，在适配器模式中，我们为一个对象创建一个适配器。

![](img/04ff43d53b56b6b378ec4d506ffe127a.png)

照片由[丹尼尔·冯·阿彭](https://unsplash.com/@daniel_von_appen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

我们可以使用适配器模式来隐藏一个对象的实现，但创建一个更容易与外部使用的接口。

facade 模式让我们可以将复杂的实现隐藏在一个接口之后，而不是使用一个或多个复杂的对象作为 facade。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)