# 2021 年要学习的 4 个 JavaScript 库

> 原文：<https://levelup.gitconnected.com/4-javascript-libraries-to-learn-in-2021-dedd6c0650ea>

![](img/9c2387296acfc02e15cb11d11e77d80d.png)

由[迪安·普](https://unsplash.com/@wezlar11?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我所有的文章现在都是免费的[这里](https://brandonburrus.com/blog)，不需要订阅 Medium！

不管喜欢还是讨厌，根据 [2020 堆栈溢出调查](https://insights.stackoverflow.com/survey/2020#technology-programming-scripting-and-markup-languages-professional-developers)，JavaScript 是最受欢迎的编程语言。多年来，这门语言围绕它开发了一个庞大的库、框架和工具生态系统。当你刚刚起步时，要成为一名熟练的开发人员，你需要学习的“东西”数量之多，一开始会让你感到不知所措。

作为一名 JavaScript 开发人员，你可以学习一些关键的库，它们将极大地提高你的价值。在您参与的任何给定项目中，您很可能会找到至少一个(如果不是几个)这样的库，这进一步增加了学习它们的价值。

这些库是按照学习难度排序的，所以建议一个一个地学习，直到你完全掌握为止。这篇文章的目标是解释这些库做什么，以及给你一个基本的介绍。我们将从最常见的 Lodash 开始。

# 洛达什

JavaScript 开发人员的虚拟工具带，了解 Lodash 提供的许多功能中的一些就可以大大提高开发人员的工作效率。

Lodash 本身是一个“实用程序”库，这意味着它的唯一目标就是做到这一点:为您提供一系列小的实用程序，帮助您完成日常任务，例如对数组进行分区、记忆函数或取消字符串中 HTML 实体的转义。

您不需要记住所有 Lodash 函数的完整列表。一种更简单的方法是浏览他们记录的函数列表，并在过程中记录有用或有趣的函数。

随着时间的推移，你会一次又一次地遇到同样的问题，很容易就能找到 Lodash 来帮你解决！

库本身最初是库下划线的一个分支，两者有很多共同之处。很有可能你工作的下一家公司会在每个项目中使用其中一个。

以下是我发现自己一次又一次使用的一些有用/有趣的 lodash 函数:

*   `partition()` —根据 O(n)时间内的谓词函数将一个数组分成两个独立的数组。
*   `compact()` —从给定数组中删除任何类型的 falsey 值。
*   `groupBy()` —根据给定的分组函数将集合分组到类似叶图的对象结构中。
*   `shuffle()` —将数组的索引打乱成随机顺序。
*   `sortBy()` —根据给定的谓词输入对数组进行排序(这比内置的数组`[.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)`方法更有用，因为它返回一个新数组，并且可以接受多个更简单的输入)。
*   `curry()`或`curryRight()` —执行功能，允许部分应用功能参数。
*   `debounce()` —根据定义的等待时间对给定功能执行去抖。
*   `memoize()` —记忆给定函数，在处理数组或对象时，也可以采用记忆解析器进行更复杂的记忆。
*   `cloneDeep()` —深度克隆一个对象。
*   `isNil()` —如果给定输入为`null`或`undefined`，则返回 true。
*   `round()` —将数字舍入到给定的精度。
*   `chain()` —用一个特殊的 Lodash 包装器对象包装一个值，允许您将任何 Lodash 函数链接成一个转换(您可以通过调用`.value()`结束该链接来获得最终的转换结果)。
*   `unescape()` —将 HTML 实体转换为这些实体所代表的实际 unicode 字符。
*   `get()` —基于给定路径安全地对对象进行深度遍历(如果您正在使用 TypeScript 或 ES2020+，请使用[可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)而不是使用这个)。

从这里开始使用 Lodash:[Lodash 文档](https://lodash.com/)。

# 不可变的. js

得益于近年来 React 的广泛采用，JavaScript 已经出现了使用更函数式编程风格的开发的趋势。函数式编程的一部分是利用[不可变的](https://en.wikipedia.org/wiki/Immutable_object)数据结构。然而，数组的许多内置函数是自变异的，这意味着它们就地改变数组(比如数组`[.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)`或`[.splice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)`)。

不可变. js 试图通过提供强大的不可变数据结构(如列表和映射)来解决这个问题。它使得在它提供的不可变对象和原生 JS 对象和数组之间的转换变得很好很容易。它还为操纵这些结构提供了比原生 JavaScript 为对象和数组提供的更多的转换和实用 API。

来自 Immutable.js 的核心结构的快速概述:

## 目录

列表本质上是数组的不可变版本:有序(索引)的项目集合。

我们可以像这样创建一个空列表:

```
const emptyList = List();
```

或者，我们可以从现有集合(如数组)创建一个列表:

```
const myList = List([1, 2, 3]);
```

所有像`filter()`和`map()`这样的转换方法都存在于列表中，就像我们对数组的期望一样:

```
const onlyEvents = List([1, 2, 3, 4, 5])
  .filter(n => n % 2 === 0);const doubledNums = List([1, 2, 3, 4, 5])
  .map(n => n * 2);
```

但是列表还有其他有用的 API，比如[交错](https://immutable-js.github.io/immutable-js/docs/#/List/interleave)或[子集检查](https://immutable-js.github.io/immutable-js/docs/#/List/isSubset)，它们使得使用列表变得非常容易。

列表的完整文档可以在[这里](https://immutable-js.github.io/immutable-js/docs/#/List)找到。

## 地图和订购地图

映射类似于对象，因为它是一个无序的键-值(或 KV)对的集合(OrderedMap 是相同的，只是键具有特定的顺序并且是可排序的)。

我们可以像创建空列表一样创建空映射:

```
const emptyMap = Map();
```

当然，我们可以从现有的 JS 对象创建一个地图:

```
const myMap = Map({ hello: 'world' });
```

该结构提供诸如检索、删除和合并之类的基本操作:

```
const majorCities = Map({
  germany: 'Berlin',
  kenya: 'Nairobi',
  russia: 'Moscow',
  colorado: 'Denver',
  japan: 'Tokyo',
});majorCities.get('germany');
majorCities.delete('berlin');
majorCities.merge({
  brazil: 'Rio de Janeiro',
  finlind: 'Helsinki',
  norway: 'Oslo',
});
```

这里要记住的关键是上面的结构是*不可变的*，所以当我们调用像`.delete()`或。`merge()`，这些方法返回一个*新的地图对象*，而不是修改原来的地图。

就像列表一样，地图有各种有用的方法来执行诸如[翻转](https://immutable-js.github.io/immutable-js/docs/#/Map/flip)、[取走](https://immutable-js.github.io/immutable-js/docs/#/Map/takeWhile)，甚至[减少](https://immutable-js.github.io/immutable-js/docs/#/Map/reduce)之类的事情。

完整的地图文档可以在[这里](https://immutable-js.github.io/immutable-js/docs/#/Map)找到，订购地图[可以在](https://immutable-js.github.io/immutable-js/docs/#/OrderedMap)这里找到。

## 集合和有序集合

集合是唯一值的集合(有序版本就是:唯一值的有序集合)。

我们可以像创建列表或地图一样创建集合:

```
const emptySet = Set();
const nums = Set([1, 1, 2, 2, 3, 3]);
```

如果我们记录`nums.size()`，我们会看到`3`。这是因为集合中唯一的唯一值是`1`、`2`和`3`。

当您需要处理独特的项目时，集合非常有用。您可能看到的一个技巧是从现有的`List`创建一个`OrderedSet`来删除任何重复项目的列表。

成套设备的完整文件可在[这里](https://immutable-js.github.io/immutable-js/docs/#/Set)找到，订购的成套设备的完整文件可在[这里](https://immutable-js.github.io/immutable-js/docs/#/OrderedSet)找到。

不仅仅是列表、映射和集合，Immutable.js 还有其他数据结构，比如[记录](https://immutable-js.github.io/immutable-js/docs/#/Record)、[堆栈](https://immutable-js.github.io/immutable-js/docs/#/Stack)和 [Seqs](https://immutable-js.github.io/immutable-js/docs/#/Seq) 等等。

从这里开始使用 immutable . js:[immutable . js 文档](https://immutable-js.github.io/immutable-js/)。

# RxJS

RxJS 是关于数据流和在这些数据流上执行可组合转换的。考虑这个库的一个更简单的方法是:

> "把 RxJS 想象成事件的 Lodash . "— [RxJS 文档](https://rxjs.dev/guide/overview)

RxJS 使得处理复杂的时间变化(异步)变得更加容易。

## 看得见的

核心实体是[可观测的](https://rxjs.dev/guide/observable)。你可以把一个可观测值想象成一个值的数组，只是这些值可以用时间来分隔。这就是我们得到“流”的概念的地方，就像一段时间内的数据或值的流。

可观察性使得与这些流一起工作(并对其做出反应)变得美好而简洁。以 RxJS 的`from()`函数为例，它接受一个数组作为参数，并返回该数组作为可观察值。一旦我们有了一个观察对象，我们通常会想要订阅它。

```
const nums$ = from([1, 2, 3]);nums$.subscribe(value => {
  console.log(value);
});
```

`nums$`后的美元符号是 JS 开发人员在使用 RxJS 时喜欢使用的约定，只是用来表示变量包含对可观察对象的引用。

在代码示例中，我们从一组数字中创建了可观察的`nums$`。一旦我们有了那个可观察的，我们就可以订阅它来记录来自那个可观察的流的所有值。

让我们看一个更有用的例子:

```
const clicks$ = fromEvent(
  document.querySelector('#myBtn'),
  'click'
);let clickCount = 0;clicks$.subscribe(clickEvent => {
  clickCount++;
});
```

在这个例子中，我们使用 RxJS 的`fromEvent()`函数从 DOM 节点事件监听器创建一个可观察对象。这给了我们一个事件流，我们可以用它来增加我们的点击次数。

但是我们使用一种非常命令式的程序来处理点击量。RxJS 真正的闪光点在于它能够将复杂的变换应用到可观察的事物上。

## 经营者

RxJS 方面的运算符是将变换应用于可观察对象的特殊函数。以我们的点击计数器为例:我们将使用`scan()`操作符将每个点击事件转换成一个递增的数字，而不是强制性地跟踪变量中的点击次数。

因此，我们将从可观察到的点击事件转变为可观察到的(不断增加的)数量。

```
const clicks$ = fromEvent(
  document.querySelector('#myBtn'),
  'click'
);clicks$
  .pipe(
    scan(accumulator => accumulator + 1, 0)
  )
  .subscribe(clickCount => {
    console.log(clickCount);
  });
```

使用`.pipe()`方法将运算符应用于可观察值。`scan()`操作符的工作方式类似于`[.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)`的工作方式:它接受一个函数和一个初始累加器值作为参数。该函数获取累加器的当前值和可观测值作为参数，需要返回下一个新的累加器值。

在上面的例子中，扫描函数参数不关心事件，只关心累加器(这就是为什么我们忽略第二个参数)。初始累加器值为 0，每次单击按钮时，操作符都会将该事件转换为一个递增的数字。

## 捐款

observables 的一个重要方面是调用`.subscribe()`方法时返回的订阅对象。

可观测量可以是有限的，也可以是无限的。当一个可观察对象是有限的(比如我们的数组示例)，一旦可观察流完成，订阅将自动关闭。然而，对于无限的可观察对象(比如我们的点击事件可观察对象),我们需要确保在某个时候显式地取消订阅可观察对象，否则我们会在应用程序中遇到内存泄漏。

这可以通过简单地在返回的订阅对象上调用`.unsubscribe()`方法来完成。

```
const clicks$ = fromEvent(
  document.querySelector('#myBtn'),
  'click'
);const clickSubscription = clicks$.subscribe(e => {
  console.log(e);
});clickSubscription.unsubscribe();
```

这只是 RxJS 的冰山一角。该库有超过*一百个* [操作符](https://rxjs.dev/guide/operators)随时可供您使用，您也可以创建自己的操作符！

如果你仍然不相信 RxJS 是一个非常有用的库，那么 André Staltz 撰写的文章[](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)*[对 RxJS 的所有原因和方法进行了更详细的介绍。](https://staltz.com/)*

*从这里开始 RxJS:[RxJS 文档](https://rxjs.dev/guide/overview)。*

# *拉姆达*

*Ramda 基本上做了与 Lodash 相同的事情(提供了大量真正有用的实用函数)，但是有一个巨大的不同:Ramda 实用程序都是用于[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)风格的。*

*如果你想从 Haskell、Elixir 或 OCaml 等语言学习 JavaScript，Ramda 将是你最好的朋友。*

*函数式编程都是关于函数的。*咄！*函数式编程的核心基础之一是使用*组合*。*

*我们来看下面这个纯函数:*

```
*const double = n => n * 2;*
```

*现在假设我们想得到一个数，并把它翻两番。我们当然可以把这个数乘以 4，*或者*我们可以对这个数*调用两次这个`double`函数。**

```
*double(double(42));*
```

*我们可以将这四倍的功能抽象成它自己的功能，如下所示:*

```
*const quadruple = n => double(double(n));*
```

*这就是组合的意义所在:获取现有的功能，然后*将它们组合在一起形成更大的功能。在示例中，`quadruple`函数由`double`函数组成。**

*Ramda 可以通过使用`compose()`函数使其更具可读性:*

```
*const quadruple = compose(double, double);*
```

*而由此产生的`quadruple`功能也是一样的。*

*所有的 Ramda 函数都被设计成使用 composition，就好像你在使用一种真正的纯函数式语言一样。*

*Ramda 的每个函数都有两条规则:*

1.  *每个功能都是 [*化*](https://en.wikipedia.org/wiki/Currying) 。*
2.  *每个函数都将函数操作的数据定义为最后一个参数。*

*例如，Ramda 有一个`filter()`函数，其工作原理与本机`[.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`方法基本相同:*

```
*const nums = [1, 2, 3];const onlyEven = filter(n => n % 2 === 0, nums);*
```

*起初这看起来很奇怪，因为我们通常认为`nums`数组应该是`filter()`的第一个参数，而不是过滤函数。*

*但是请记住，来自 ramda 的`filter()`是咖喱，所以我们实际上可以这样做:*

```
*const nums = [1, 2, 3];const evenFilter = filter(n => n % 2 === 0);const onlyEven = eventFilter(nums);*
```

*将数据自变量与组合函数结合起来，您可以定义如下函数转换函数:*

```
*const transform = compose(
  add(2),
  multiply(2),
  subtract(5)
);transform(10); // 12*
```

*这个例子是人为的，但是将这个技术应用到更复杂的东西上，我们可以看到像 Ramda 这样的东西带来的真正价值。*

*假设我有以下数据结构:*

```
*const records = [
  {
    title: 'Section B',
    entries: [5, 1, 2, 10, 7],
  },
  {
    title: 'Section A',
    entries: [4, 1, 2, 7]
  },
];*
```

*这里的目标是做以下事情:*

*   *按标题对数组中的每个记录对象进行升序排序*
*   *按数字降序对每个记录对象中的嵌套条目进行排序*

*使用 Ramda 实用程序，我们可以创建自己的映射实用程序来满足这些需求，看起来可能是这样的:*

```
*const recordMapper = compose(
  map(over(lensProp("entries"), sort(descend(identity)))),
  sort(ascend(prop("title")))
);*
```

*我们并不是在这里玩代码高尔夫，但是想象一下，在命令式风格中，同一个转换函数需要多少行代码。相反，我们基本上将所有的逻辑压缩成了三行。*

*这个例子相当严格，因为它只适用于我们给定的数据结构。通常，您会先将这个实用程序分成几个较小的函数。这就是通过组合进行函数式编程的最终目标:用更小的函数构建更大的函数！*

*如果你真的想通过 Ramda 更深入地使用函数式编程风格的方法，Randy Coulman 撰写的精彩文章系列 [*在 Ramda*](https://randycoulman.com/blog/categories/thinking-in-ramda/) 中思考是一个很好的起点！*

*这里值得注意的是，Ramda 是你在项目中可能看到的最不常见的库。然而，如果您仍然想使用函数式方法，Lodash 有一个子模块`[lodash/fp](https://github.com/lodash/lodash/wiki/FP-Guide)`，它是以 FP 方式构建的所有 Lodash 函数。*

*从这里开始使用 Ramda:[Ramda document ation](https://ramdajs.com/)。*

# *结论*

*学习这些库不仅会让你成为更好的 JavaScript 开发人员，而且会让你成为更好的开发人员。这是因为所讨论的 4 个库中的 3 个不仅为您提供了非常有用的功能，还因为它们挑战了传统的命令式或面向对象的编程风格，大多数人在学习 JS 这样的编程语言时首先学习的就是这种风格。*

*成为一名优秀程序员的一个基本方面是确保为工作选择正确的工具。这正是这些库的作用:一套供您使用的工具，只是您解决任何给定问题的众多方法之一。最终，这就是我们作为开发人员要做的事情:解决问题。*