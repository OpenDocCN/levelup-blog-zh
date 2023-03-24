# 6 个更简洁代码的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/6-javascript-hacks-for-cleaner-code-8942f491e5e5>

![](img/677d61aba1e7c104310c3f599dc847cf.png)

一头扎进去👇

# 1.解构

您可以从数组中销毁值，或者从对象中销毁属性。一个流行的用例是函数参数的析构。当与 spread (…)运算符结合使用时，它可用于分配其余的值。

```
const student = { age: 20, name: "Bob" }
const { age, name } = studentconsole.log(age) // 20
console.log(name) // "Bob"let a, b, rest;
[a, b] = [10, 20];console.log(a); // expected output: 10
console.log(b); // expected output: 20[a, b, ...rest] = [10, 20, 30, 40, 50];console.log(rest); // expected output: Array [30,40,50]
```

# 2.可选链接

可选的链接操作符(`?.`)访问对象的属性或调用函数。如果对象是`[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`或`[null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/null)`，则返回`[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`而不是抛出错误。

```
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah'
  }
};const dogName = adventurer.dog?.name;
console.log(dogName);
// expected output: undefinedconsole.log(adventurer.someNonExistentMethod?.());
// expected output: undefined
```

# 3.Or:无效合并运算符(？？)

无效合并运算符(`??`)是一种逻辑运算符，当其左侧操作数为`[null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/null)`或`[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`时，返回其右侧操作数，否则返回其左侧操作数。

这可以看作是[逻辑 OR (](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR) `[||](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)` [)运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)的一种特殊情况，如果左边的操作数是*任意* [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 值，而不仅仅是`null`或`undefined`，则返回右边的操作数。换句话说，如果您使用`||`为另一个变量`foo`提供一些默认值，如果您认为一些假值是可用的(例如`''`或`0`，您可能会遇到意外的行为。更多示例见下文。

无效合并运算符具有第五低的[运算符优先级](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)，直接低于`||`并直接高于[条件(三元)运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

```
const foo = null ?? 'default string';
console.log(foo);
// expected output: "default string"const baz = 0 ?? 42;
console.log(baz);
// expected output: 0
```

# 4.高级数组搜索

靠边站，`indexOf()`和`includes()`因为还有另一种允许**高级**数组搜索的方法，它叫做`find()`。

`find()`方法允许你传入一个**测试函数**。将返回数组中通过测试函数的第一个元素。

这有助于更有用的数组搜索。

```
const array1 = [5, 12, 8, 130, 44];const found = array1.find(element => element > 10);console.log(found);
// expected output: 12
```

# 5.移除数组重复项

您可能以前听说过这种方法，但是有一种非常简单的方法可以使用`Set`数据结构从数组中删除重复项。

基本上，`Set`不允许重复值。我们可以利用这一点，将一个数组转换成一个`Set`，然后再转换回一个数组。

```
const numbers = [5, 10, 5, 20];
const withoutDuplicates = [...new Set(array)];// [5, 10, 20]
console.log(withoutDuplicates);
```

# 6.立即调用函数表达式

这张是经典之作。自调用函数是**自己执行**的函数。一个常见的用例是分配一个需要复杂逻辑的变量或常量。当您正在编写一个包含要在执行脚本时执行的主函数的脚本时，这可能会很有用

```
(function () {
  // …
})();

(() => {
  // …
})();

(async () => {
  // …
})();
```

## 进一步阅读

*   [30 秒内 10 次 Javascript 破解](https://medium.com/gitconnected/10-javascript-hacks-in-30-seconds-a9b0213971bd?source=your_stories_page-------------------------------------)
*   [用于平台工程的 71 条 Linux 命令](https://medium.com/dev-genius/71-linux-commands-for-platform-engineering-563cf8f16c5?source=your_stories_page-------------------------------------)
*   [8 C 小技巧&1 分钟变戏法](https://medium.com/towardsdev/8-c-tips-tricks-in-1-minute-f433f7882467?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次 SQL 破解](https://medium.com/@caopengau/10-sql-hacks-in-30-seconds-bc2be9b6f4d9?source=your_stories_page-------------------------------------)
*   [5 分钟 10 个设计图案](https://medium.com/gitconnected/10-design-patterns-in-5-minutes-a8643b1e1b1?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次科特林攻击](https://medium.com/@caopengau/10-kotlin-hacks-in-30-seconds-f5fd5a81224f?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次 Java 破解](https://medium.com/@caopengau/10-java-hacks-in-30-seconds-c3ba2771f381?source=your_stories_page-------------------------------------)

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，获取我和所有其他优秀作家在 medium 上发表的所有优质文章。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)