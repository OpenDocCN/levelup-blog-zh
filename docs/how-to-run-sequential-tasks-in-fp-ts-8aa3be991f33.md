# 如何使用 fp-ts 在 TypeScript 中运行顺序任务

> 原文：<https://levelup.gitconnected.com/how-to-run-sequential-tasks-in-fp-ts-8aa3be991f33>

## 以打字打的文件

将并行工作流转换为顺序工作流的简单方法

![](img/a1555be88f0d414d99ec0446a52d1a19.png)

来源:https://unsplash.com/

当处理异步编程时，您基本上可以在两种不同的工作流之间进行选择:作为一组并行任务运行和按顺序运行该组任务。虽然并行运行通常是最有效的选择，但有时也有顺序设置的原因。最容易忘记的是变量依赖，这意味着第二个任务依赖于第一个任务的输出。其他更微妙的情况是，例如，由于成本或许可问题，API 不能并行处理多个请求，或者只允许顺序请求。

# 异步等待方法

让我们首先看看 Typescript-native 异步-await 方法。这里，并行流非常简单，因为您可以使用内置的 Promise 功能:

```
const parallelResult = await Promise.all([task1(), task2()]);
```

顺序流有点冗长，至少如果您想使用 async-await 的话。事实上，为此您需要一个专用的 for 循环:

```
const tasks: Array<Promise<string>> = [task1, task2];
for (const task of tasks) {
    await task();
}
```

# fp-ts 方法

如果您喜欢使用 fp-ts 进行函数式编程，并且希望编写函数而不是等待中间结果，那么您可能会处理以下两种结构:数组和任务。为了简单起见，我在这里不使用老大哥任务，但是您也可以将下面的方法应用于任务。

与本机 Typescript 一样，并行流的 fp-ts 方法非常简单:

```
import * as f from "fp-ts/function";
import * as T from "fp-ts/Task";const tasks: Array<Task<string>> = [task1, task2];await f.pipe(tasks, T.sequenceArray)(); // Runs in parallel
```

我们将任务的数组转换成带有数组的单个任务，然后等待管道的并行执行。但是我们怎样才能顺序地运行这个任务呢？这里的关键是要理解“序列”不是任务的固有属性，而是数组的属性。因此，“T.sequenceArray”更像是一个辅助函数，也可以用等价的数组实例函数来表示:

```
import * as f from "fp-ts/function";
import * as T from "fp-ts/Task";
import * as Afrom "fp-ts/Array";const tasks: Array<Task<string>> = [task1, task2];await f.pipe(tasks, A.sequence(T.ApplicativePar))(); // Runs in parallel
```

你可能注意到了“A.sequence”的论点:我们需要在这里注入一个任务模块的应用实例，以使序列功能工作。令人欣慰的是，fp-ts 为我们提供了并行执行所必需的应用程序:“T.ApplicativePar”。有了这些知识，最后一步就很容易了:我们不需要使用提供的 Applicative 进行并行执行，而是需要使用一个 Applicative 进行顺序执行:

```
import * as f from "fp-ts/function";
import * as T from "fp-ts/Task";
import * as Afrom "fp-ts/Array";const tasks: Array<Task<string>> = [task1, task2];await f.pipe(tasks, A.sequence(T.ApplicativeSeq))(); // Runs in sequence
```

这个简单的代码片段解决了整个谜团，代码看起来甚至比 Promise for-loop 中的代码还要干净。但是在图书馆里有更多额外的帮助来改善你的异步工作流程，只需在 https://gcanti.github.io/fp-ts/modules/的[查阅文档。](https://gcanti.github.io/fp-ts/modules/)