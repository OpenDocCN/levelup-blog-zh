# 用这四个构建块函数提升您的 JavaScript 数组

> 原文：<https://levelup.gitconnected.com/level-up-your-javascript-arrays-with-these-four-building-block-functions-d01ca0971bcf>

## 使用 Map、Reduce、Filter 和 Find，您可以编写更好的 JavaScript 代码。

![](img/f057845c6ada6cd6f17bb02b83986991.png)

由[詹姆斯·庞德](https://unsplash.com/@jamesponddotco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 中的数组有些特殊，因为它们利用了 JS 的原型特性，并提供了一种方便的方法来直接从对数组实例的引用运行函数。

我相信在你的代码中尽可能多地使用这四个功能不仅会让你更有效率，而且会让你在团队中更受欢迎，通常会对你所做的事情更感兴趣。

所有这四个原型函数都接受一个“回调”函数，这是一个纯粹的函数，接受参数并必须将预期的东西返回给调用者，无论是像`true`或`false`这样的`boolean`，还是像`String`、`Object`、`Array`或`Number`这样的类型。

让我们来看看这些有用的数组函数，并学习使我们的代码更加简洁！

# map-array . prototype . map()

![](img/98ca2a451829c5f883ffd1cb859a862b.png)

照片由[Louis Hansel @ shotsoflouis](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`map`是大多数编程语言的构建模块，并且绝对是一个很棒的`Array`函数。

例如，这一行代码将遍历`ANIMALS`并创建一个`Strings`数组，其中包含它们的名字和动物类型:

如果您正在使用 React 或 Vue 这样的前端框架，您可能已经熟悉了`map`,在这些框架中，您经常需要遍历一组数据来生成 HTML:

```
<ul>
    {ANIMALS.map((animal) =>
        <li>
            {animal.name} ({animal.type})
        </li>
    )}
</ul>
​
// produces:
<ul>
    <li>Spot (dog)</li>
    <li>Fluffy (cat)</li>
    <li>Peppy (bird)</li>
    <li>Lizzy (lizard)</li>
</ul>
```

`map`函数对于*将 X 的数组转换成 Y 的数组*非常有用。

# reduce-array . prototype . reduce()

`reduce`功能的用法与`map`相似，但更进一步。

代替*将 X 的数组转换成 Y 的数组* , `reduce`被设计成*将 X 的数组转换成 Y 的数组*，意思是**你不仅仅局限于做一个新的数组**。

你可以把一个数组变成`Objects`、`Numbers`或者`Strings`。这里我们合计一下所有`ANIMALS`的总重量:

`reduce`是一个非常强大的数组函数，它足够灵活，可以产生和`map`一样的效果。看看这个`reduce`版本的`map`函数:

# Filter — Array.prototype.filter()

![](img/03acfccd811b9bbae76c09e2daaec0b3.png)

照片由 [Caleb Dow](https://unsplash.com/@calebscamera?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`filter`工作起来就像它听起来一样，并且只从一个数组中检索那些从你的回调函数中产生真值的项目。在这里，我们寻找拥有柔软皮毛或羽毛的`ANIMALS`:

这是另一个例子，用数学比较。只要函数返回一个 true/falsy 值，它就会按预期工作。这里我们只选择重量小于 10 磅的`ANIMALS`:

# Find — Array.prototype.find()

最后，我们有`find`，它帮助你使用`callback`搜索一个数组。这非常有用，因为它提供了一个接口来搜索一个数组，该数组还可以与一个名为`findIndex`的姐妹函数一起工作。

只要您的函数返回真值，返回的第一个**真值将退出`find`搜索并从数组返回该行。**

在这个例子中，我们`find`一只名叫斑点的狗:

# 组合数组函数

![](img/e9fabceecdac622c4a91384f5205efaa.png)

照片由[阿里·叶海亚](https://unsplash.com/@ayahya09?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在您已经掌握了这四个构建模块功能，我们可以将它们结合在一起:

上面，我们首先`map`超过我们的`ANIMALS`并创建一个新的对象，它们的**重量**和**类型**，也借此机会喂养每一个。我们给它们喂食，它们可能会在进食后体重增加 1%——这只是猜测。

然后我们只过滤掉有皮毛或羽毛的动物，注意我们在喂食后做了这个*，因为我们不想让蜥蜴 Lizzy 挨饿🦎：*

`.filter((animal) => animal.type !== 'lizard') // only soft`

然后我们过滤掉超过一定重量限制的动物，这就去除了狗的条目🐕：

`.filter((animal) => animal.weight < 10) // only light (after eating)`

最后，我们将剩余的动物行简化为一个值，一个代表它们体重的数字🐈🐦：

`.reduce((weightSum, animal) => weightSum + animal.weight, 0); // get total weight`

多亏了数组原型和 JavaScript 魔法，所有这些都可以轻松、完美地链接在一起。