# JavaScript 干净代码—函数

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-functions-f719c326d624>

![](img/bf55cd349bc796881a5071d33a1f00d7.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[诚信公司](https://unsplash.com/@honest?utm_source=medium&utm_medium=referral)的照片

函数是 JavaScript 程序的重要组成部分。它们用于将代码分割成可重用的块，这些块只做一件事。

因此，为了拥有清晰的 JavaScript 代码，我们必须拥有易于理解的函数。

在本文中，我们将看看如何编写干净、易于阅读和修改的函数。最重要的是写小函数。

# 小功能

功能应该很小。更小的函数做得更少，也更容易阅读。函数中的每一行应该在 100 个字符左右，这样它们就可以在任何屏幕上显示。

做得越少意味着代码越少，意味着更容易阅读。如果一个功能不止做几件事，那么它应该被分成更小的功能。

在旧的 JavaScript 代码中制作小函数是非常困难的，因为函数被用于许多它们不应该用于的事情，比如创建块和命名空间代码。

然而，现在我们有了 JavaScript 模块作为标准，我们可以逐步将函数转换为做函数应该做的事情，也就是做一件事情。

例如，不是创建具有如下功能的块:

```
(function() {
  var numFruits = 1;
})();
```

我们可以改为写:

```
{
  let numFruits = 1;
};
```

它们都创建了隔离，但是我们没有滥用函数，相反，我们只有一个隔离的代码块。我们可以在 ES6 或更高版本中实现这一点。

`let`和`const`应该分别用于创建块级变量和常量。

此外，如果我们想把相关的代码放入一个组中，我们可以使用模块。它们可以通过导入另一个模块的导出成员来使用。

例如，我们可以创建一个名为`fruit.js`的文件，如下所示导出一个成员:

```
export color = 'red';
```

然后我们可以将它导入到另一个名为`main.js`的模块中，假设它们在同一个文件夹中，如下所示:

```
import { color } from './fruit'
console.log(color);
```

然后我们不用函数就能得到代码隔离。

# 区块和缩进

缩进应该由代码格式化程序自动完成。我们应该有缩进两个空格的条件语句和循环。

空格比制表符好，因为它们不会产生不同操作系统的问题。在某些系统中，选项卡可能看起来很混乱。

如果我们不使用自动格式化 JavaScript 代码的文本编辑器，我们可以使用 ESLine、JSLint 或其他 linters 来处理缩进。

# 尽可能少做

通常，好的函数应该只做一件事。长函数很难阅读，如果它们有很多内容，那么会让代码的读者感到困惑。

有一件事可能很难知道。如果它做的动作不止一个，那么很可能就太多了。

例如，向用户呈现简单 HTML 的代码可以是一个函数，因为这就是它的全部功能。

然而，如果 HTML 中有许多部分，比如在多个地方循环从 API 中检索到的项目，以及 if 语句等等，那么它们就应该分成自己的函数。

如果一个函数有很多条件和循环，那么它们可以被拆分成各自的函数。

另一种知道我们是否可以将某个东西移动到它自己的函数中的方法是，我们可以描述这段代码，而不必重述函数的实现。

![](img/f2eb10c30d43b7213cfa2a5aa0f5fd04.png)

蒂娜·道森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 一个抽象层次

每个功能应该只有一个抽象层次。这意味着如果一个函数做一些高度抽象的事情，那么它应该只做这些事情。

例如，如果我们想写一个函数，循环遍历一个数组的元素，并将其添加到一个列表中，那么它应该只做这一步。

下面是根据抽象级别将代码划分为功能的示例:

```
const addFruitLis = (fruits, ul) => {
  for (const f of fruits) {
    const li = document.createElement('li');
    li.innerHTML = f;
    ul.appendChild(li);
  };
}const addFruitUl = (fruits) => {
  const ul = document.createElement('ul');
  addFruitLis(fruits, ul);
  document.body.appendChild(ul);  
}const fruits = ['apple', 'orange', 'grape'];
addFruitUl(fruits);
```

在上面的代码中，我们有一个函数`addFruitLis`，它创建了`li`元素，并将其附加到参数中的`ul`元素。

这是一个抽象层次，因为我们在生成`ul`元素之后添加了`li`元素。从等级上来说比`ul`低一级。

然后我们定义了`addFruitUl`函数来创建`ul`元素，并将`li`元素的添加委托给`addFruitLis`函数。然后将`ul`追加到文档的主体。这样每个函数只做尽可能少的事。

最后，我们通过传入一个数组来调用`addFruitUl`函数，然后获取页面上的元素。

每个函数只处理一个抽象层次，因为`addFruitLis`只处理`ul`元素中的`li`元素，而`addFruitUl`只处理`ul`元素。

编写上述代码的错误方法是将所有内容都合并到一个函数中。这使得函数的代码变得复杂和混乱。

# 结论

函数应该做一点可能的。我们可以通过将它们隔离在块和模块中来做到这一点。使用函数来做这件事的旧代码应该被淘汰。

此外，每个功能应该尽可能地少做，并且只处理一个抽象层次。否则，函数的代码会变得冗长而混乱。