# 每时每刻都在变化——如何改变、为什么改变以及如何改变

> 原文：<https://levelup.gitconnected.com/changing-from-moment-to-day-js-how-why-and-fixing-vue-chartjs-786314ef4697>

![](img/23523bef806a344273a70155f86baf8f.png)

由 [Fabrizio Verrecchia](https://unsplash.com/@fabrizioverrecchia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/clocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

moment.js 是一个非常棒的日期/时间库，在过去十年里，我几乎在我参与的每个项目中都使用过它。可悲的是，随着创作者们不鼓励它在新项目中的使用，以及项目被置于只维护模式，它已经显示出它的年龄了。

我能理解为什么 JavaScript 库的工具和结构在最近几年发展迅速，旧的库如果不进行大规模的、向后不兼容的修改，通常就跟不上。网络本身也发生了变化——IE 的祸害几乎消失了，几乎所有东西都慢慢地由 Webkit 及其变体驱动。有很多向后兼容性已经不再需要了，从某种意义上来说，重写会变得更加明智。

那么，这为什么会让你烦恼呢——你应该为转换而烦恼吗？如果你想减少你的包的大小，我会说这是值得考虑的，因为与新一代的轻量级数据操作库相比，moment 相当重。由于 [Day.js](https://day.js.org/) 试图保持一个与 moment.js】几乎相同的 API，即使使用复杂的现有代码库也很容易做到。

在两天之间交换时，你只需要知道两件事——js 使用核心+插件系统，所以根据你当前使用的格式字符串和特性，你需要注册适当的插件。例如，我需要`.weekday()`，所以我注册了`weekday`插件:

```
import dayjs from 'dayjs'
import weekday from 'dayjs/plugin/weekday'dayjs.extend(weekday)
```

当你完成的时候，你可能会得到一些这样的插件！你需要知道的第二件事是 Day.js 对象是不可变的——所以像这样的东西:

```
var foo = moment()
foo.add(1, 'day')
```

将改为:

```
var foo = moment()
foo = foo.add(1, 'day')
```

一旦你注册了正确的插件，并且习惯了不可变的对象，这本质上是相同的。一些发现和替换，你可以给自己一个鼓励！除非你用的是 Chart.js 或者 vue-chartjs，在这种情况下…

## 强制 Chart.js 也使用 Day.js

那么 vue-chartjs 在哪里呢？事实证明，Chart.js 实际上默认捆绑了 moment.js，当然 vue-chartjs 也使用 Chart.js。因此，我们需要做到以下几点:

*   告诉 Chart.js 停止捆绑 moment.js
*   添加一个日期/时间适配器，以便 Chart.js 使用 Day.js
*   确保我们在不干扰代码分割的情况下完成上述

**最后一部分可能有点棘手，但却至关重要——毕竟，我们开始这个过程是为了寻找减少捆绑包的方法！如果你只是简单地在你的入口文件中导入 dayjs 和 Chart.js，并开始摆弄插件和适配器，代码分割系统将不再能够把它拉出到它自己的块中。这对 Day.js 来说没多大关系，因为你可能会在任何地方使用它，而且它也很小——但 Chart.js 完全是另一回事。**

**对我来说最有效的方法是包装底层库并传递导出。所以对于 Day.js，我有一个文件——`src/dayjs.js`——看起来像这样:**

**非常简单——导入库，配置它，然后再次导出它。现在在我的代码库中，我简单地用`import dayjs from '@/dayjs'`代替了`import dayjs from 'dayjs'`。这确保了在任何地方都可以获得相同的插件，并且代码仍然可以根据需要被分割成块。**

**我们还需要包装 vue-chartjs 来告诉它如何使用 Day.js 而不是 moment.js，npm 上有一些库声称可以做到这一点，但我运气更好地适应了(哈哈！)[这里的适配器](https://gitlab.com/mmillerbkg/chartjs-adapter-dayjs/-/blob/master/src/index.js)也用我的 wrapped Day.js 代替。这次我们将包装 vue-chartjs，因为这是我们最终要导入的内容(这也是代码分割将要使用的内容):**

**最后，我们需要停止将 moment.js 默认绑定到 Chart.js 中。你可以通过更新你的 webpack 配置来实现，例如在`vue.config.js`中:**

```
module.exports = {
  configureWebpack: {
    externals: {
      moment: 'moment'
    }
  }
}
```

**这让我们可以对 webpack 撒谎说，那个时刻已经存在于页面外部，所以它不会试图导入它。当然不会真的存在，但是反正也没什么会用，所以..任务完成了？**

**一旦所有这些都完成了——取决于您使用的 Day.js 插件的数量以及您之前是否设置了删除 moment.js 语言环境的规则——您应该会看到代码库的解析大小在**45-250 kb 之间减少了**。不算太寒酸！**