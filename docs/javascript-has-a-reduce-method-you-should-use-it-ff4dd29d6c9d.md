# JavaScript 有一个 Reduce 方法。你应该使用它。

> 原文：<https://levelup.gitconnected.com/javascript-has-a-reduce-method-you-should-use-it-ff4dd29d6c9d>

## 学习基础知识，然后跳过它们

![](img/2d85e992ccd0acca3f57d6161e05b107.png)

每个人都喜欢 JavaScript 的`.map`、`.forEach`、`.filter`迭代方法，但是经常被忽略的`.reduce`呢？我认为人们从来没有真正适应过它，所以他们忽略了它。先说说它到底是怎么运作的，然后用它做点有趣的事情。

# 减少一组值

之所以称之为 reduce，是因为它将把一个包含多个值的数组简化为一个值。

```
**total**([1,2,3,4]); // 10
```

我们*能不能*只和`.forEach`循环一次就完事了:

```
*const* **total** = (**arr**) => {
  *let* **result** = 0;
  **arr**.forEach(**num** => { **result** += **num** });
  *return* **result**;
};
```

这似乎有点多，对不对？我们正在创建一个值，操作它，然后立即返回它。所有这些动作都阻止我们使用 arrow 函数的隐式返回值。让我们*减少*的步骤:

```
*const* **total** = (**arr**) => **arr**.reduce((**result**, **num**) => **result** + **num**, 0);
```

很好。

# 崩溃了。减少

`reduce`方法本身有两个参数，即`callback`和`startingValue`。那个`callback`有两个主要参数，即`accumulator`和`nextValue`:

```
// reduce's arguments
.reduce(**reducerCallback**, **startingValue**)// callback's arguments
reducerCallback(**accumulator**, **nextValue**)
```

下面是前一个单独回调的例子:

```
*const* **reducerCallback** = (**accumulator**, **nextValue**) => {
   *return* **accumulator** + **nextValue;**
};
*const* **total** = (**arr**) => **arr**.reduce(**reducerCallback**, 0);
```

# 了解回拨

基本上，它将`accumulator`设置为给定的`startingValue`，然后遍历数组，索引处的每个**值**成为`nextValue`参数。无论回调函数返回什么，都将成为下一次迭代开始的`accumulator`。在数组完全运行之后，`reduce`方法返回最终的`accumulator`值。

*有可能*不给`startingValue`给`reduce`；它将简单地使用数组中的第一个值作为`accumulator`(第二个值作为第一个`nextValue`)。然而，如果给定一个空数组，给一个起始值可以防止`reduce`抛出错误。文档很好地说明了所有这些值(并告诉你另外两个可选参数)。

# 有趣的是

没有说我们必须用数字。当我们遍历一个数组时，`reduce`所做的就是给我们一个连续的值来操作。 ***这就是*** 这个方法的威力来自哪里。那么，如果起始值是`{}`呢？

# 制作我们自己的计数函数

有时您想要计算列表中出现的所有次数:

```
const **arr** = ['x', 'y', 'z', 'z'];
// I want: { x: 1, y: 1, z: 2 };
```

同`forEach`:

```
*const* **counts** = (**arr**) => {
  *const* **result** = {};
  **arr**.forEach((**item**) => {
    **result**[**item**] ? **result**[item]++ : **result**[item] = 1;
  });
  *return* **result**;
};
```

我们正在做的是检查我们的字母的键是否有值。如果是这样，那么我们对那个值加 1。如果不是，那么我们保存值为 1 的键。但是，这与前面的模式相同:*创建*对象，*操作*对象，*返回*对象。请留意这种模式，因为`reduce`运行得非常完美:

```
*const* **counts** = (**arr**) => **arr**.reduce((**result**, **item**) => {
  **result**[**item**] ? **result**[**item**] += 1 : **result**[**item**] = 1
  *return* **result**;
}, {} )
```

# 分解我们的计数函数

事情并没有像他们简化的那样改变太多。我们使用相同的[三元组](https://itnext.io/whats-a-javascript-ternary-5edf4415a09d)来填充我们的`result`对象，但是这次我们不需要浪费空间来声明变量，因为我们有累加器参数。然后，我们只需返回我们的累加器`result`对象。我们*总是*必须返回对象，因为**回调的返回值是在下一次调用中赋予累加器的值。**

有趣的是，看看这个 [code golf](https://en.wikipedia.org/wiki/Code_golf) 片段，它在一行中完成了所有工作:

```
*const* **count** = (**arr**) => **arr**.reduce((**acc**, **val**) => {
  *return* { ...**acc**, [**val**]: **acc**[**val**] ? **acc**[**val**] + 1 : 1 };
}, {})
```

# Reduce 是一个有用的工具

使用循环或者其他迭代器没有错，我的观点是，`reduce`不值得被忽略。如果你需要找一个总数，它是量身定做的。如果你发现 ***创造、操纵、返回*** 模式，这可能是展示`reduce`如何足智多谋的绝佳机会。

大家编码快乐，

麦克风