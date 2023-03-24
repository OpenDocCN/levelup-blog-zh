# 类型脚本析构

> 原文：<https://levelup.gitconnected.com/typescript-destructuring-1ec4c747b5e2>

## 通过使用析构赋值语法来简化代码

![](img/5eda5a893bdd3817f8b0dcb1bc5e7704.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

本文试图总结析构在 TypeScript 中的使用，并以最常见的用例为例，供您进行交互和体验。

Mozzila 对析构有一个很好的正式定义:

> 析构赋值语法是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。
> **来源:** Mozzila 网络文档

这种方法避免了在解包数组元素或对象属性时的重复，并使代码更整洁、更易读。

析构赋值允许我们定义什么值(在数组的情况下)或属性(在对象的情况下)要从源变量解包到赋值的左边。

对于对象和数组的析构，有两种析构模式:*绑定模式*和*赋值模式*。

让我们来看看每个图案的含义。

## *装订模式*

当使用绑定模式时，这意味着我们从声明一个变量(赋值的左边)开始，这个变量将被绑定到源变量(赋值的右边)中存在的值。下面一个简单的例子使用了一个对象。

```
const person: Person = { name: 'John', age: 28 };
const { name, age } = person;console.log(name, age); // John 28
```

## *分配模式*

在赋值模式中，我们不是从声明一个变量开始，而是使用一个已经存在的变量或另一个对象的属性(赋值的左侧)，它们的工作方式类似于被析构的源变量的目标(赋值的右侧)。

```
const order = { priceBeforeVAT: 10, priceAfterVAT: 12.3 };
const prices = [];({ priceBeforeVAT:prices[0] , priceAfterVAT: prices[1] } = order)console.log(prices) // [10, 12.3]
```

下面几节展示了如何在不同的场景中使用析构，并包含了每个示例的背景。

# 破坏场景

在这一部分，我收集了一些我经常遇到的例子。我们将使用投标模式。

让我们看一看。

## 对象析构

这可能是析构最常见的用例。

[](https://replit.com/@NunoBrites/objectdestructuring) [## object _ destructing-TypeScript Repl

### 跳转到我的加密货币上为智能合约制作的内容进行中的编程语言。还没有…

replit.com](https://replit.com/@NunoBrites/objectdestructuring) 

上面的代码被分成多个例子来演示对象析构的不同用法。

**属性解包:**允许使用赋值左边的`{}`将一个属性从一个对象中解包并赋给一个变量(见**例 1** )。

**属性重命名:**一个非常有用的选项是在析构时重命名属性；当析构两个相同类型的对象时，这可以避免名称冲突(见**例 2** )。

**属性默认值:**可以设置一个属性的默认值，但是只有当该属性在对象实例中不存在，或者该属性的值为`undefined`，`null`值不回退到默认值时，才会使用默认值(参见**示例 3)。**

**剩余属性:**也可以从一个对象中排除几个属性，并析构原始对象属性的子集(见**例 4** )。

📝 ***例 4*** *关于剩余属性也可以应用于数组但是它将包含数组的剩余值而不是对象的剩余属性。*

也可以组合属性解包、重命名、默认值和剩余属性(参见**示例 5** )。

## 数组析构

类似于我们在上面看到的对象析构，但是这次我们将解包值而不是属性。

[](https://replit.com/@NunoBrites/arraydestructuring) [## array _ destructing-TypeScript Repl

### 跳转到我的加密货币上为智能合约制作的内容进行中的编程语言。还没有…

replit.com](https://replit.com/@NunoBrites/arraydestructuring) 

在上面的例子中，我们有一个订单数组，其中每个元素都是一个对象。解包数组值的语法略有不同，因为它在赋值的左边使用了`[]`而不是`{}`，就像我们看到的对象一样。

## 函数声明析构

最后一个示例显示了一个函数，该函数接收一个对象作为参数，解包对象属性，并在函数体中使用它们。

[](https://replit.com/@NunoBrites/functiondestructuring) [## function _ destructing-TypeScript Repl

### 类型脚本中的函数析构

replit.com](https://replit.com/@NunoBrites/functiondestructuring) 

# 包扎

除了上面的例子，还有其他地方可以使用析构，比如元组、`for...in`和`for...of`循环的循环变量或者在`catch`子句绑定变量中使用析构。

关于析构的更多细节，请看 Mozzila 的 Web 文档，你会发现上面的例子和更多的细节。

希望这篇文章以简单而有帮助的方式总结了 TypeScript 中的析构。

如果你有任何建议或贡献，欢迎在下面评论。感谢您的阅读和快乐编码！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)