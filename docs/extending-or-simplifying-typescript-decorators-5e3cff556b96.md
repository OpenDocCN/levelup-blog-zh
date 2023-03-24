# 扩展或简化 TypeScript 装饰器

> 原文：<https://levelup.gitconnected.com/extending-or-simplifying-typescript-decorators-5e3cff556b96>

你有没有使用过图书馆里的打字稿装饰器

> 如果这东西能再多做一件事，那就完美了！

或者也许

> 这 4 个参数中的 3 个总是相同的，一遍又一遍地写同样的东西真是浪费时间！

对自己？如果不是，你在撒谎或者只是非常谦虚😉。如果是，请继续阅读。

![](img/18ddb315cc359701bc4a4f6182feebc8.png)

由 [Roman Synkevych](https://unsplash.com/@synkevych?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 装饰者是函数

关于 TypeScript decorators，要知道的主要事情是:它们看起来很花哨，但在它们的核心中，它们只不过是一个函数，当被修饰的方法、属性或类被加载时，它将被执行。当你定义一个装饰器时，你可以直接定义一个函数，或者实现一个工厂，让**返回**一个参数化的函数。

最后一个函数，无论是由工厂定义的还是返回的，都将其`target`作为一个最小参数。根据装饰者的类型(即你把它放在什么前面)，这个`target`可能是:

*   一个类的方法
*   一个类的属性
*   一个类本身

在官方的 [TypeScript 文档](http://www.typescriptlang.org/docs/handbook/decorators.html)中阅读更多关于不同类型装饰器的信息。

## 装饰示例

让我们看看类装饰器的以下用法:

```
import {Table} from "imaginary-decorator-library";@Table({
    name: "example",
    createAutoAttributes: true,
})
export class MyEntity {}
```

> 这是一个基于**工厂**的装饰器，因为您可以放入参数来指定细节。

想象一下，您在无数的实体文件中使用这个装饰器，并且您总是必须提供一个对象作为参数，给出表的名称和`createAutoAttribute`标志，它总是`true`。但是另一方面，如果这里给出的名字可以放在一个名字的全局数组中，那就太好了，这样我们就可以在允许装饰器之前检查数组是否有重复。

因此，让我们实现我们自己的装饰工厂:

```
import {Table} from "imaginary-decorator-library";
import {TABLE_NAME} from "somewhere-else";export function CoolerTable(name: string): Function {  
  // Factory returns the actual decoration function.  
  return function(target: Function): void {

    // Check our duplication stuff.   
    if(TABLE_NAMES.includes(name)) {
      throw new Exception(`Duplicate table name "${name}"!`);
    }
    TABLE_NAMES.push(name); // Execute the uncool decorator.
    Table({
      name: name,
      createAutoAttributes: true,
    })(target);
  }
}
```

工厂得到一个`name`作为参数，并返回一个新的类装饰器，这个函数现在是空的。现在让我们插入我们自己的实现细节:

```
import {Table} from "imaginary-decorator-library";
import {TABLE_NAME} from "somewhere-else";export function CoolerTable(name: string): Function {  
  // Factory returns the actual decoration function.  
  return function(target: Function): void {

    // Check our duplication stuff.   
    if(TABLE_NAMES.includes(name)) {
      throw new Exception(`Duplicate table name "${name}"!`);
    }
    TABLE_NAMES.push(name);
  }
}
```

现在，你已经可以在你的代码中使用装饰器了。它将检查给定的表名是否已经在另一个装饰器中使用，否则抛出一个异常。但是您仍然需要为实际的功能应用最初的`@Table`装饰器。让我们来完成这个:

```
import {Table} from "imaginary-decorator-library";
import {TABLE_NAME} from "somewhere-else";export function CoolerTable(name: string): Function {  
  // Factory returns the actual decoration function.  
  return function(target: Function): void {

    // Check our duplication stuff.   
    if(TABLE_NAMES.includes(name)) {
      throw new Exception(`Duplicate table name "${name}"!`);
    }
    TABLE_NAMES.push(name); // Execute the uncool decorator.
    Table({
      name: name,
      createAutoAttributes: true,
    })(target);
  }
}
```

看到我做了什么吗？因为装饰器是函数——你可以在目标上自己调用它们。在我们的自定义装饰器的最后几行中，我们首先调用工厂来获得一个装饰器函数，然后调用函数本身，将原始目标作为参数。这样，您可以在应用装饰器之前和之后做任何您想做的事情，并定制它的输入。

## 额外小费

当你总是不得不使用两个或更多的装饰者来得到你想要的行为时，你不讨厌吗？只需创建一个新的装饰器，并在其实现中调用它们！