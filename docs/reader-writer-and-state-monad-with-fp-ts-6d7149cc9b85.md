# 使用 fp-ts 的 TypeScript 中的读取器、编写器和状态单子

> 原文：<https://levelup.gitconnected.com/reader-writer-and-state-monad-with-fp-ts-6d7149cc9b85>

## 使用类型脚本的函数式编程

## 如何用三个简单的单子丰富你的 fp-ts 工作流程

![](img/02d48889ae77a592d8b66b970ec14a6f.png)

来源:https://www.pexels.com/

读取器、写入器和状态单子是函数式编程中三个非常常用的概念。Fp-ts 支持这些现成的类型，尽管它们不像有良好文档记录的选项或任务单子那样受欢迎。为了简化读者、作者和状态对初学者的使用，下面的文章借助一些简单的打字稿示例对这个主题进行了简短的介绍。

# 《朗读者》

fp-ts 中阅读器 monad 的接口如下:

```
export interface Reader<R, A> {
  (r: R): A
}
```

读取器只是一个接收类型 R 作为参数并返回类型 a 的值的函数。因此，读取器“读取”值 R，处理它并返回结果。这里重要的是，阅读器在概念上不会改变输入值。这就是为什么 Reader monad 非常适合通过一系列读取配置值的函数来传递配置。命令式编程中的对应物是对全局配置变量的访问。

举个例子，假设我们想写一个简单的函数来计算分数。分子应该是函数的参数值，而分母值应该从配置中读取。然后，我们希望应用分数函数三次，并返回结果。

首先，我们定义配置类型:

```
type ReaderConfig = {
    denominator: number;
};
```

然后，我们定义高阶函数“fraction ”,它获取分子值并返回一个读者单子:

```
import * as R from 'fp-ts/Reader';type ReaderConfig = {
    denominator: number;
};const fraction: (numerator: number) => 
R.Reader<ReaderConfig, number> = (numerator: number) => 
(config: ReaderConfig) => numerator / config.denominator;
```

之后，我们在 fp-ts 管道的帮助下三次组合分数函数:

```
import * as R from 'fp-ts/Reader';
import * as f from 'fp-ts/function';type ReaderConfig = {
    denominator: number;
};const fraction: (numerator: number) => 
R.Reader<ReaderConfig, number> =(numerator: number) => 
(config: ReaderConfig) => numerator / config.denominator;const fractionThreeTimes = (numerator: number) =>
f.pipe(
  numerator,
  fraction,
  R.chain(fraction),
  R.chain(fraction)
);
```

这里很酷的一点是，配置值本身在组合中并不显式可见——它只是通过管道“隐藏”起来。我们只需要在最后触发管道时注入配置:

```
import * as R from 'fp-ts/Reader';
import * as f from 'fp-ts/function';type ReaderConfig = {
    denominator: number;
};const fraction: (numerator: number) => 
R.Reader<ReaderConfig, number> = (numerator: number) => 
(config: ReaderConfig) => numerator / config.denominator;const fractionThreeTimes = (numerator: number) =>
f.pipe(
  numerator,
  fraction,
  R.chain(fraction),
  R.chain(fraction)
);const startValue = 10;
const config: ReaderConfig = {
  denominator: 2
};console.log(fractionThreeTimes(startValue)(config)); // Returns 1.25
```

# 作家

fp-ts 中的 Writer monad 的接口如下:

```
export interface Writer<W, A> {
  (): [A, W]
}
```

与读取器相反，写入器 monad 根本不接收任何输入，但是除了类型 a 的实际值之外，还将类型 W 的附加值“写入”到元组类型中。通常，类型 W 是幺半群，这意味着该类型可以连接其值。典型的幺半群是数组，它甚至在 Javascript 规范中有一个名为“concat”的函数。那么这个单子有什么用呢？一个经典的例子是日志记录:除了计算类型 A 的值之外，monad 还返回类型 W 的日志条目，因此这不会被计算为副作用，而是将在工作流结束时进行处理。命令式编程中的对应物是标准的“console.log()”，从概念上讲，它是对全局“控制台”变量的访问。

作为一个例子，让我们假设我们想写一个计算分数的替代函数，这一次有额外的日志记录。因为我们不能再从注入的配置中读取分母值，所以我们现在需要将它硬编码到函数本身中。让我们从创建一个带有数组幺半群的 Writer monad 开始，这样我们工作流的所有日志都被连接到一个字符串数组中。帮助器方法“getChain”返回一个实际的 Monad 对象，它有一个“Chain”方法:

```
import * as W from 'fp-ts/Writer';
import * as A from 'fp-ts/Array';const ArrayWriter = W.getChain<Array<string>>(A.getMonoid());
```

然后，我们定义我们的高阶函数“fractionWithStaticDenominator ”,它获取分子值并返回一个编写器单子，包括作为数组的日志字符串:

```
import * as W from 'fp-ts/Writer';
import * as A from 'fp-ts/Array';const ArrayWriter = W.getChain<Array<string>>(A.getMonoid());const fractionWithStaticDenominator: (numerator: number) => W.Writer<Array<string>, number> = (numerator: number) => () => [numerator / 2, [`Numerator: ${numerator}. Denominator: 2`]];
// The denominator of 2 is hardcoded because we cannot read it from anywhere
```

之后，我们在 fp-ts 管道的帮助下三次组合分数函数:

