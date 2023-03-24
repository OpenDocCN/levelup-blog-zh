# 如何用 JavaScript 创建你的自定义 iterables

> 原文：<https://levelup.gitconnected.com/how-to-create-your-custom-iterables-in-javascript-6ddaf1b5201b>

ES6 最重要的增加之一是 iterables。

iterable 是一种特殊的对象，它实现了 iterable 协议，这意味着它们有一个特殊的属性，带有键[Symbol.iterator]和一个返回迭代器的函数值。

迭代器是实现迭代器协议的对象。它应该有一个`next()`方法，该方法返回一个具有两个属性的对象:

*   `value`可以是任何 JavaScript 值，
*   `done`这是一个布尔值，表示迭代器是否完成了它的序列。

因此，使用 TypeScript 接口，可迭代对象和迭代器具有以下签名:

```
interface Iterable {
    [Symbol.iterator]() : Iterator;
}
interface Iterator {
    next() : IteratorResult;
    return?(value? : any) : IteratorResult;
}
interface IteratorResult {
    value: any;
    done: boolean;
}
```

这些协议允许我们迭代与我们的对象相关的一组值。

理解可迭代的一个很好的比喻是，如果我们把可迭代的物理对象当成一本书。在这种情况下，迭代器将是书签，它保存了我们在迭代过程中所处位置的引用。下一个方法实际上是过渡到下一个迭代，在这种情况下是翻页。

![](img/cccb07188592fc968d3b32215f779a30.png)

在 ES6 中，许多内置类型都有迭代器属性。例如:

*   用线串
*   数组
*   地图
*   设置
*   节点列表
*   还有那个`argument`物体

都是可重复的。

我们可以用 for…of 循环遍历它们，使用 spread 操作符，或者使用析构赋值。

> 我们可以用生成器函数创建迭代器，但我们不会在这里使用它们，我们将只关注迭代器协议。

## 简单的迭代器函数示例

默认情况下，字符串是可迭代的，但是我们可以简单地通过在[Symbol.iterator]属性上添加我们自己的迭代器函数来覆盖默认的字符串迭代器。假设我们想要向后遍历字符串的字符:

注意，我们必须使用字符串构造函数来避免自动装箱。此外，我们必须在下一个方法中使用箭头函数来保持`this`引用我们的字符串对象。

我们使用封闭的`i`变量作为迭代器状态，并从末尾到第一个字符返回字符串的字符。一旦我们到达那里，我们返回我们的 IteratorResult 对象，并将 done 标志设置为 true，表示迭代过程结束。

出于多种原因，JavaScript 中的对象在默认情况下是不可迭代的，但这并不意味着我们不能为它们添加自定义迭代器。例如，如果我们想以升序遍历对象的值:

我们也可以像这样简单地定义一个可迭代范围:

如果我们想让一个链表是可迭代的呢？让我们来看一个简单的链表实现:

我们也可以将迭代器添加到 LinkedList 类中:

参考资料:
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Iteration _ protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
【https://exploringjs.com/es6/ch_iteration.html】T4