# chai 中==、===、equal 和 eql 之间的差异

> 原文：<https://levelup.gitconnected.com/difference-between-equal-and-eql-in-chai-162754a935c5>

## 这些看起来非常相似，但有细微的差别。跟着这个教程去理解吧。

![](img/54416417579fb5ee58897cbbd94fbcc6.png)

韦斯·希克斯在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

看看堆栈溢出，我们可能会发现很多程序员发现`equal`和`eql`之间的区别令人困惑。特别是，`eql`看起来就像是`equal`的缩写。此外，在某些情况下，两种断言的工作方式完全相同！

读完这篇教程，你会明白其中的细微差别。

# 简单测试

让我们用`mocha`和`chai`开始一些非常简单的测试:

```
'use strict';const { expect } = require('chai');describe('expect', () => {
    it('it should equal', () => {
        const value1 = 1;
        const value2 = 1; expect(value1).to.equal(value2);
    });
});
```

显然`1`等于`1`，因此测试将通过。数字是 JavaScript 中的原始数据类型，因此如果我们实际使用`==`(等式运算符)、`===`(严格等式运算符)或`eql`，结果将是相同的:

```
describe('expect', () => {
    it('it should equal', () => {
        const value1 = 1;
        const value2 = 1; const result = value1 == value2;
        const strictResult = value1 === value2; expect(value1).to.equal(value2);
        expect(value1).to.eql(value2);
        expect(result).to.equal(true);
        expect(strictResult).to.equal(true);
   });
});
```

在这种特殊情况下，我们使用哪个运算符并不重要。**测试会通过**，没有区别。

# 比较对象

当我们想要比较 JavaScript 中的两个对象时，我们必须更加小心。简单等式和严格等式都会导致`false`结果:

```
const obj1 = { foo: 'bar' };
const obj2 = { foo: 'bar' };const result = obj1 == obj2;         // false
const strictResult = obj1 === obj2;  // false
```

在 JavaScript 中有很多方法可以比较两个对象，但是让我们把重点放在`chai`库和单元测试上。

当我们查阅[柴的文献资料](https://www.chaijs.com/api/bdd/#method_equal)时我们可以发现`equal`的区别在于:

> 断言目标严格(`===`)等于给定的`val`。

而`eql`则是:

> 断言目标深度等于给定的`obj`。

我们来注意四个关键词:*严格*，*深入*， *val* 和 *obj* 。

因此，简单的规则是:当我们想要比较对象时，使用`eql`。否则，使用`equal`比较*值*。

示例:

```
it('should equal', () => {
    expect({ a: 1 }).to.equal({ a: 1 }); // This fails as...

    const obj1 = { foo: 'bar' };
    const obj2 = { foo: 'bar' };
    const result = obj1 === obj2; // ...two objects are not strictly equal. expect(result).to.equal(true); // Thus result is false.
});
```

这个测试失败了，因为我们使用了严格比较两个值的`equal`。它相当于`===`运算符，也给出了`false`。

它能与 object 一起工作的唯一方式是当我们将同一个对象与其自身进行比较时(这有点无意义):

```
it('pointless comparison', () => {
   const object1 = { foo: 'bar' };
   const object2 = object1; expect(object1).to.equal(object2);
});
```

在这种情况下，我们检查一个对象`object1`是否等于我们分配给`object1`的`object2`引用

我们甚至可以将它改写为:

```
const object = { foo: 'bar' };
const result = object === object; // Even strict equal is true!
```

# 深度相等

我们必须承认在`chai`中使用的命名约定不是最好的，因为`eql`检查两个对象是否*完全相等*。因此，像`deeplyEqual`这样更好的名字会更容易混淆，事实上它就在那里！

不使用`eql`，我们可以使用:`deep.equal` —这两个是一样的:

```
it('should eql', () => {
    expect({ b: 2 }).to.eql({ b: 2 }); // Two objects are deeply equal. Test will pass.
});it('should deeply equal', () => {
    expect({ c: 3 }).to.deep.equal({ c: 3 }); // Test will pass
});
```

深度相等意味着两个对象具有相同的属性和相同的值。

# 摘要

关键区别在于*不同*用法。使用`equal`严格比较*值*，如果要比较*对象*则使用`eql`，相当于深度相等。

`eql`的命名具有误导性和迷惑性。相反，你总是可以使用:`deep.equal`。请记住，基本数据类型不需要深度相等！

感谢阅读！

如果你觉得这个教程有帮助和有趣，请鼓掌并跟我来。