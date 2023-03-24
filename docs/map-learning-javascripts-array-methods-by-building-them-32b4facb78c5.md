# map:通过构建 JavaScript 的数组方法来学习它们

> 原文：<https://levelup.gitconnected.com/map-learning-javascripts-array-methods-by-building-them-32b4facb78c5>

![](img/c7cee700cac8848e1fecd8ae48153af7.png)

探索 JavaScript 数组方法系列文章的第二篇，包括如何构建自己的数组方法。本文涵盖了`map`方法。

# 介绍

JavaScript 的`map`方法是处理数组的强大方法，也是最常用的方法之一。您可能会在 React 代码库中看到它将 API 中的一组数据转换为组件数组。它在复杂性上也比`forEach`高了一小步，之前我们在[讨论过。](https://zkf.io/js-array-methods-foreach/)

本文是我解释 JavaScript 内置数组方法的系列文章的一部分。本文主要关注`map`方法，它用于在现有数组的基础上创建一个新数组。我将描述它是如何工作的以及何时使用它，但是文章的大部分将集中在为你自己实现`map`上。

# 它是如何工作的

`map`方法获取一个现有的数组和一个函数，并对数组的每一项执行该函数。这就像`forEach`方法一样，但是`forEach`和`map`之间有一个关键的区别:`map`方法基于执行传入函数的结果返回一个新数组。对于传递给`map`的函数有一个微妙的要求:它必须返回一些东西，这样`map`就可以将它添加到它为我们创建的数组中。

描述`map`的一种技术方式是:它基于一个函数创建一个*派生*数组；

```
const numbers = [1, 2, 3, 4, 5]function doubleNumber(number) {
	return number * 2
}const doubles = numbers.map(doubleNumber)
console.log(doubles) // [2, 4, 6, 8, 10]
```

我们从一个名为`numbers`的数字数组和一个名为`doubleNumber`的函数开始，该函数接受一个参数并返回其 double 值(乘以 2 的结果)。通过将`doubleNumber`传递给`map`，我们说，“对`numbers`数组中的每一项调用该函数”，这将产生数组`[2, 4, 6, 8, 10]`(或原始数组，`numbers`包含值`[1, 2, 3, 4, 5]`)。

重要的是要记住每一个数组方法都可以用一个`for`循环来实现，包括`map`。

一个初学者可能会用类似这样的东西来完成将数组中的每一项加倍的任务:

```
const numbers = [1, 2, 3, 4, 5];
const doubles = []for (let i = 0; i < numbers.length; i++) {
  let number = numbers[i]
  let double = number * 2
  doubles.push(double)
}console.log(doubles) // [2, 4, 6, 8, 10]
```

我们可以通过将我们正在执行的实际“动作”转换成它自己的函数并带回我们的`doubleNumber`函数来简化这一点:

```
const numbers = [1, 2, 3, 4, 5];
const doubles = []function doubleNumber(number) {
  return number * 2
}for (let i = 0; i < numbers.length; i++) {
  let number = numbers[i]
  let double = doubleNumber(number)
  doubles.push(double)
}console.log(doubles) // [2, 4, 6, 8, 10]
```

这实际上让我们非常接近自己的`map`实现。

# 实现我们自己的

我们前面说过`map`用于创建一个*派生*数组:

```
const numbers = [1, 2, 3, 4, 5]function doubleNumber(number) {
	return number * 2
}const doubles = numbers.map(doubleNumber)
console.log(doubles) // [2, 4, 6, 8, 10]
```

前面的代码片段从数组`numbers`开始，使用`map`和函数`doubleNumbers`创建`doubles`数组。

基于我们在这里如何使用它，我们可以确定对`map`方法的要求是:

*   接受一个数组和一个函数的函数
*   对传入数组中的每一项调用传入函数
*   将函数调用的结果存储在一个新数组中
*   返回新数组

从第一个需求开始，我们可以创建一个名为`map`的函数，它接受一个数组和一个函数作为参数:

```
function map(arr, fn) {
  // more to come here
}
```

然后，我们希望使用与上面基本相同的循环来遍历数组，这样我们就可以在下一步中对每一项调用传入的函数:

```
function map(arr, fn) {
  for (let i = 0; i < arr.length; i++) {
	  // more to come here
  }
}
```

现在我们将数组中的每一项传递给`fn`:

```
function map(arr, fn) {
  for (let i = 0; i < arr.length; i++) {
	  let item = arr[i]
	  let newItem = fn(item)
  }
}
```

我们从函数中得到了新的值。根据需求，我们需要将它存储到一个数组中并返回该数组:

```
function map(arr, fn) {
	const final = [] for (let i = 0; i < arr.length; i++) {
	  let item = arr[i]
	  let newItem = fn(item) final.push(newItem)
  } return final
}
```

这就是全部了！

用我们的`map`函数将数组中的每一项加倍，现在看起来像这样:

```
const numbers = [1, 2, 3, 4, 5, 6];const doubles = map(numbers, doubleNumber)
console.log(doubles) // [2, 4, 6, 8, 10, 12]
```

就像`forEach`一样，这比写出完整的`for`循环更容易阅读和理解。

# `map`在行动

现在我们已经为自己实现了`map`,我们可以进一步探索它的用途。我通常在两种情况下使用`map`:*转换*数据数组或者*从现有数组中提取*数据。

我将使用以下数据向您展示两者的示例:

```
const profiles = [
  {
    name: "Mercedes",
    profession: "Software Engineer"
  },
  {
    name: "Antonio",
    profession: "Front-end Developer"
  },
  {
    name: "Ana",
    profession: "Back-end Developer"
  }
]
```

这个数组包含代表工程师的对象，其中每个工程师的配置文件包含他们的姓名和职业。

# 转换数据

上面的`doubles`例子是一个使用`map`转换数据的简单例子。不过，我们的数据通常没那么简单。因此，一个更复杂的例子是利用我们的`profiles`数组，为每个工程师创建一个新的描述数组。

```
const descriptions = map(
  profiles,
  function createDescription(profile) {
    let {name, profession} = profile
    return `${name} is a ${profession}`
  }
)console.log(descriptions) // [ 'Mercedes is a Software Engineer', 'Antonio is a Front-end Developer', 'Ana is a Back-end Developer' ]
```

在上面的代码片段中，我们用`createDescription`函数获取配置文件和`map`的数组，该函数获取一个配置文件并返回一个字符串`"<name> is a <profession>"`。现在，我们有了一个配置文件数组和一个包含每个工程师描述的单独数组。

我们可以稍微修改一下，给我们一个新的概要数组，每个概要都包含描述。这通常更有帮助:

```
map(
  profiles,
  function createDescription(profile) {
    let {name, profession} = profile
    return {
      ...profile,
      description: `${name} is a ${profession}`
    }
  }
)
```

这段代码将生成如下所示的数组:

```
[
  {
    name: 'Mercedes',
    profession: 'Software Engineer',
    description: 'Mercedes is a Software Engineer'
  },
  {
    name: 'Antonio',
    profession: 'Front-end Developer',
    description: 'Antonio is a Front-end Developer'
  },
  {
    name: 'Ana',
    profession: 'Back-end Developer',
    description: 'Ana is a Back-end Developer'
  }
]
```

# 提取数据

使用`map`的另一种常见方式是将数据提取到一个单独的数组中。例如，我们可以用我们的`profiles`数组创建一个只有名字的数组:

```
const names = map(profiles, function getProfileNames(profile) {
  return profile.name
})console.log(names)
```

当一个 API 给了你很多你不需要的数据时，这是很常见的。当我使用 API 的图表库和数据时，我发现自己经常做这样的事情。来自 API 的数据以 API 模式确定的格式返回，我需要提取一些数据，并将其转换成图表库可以使用的格式。

# 结论

我对`map`的讨论到此结束。如果你没有看过`forEach`的类似讨论，那么你可以在这里[找到](https://zkf.io/js-array-methods-foreach/)。我将详细介绍并解释和实现每个内置数组方法，这将帮助您理解它们是如何工作的，以及何时和如何使用它们。要跟进，注册我的[简讯](https://hawthorne.substack.com)并在 [Twitter](https://twitter.com/ZFleischmann) 上关注我。