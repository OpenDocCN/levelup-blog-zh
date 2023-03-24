# 5 个高级 JavaScript 概念，让你成为更好的开发者

> 原文：<https://levelup.gitconnected.com/5-advanced-javascript-concepts-that-will-make-you-a-better-developer-5d04292107a1>

![](img/d695e83a3922d589eee0be8b86565d9b.png)

阿诺·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## Currying

Currying 意味着计算具有多个参数的函数，并将它们分解成具有单个参数的函数序列。因此，函数不是一次获取所有参数，而是获取第一个参数并返回一个新函数，该新函数获取第二个参数并返回一个新函数，该新函数获取第三个参数……以此类推，直到提供了所有参数并执行了最后一个函数。

Currying 帮助您将功能划分为处理单一职责的更小的可重用功能。这使得你的函数更纯粹，更不容易出错，更容易测试。

简单的咖喱例子

在上面的例子中，我们创建了自己的简单实现来处理一个只有三个参数的函数。作为一个通用的解决方案，我建议使用 [Ramda](https://ramdajs.com/docs/#curry) 或者类似的支持任意数量参数的 currying 函数，并且支持使用占位符改变参数的顺序。

## 作文

合成是一种技术，其中一个函数的结果被传递到下一个函数，下一个函数又被传递到下一个函数，以此类推……直到最后一个函数被执行，并且计算出一些结果。函数组合可以由任意数量的函数组成。

组合还有助于将功能分解成更小的可重用功能，这些功能只有一个职责。

[Ramda](https://ramdajs.com) 也有用[管道](https://ramdajs.com/docs/#pipe)和[组合](https://ramdajs.com/docs/#compose)进行函数组合的 API。

## 关闭

闭包是一个保留对外部函数的变量和参数(作用域)的访问的函数，即使在外部函数已经完成执行之后。闭包对于隐藏 JavaScript 中的实现细节很有用。换句话说，像这样创建私有变量或函数是很有用的:

## 零化合并运算符？？

如果左边的操作数为空或未定义，nullish 合并运算符是一种快速应用默认值的方法。这在您希望接受除 null 和 undefined 之外的所有 falsy 值的情况下尤其方便。或者在您希望将 falsy 值作为默认值的情况下。

## 反映 API

就编程而言，反射意味着一个程序能够通过内省来检查自己，并操纵自己的结构。Reflect API 通过 Reflect API 中的静态方法为自省和操作提供了一组有用的方法。

还有很多要反思的，你可以在这里阅读细节[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

这总结了 5 个 JavaScript 概念，将使你成为更好的开发人员。我希望你喜欢阅读，甚至可能学到一些新东西。

也请阅读 [5 个概念，这将使你成为一个更好的 React 开发者](/5-concepts-that-will-make-you-a-better-react-developer-4d0b56e031e7)。

# 分级编码

感谢您成为我们社区的一员！升级正在改变技术招聘。 [**在最好的公司**找到你的完美工作](https://jobs.levelup.dev/talent/welcome?referral=true) **。**

[](https://jobs.levelup.dev/talent/welcome?referral=true) [## 升级—转变技术招聘

### 升级—转变技术招聘🔥使软件工程师能够找到完美的角色…

作业. levelup.dev](https://jobs.levelup.dev/talent/welcome?referral=true)