```
import * as W from 'fp-ts/Writer';
import * as A from 'fp-ts/Array';
import * as f from 'fp-ts/function';const ArrayWriter = W.getChain<Array<string>>(A.getMonoid());const fractionWithStaticDenominator: (numerator: number) => W.Writer<Array<string>, number> = (numerator: number) => () => [numerator / 2, [`Numerator: ${numerator}. Denominator: 2`]];
// The denominator of 2 is hardcoded because we cannot read it from anywhereconst fractionThreeTimes = (numerator: number) => 
f.pipe(
  numerator,
  fractionWithStaticDenominator,
  (writer) => ArrayWriter.chain(writer,     fractionWithStaticDenominator),
  (writer) => ArrayWriter.chain(writer, fractionWithStaticDenominator)
);
```

这种组合不如 Reader 中的组合漂亮，因为 fp-ts 中的“ArrayWriter.chain”方法不是 curried，它同时接受两个参数。但是基本流程保持不变。我们可以像在读取器的情况下那样触发流，这一次没有配置:

```
import * as W from 'fp-ts/Writer';
import * as A from 'fp-ts/Array';
import * as f from 'fp-ts/function';const ArrayWriter = W.getChain<Array<string>>(A.getMonoid());const fractionWithStaticDenominator: (numerator: number) => W.Writer<Array<string>, number> = (numerator: number) => () => [numerator / 2, [`Numerator: ${numerator}. Denominator: 2`]];
// The denominator of 2 is hardcoded because we cannot read it from anywhereconst fractionThreeTimes = (numerator: number) => 
f.pipe(
  numerator,
  fractionWithStaticDenominator,
  (writer) => ArrayWriter.chain(writer, fractionWithStaticDenominator),
  (writer) => ArrayWriter.chain(writer, fractionWithStaticDenominator)
);const startValue = 10;
console.log(fractionThreeTimes(startValue)());
/**
[
  1.25,
  [
    'Numerator: 10\. Denominator: 2',
    'Numerator: 5\. Denominator: 2',
    'Numerator: 2.5\. Denominator: 2'
  ]
]
*/
```

Writer 模式的好处是，我们不需要显式地处理日志值的连接，而是可以专注于修改类型 A 的实际值。其他一切都在“引擎盖下”再次处理。

# 国家

读取器可以读取值，写入器可以写入——组合就是状态单子！它是三个单子中最强大的，因为它不仅可以读取配置，还可以修改它。这就是为什么注入的配置值实际上变成了通过工作流传输的状态值。

fp-ts 中状态 monad 的接口如下:

```
export interface State<S, A> {
  (s: S): [A, S]
}
```

状态接收类型 S，处理它并将类型 S 的(潜在的)新值与类型 A 的值一起写入元组。命令式编程中的对应物将是全局变量的访问和突变，例如共享映射。

作为一个例子，让我们通过处理状态单子来扩展我们的分数函数。首先，我们需要定义新的配置类型:

```
type WriterConfig = {
    denominator: number;
    logs: Array<string>
};
```

现在，我们可以定义“fractionWithLogs”函数来读取和修改配置，并计算分数:

```
import * as S from 'fp-ts/State';type WriterConfig = {
    denominator: number;
    logs: Array<string>
};const fractionWithLogs: (numerator: number) => 
S.State<Config, number> = (numerator: number) => (c: Config) =>
[
  numerator / c.denominator,
  {
    denominator: c.denominator,
    logs: [
      …c.logs,
      `Numerator: ${numerator}. Denominator: ${c.denominator}`
    ]
  }
];
```

之后，我们可以再次构建函数:

```
import * as S from 'fp-ts/State';
import * as f from 'fp-ts/function';type WriterConfig = {
    denominator: number;
    logs: Array<string>
};const fractionWithLogs: (numerator: number) => 
S.State<Config, number> = (numerator: number) => (c: Config) =>
[
  numerator / c.denominator,
  {
    denominator: c.denominator,
    logs: [
      …c.logs,
      `Numerator: ${numerator}. Denominator: ${c.denominator}`
    ]
  }
];const fractionThreeTimes = (numerator: number) =>
f.pipe(
  numerator,
  fractionWithLogs,
  S.chain(fractionWithLogs),
  S.chain(fractionWithLogs)
);
```

最后，我们用一个配置值触发工作流:

```
import * as S from 'fp-ts/State';
import * as f from 'fp-ts/function';type WriterConfig = {
    denominator: number;
    logs: Array<string>
};const fractionWithLogs: (numerator: number) => 
S.State<Config, number> = (numerator: number) => (c: Config) =>
[
  numerator / c.denominator,
  {
    denominator: c.denominator,
    logs: [
      …c.logs,
      `Numerator: ${numerator}. Denominator: ${c.denominator}`
    ]
  }
];const fractionThreeTimes = (numerator: number) =>
f.pipe(
  numerator,
  fractionWithLogs,
  S.chain(fractionWithLogs),
  S.chain(fractionWithLogs)
);const startValue = 10;
const config: Config = {
  denominator: 2,
  logs: []
};console.log(fractionThreeTimes(startValue)(config));
/**
[
  1.25,
  {
    denominator: 2,
    logs: [
      'Numerator: 10\. Denominator: 2',
      'Numerator: 5\. Denominator: 2',
      'Numerator: 2.5\. Denominator: 2'
    ]
  }
]
*/
```

如果我们愿意，我们也可以改变分母的值，这个值已经在“Writer”示例中进行了硬编码。这显示了状态单子对于许多工作流是多么灵活。然而，作为一个经验法则，如果你真的需要这个功能，你应该只使用更复杂的单子。否则，函数定义会变得过于复杂，并误导其他开发人员。当然，在 fp-ts 库中有更多的组合子和实用函数可供读者、作者和状态单子使用，可以用于更复杂的用例。看看官方文件就知道了:[https://gcanti.github.io/fp-ts/modules/](https://gcanti.github.io/fp-ts/modules/)。