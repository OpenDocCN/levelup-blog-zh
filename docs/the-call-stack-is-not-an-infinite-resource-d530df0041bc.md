# 调用堆栈不是无限的资源——如何避免 JavaScript 中的堆栈溢出

> 原文：<https://levelup.gitconnected.com/the-call-stack-is-not-an-infinite-resource-d530df0041bc>

![](img/b4f567a12862352e8858f31c091ca317.png)

照片由[布里吉特·托姆](https://unsplash.com/@brigittetohm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/stack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

## 使用 JavaScript 来理解递归如何导致堆栈溢出，以及防止它发生的技巧

[调用栈](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)不是无限的资源。在执行深度递归时尤其如此。函数内部的函数调用被放在调用栈的更高层，这意味着每个递归调用都位于调用栈中，等待被执行。

在 Javascript 中[调用栈的大小是不固定的，但是它并不大。让我们看一个微不足道的例子:](https://2ality.com/2014/04/call-stack-size.html)

```
let ct = 0;
const MAX = 100_000const recurse = () => {
  if (++ct > MAX) return
  recurse()
}try {
  recurse()
} catch (e) {
  console.error({ ct, e })
}
```

如果我在 Node.js 中运行它，我会得到以下输出:

```
{
  ct: 15711,
  e: RangeError: Maximum call stack size exceeded
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:4:17)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
      at recurse (/home/jlowery2663/projects/stacksmash/overflow.js:6:3)
}
```

在第 15，711 次迭代时发生堆栈溢出。以下是一些防止这种情况发生的技巧。

# 设置超时

setTimeout 函数调度一个函数调用，由事件循环在将来的某个时间点处理。在这种情况下，不会发生真正的递归:内部的“递归”函数将会执行，但是外部的函数将会返回，因此不再在事件消息队列中(告诉事件循环做什么以及何时做的队列)。

setTimeout 方法有两个参数:一个函数和一个时间延迟(以毫秒为单位)。延迟可以是零毫秒，因此功能可以“立即”执行。让我们来试试:

```
let ct = 0;
const MAX = 100_000const recurse = (cb) => {
  if (++ct > MAX) {
    return cb(ct)
  }
  **setTimeout**(() => recurse(cb), 0)
}try {
  const then = process.hrtime.bigint();recurse((ct) => {
    const now = process.hrtime.bigint();
    const nanos = now - then
    const runtime = Number(nanos) / 1_000_000_000
    ct--
    console.log({ ct, runtime });
  })
} catch (e) {
  console.error({ ct, e })
}
```

这个程序输出执行时间和迭代次数。结果是:

```
{ct: 100000, runtime: 144.47516928 }
```

这避免了调用堆栈溢出，但是有点慢，你不觉得吗？

# setImmediate

尽管 setTimeout 可以将 0ms 作为第二个参数，但它没有调用 setImmediate 快:

```
let ct = 0;
const MAX = 100_000const recurse = (cb) => {
  if (++ct > MAX) {
    return cb(ct)
  }
  **setImmediate**(() => recurse(cb))
}try {
  const then = process.hrtime.bigint();recurse((ct) => {
    const now = process.hrtime.bigint();
    const nanos = now - then
    const runtime = Number(nanos) / 1_000_000_000
    ct--
    console.log({ ct, runtime });
  })
} catch (e) {
  console.error({ ct, e })
}
```

这个程序的执行时间比上一个程序快得多:

```
{ ct: 100000, runtime: 0.295789227 }
```

几分之一秒，而不是 144+！为什么？

通过 setImmediate 执行函数不像 setTimeout 那样被调度。相反，它会在所有输入/输出处理器执行完毕后立即执行。JavaScript 中的事件循环处理在其他地方已经有了很好的解释，比如这里的，这篇文章是关于避免调用堆栈溢出的。

## 下一滴答

当在 Node.js 环境中执行时，还有另一个资源可以避免堆栈溢出: **process.nextTick()。****进程**对象是一个全局对象，很像浏览器中的**窗口**对象。 **nextTick** 方法在第一时间执行函数，绕过事件消息队列。

```
let ct = 0;
const MAX = 100_000const recurse = (cb) => {
  if (++ct > MAX) {
    return cb(ct)
  }
  process.**nextTick**(() => recurse(cb))
}try {
  const then = process.hrtime.bigint();recurse((ct) => {
    const now = process.hrtime.bigint();
    const nanos = now - then
    const runtime = Number(nanos) / 1_000_000_000
    ct--
    console.log({ ct, runtime });
  })
} catch (e) {
  console.error({ ct, e })
}
```

结果是:

```
{ ct: 100000, runtime: 0.109725787 }
```

因此，在这个简单的代码示例中， **nextTick** 比 **setImmediate** 快 60–70%。

# 环

![](img/629f1767794d1560847ce20de0568179.png)

Marc Schaefer 在 [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

不使用递归，可以将递归调用转换为循环。对于到目前为止显示的琐碎示例，这是一项简单的任务。

不过，有时递归可能被嵌套得很深。假设函数 A 调用函数 B 调用函数 C，函数 C 可能再次调用函数 B*或*函数 A。现在你有一个- > B- > C- > B|A 递归，将这种类型的递归转换成循环可能不那么容易。

主循环可以通过变量调用函数，而不是直接调用函数。函数变量由每个函数调用设置，调用每个函数成为循环结构的责任:

```
let ct = 0;
const MAX = 100_000const A = () => {
  **fp** = B;
}const B = () => {
  **fp** = A;
}let **fp** = B;const then = process.hrtime.bigint();for (; ;) {if (++ct > MAX) {
    const now = process.hrtime.bigint();
    const nanos = now - then;const runtime = Number(nanos) / 1_000_000_000
    ct--
    console.log({ ct, runtime });
    break;
  }
  **fp**()
  continue;
}
```

这个循环没有任何调用函数的函数，但是每个函数都有责任通过函数变量 **fp** 知道并控制下一步执行什么函数。这产生了迄今最快的结果:

```
{ ct: 100000, runtime: 0.015974012 }
```

现在你知道了:避免可怕的堆栈溢出的四种方法。