# 奥卡姆剃刀:保持代码简短

> 原文：<https://levelup.gitconnected.com/occams-razor-keeping-the-code-short-and-simple-7ce4b60f87d4>

![](img/70999b59e1844c49f81f597c49c2f41d.png)

# 背景

在我的组随机数发生器应用程序中，我的 MVP(最小可行积)的一个特性是创建一个保存组特性。当用户点击“保存组”按钮时，它会将当前选项列表保存为一个新组。但是，此功能的核心功能是，如果它找到具有相同选项的现有已保存组，不管顺序如何，都会出现一个警告，说明具有您当前选项列表的组已经存在。

例如，保存的组#1 具有人员列表，“Sam”和“Bob”。如果我的当前选项列表是“Bob”和“Sam ”,并且我试图将其保存为一个新组，那么将弹出一个警告，说明“具有您的当前选项列表的已保存组已经存在”。它是保存组#1”。

在这篇博客中，我将讲述我是如何创建算法来创建这个检查功能和奥卡姆剃刀的应用的。如果你不熟悉奥卡姆剃刀，维基百科的定义是:

> **奥卡姆剃刀……**是解题[原理](https://en.wikipedia.org/wiki/Principle)说的是“实体没有必要就不要相乘。”

# 校验分组算法的首次尝试

下面的代码是我第一次尝试检查组算法。

```
function checkGroups(savedGroup, currentListOfOptions) { savedGroupHash = {} // populates the savedGroupHash based on the contents of the   savedGroup array parameter for (let i = 0; i < savedGroup.length; i++){
    savedGroupHash[savedGroup[i]] = true
  } // checks the savedGroupHash against the current list of options to check if savedGroupHash contains all of the list of options for (let j = 0; j < currentListOfOptions.length; j++) {
    if (!savedGroupHash.hasOwnProperty(currentListOfOptions[j])) {
      return false
    }
  }

  return true
}
```

注意:这个算法的参数都是数组。每当下面提到“选项”时，它指的是它们各自数组中的元素。

为了对我如何处理这个算法有一个全面的了解，我创建了两个 for 循环。第一个`for`循环根据`savedGroup`参数的内容填充了`savedGroupHash`。对于键-值对，键是选项，默认值为`true`。该值(或缺少)将在第二个 for 循环中用作布尔值。在第二个 for 循环中，我对照`savedGroupHash`检查了`currentListOfOptions`参数中的每个选项。一旦`currentListOfOptions`中的一个选项没有包含在`savedGroupHash`中，我将返回`false`，这将立即退出该函数。如果没有，那么最后一行将返回`true` ，因为它没有从第二个 for 循环返回`false`。

# 测试检验组算法的第一次尝试

基于我在上一篇博客中正确测试的经验，我决定创建一些测试用例来彻底测试我的算法。在这个案例中，我想到了三种情况。

1.`savedGroup`和`currentListOfOptions`实际上有完全相同的选项，但顺序不同。这应该归`true`。
2。`savedGroup`包括与`currentListOfOptions`相同的选项，但比`currentListOfOptions`多。这应该会返回`false`。
3。`currentListOfOptions`包括与`savedGroup`相同的选项，但选项比`savedGroup`多。这应该会返回`false`。

第一种情况，我们用`savedGroup = [“Sam, “Bob”]`和`currentListOfOptions = [“Bob”, “Sam”]`。接下来，让我们将这两个变量作为参数输入到算法中，并在控制台记录返回值。

```
const savedGroup = ["Sam", "Bob"]
const currentListOfOptions = ["Bob", "Sam"]console.log(checkGroups(savedGroup, currentListOfOptions)) //true
```

看起来这通过了我们的第一个测试。让我们继续第二个测试案例。

第二种情况，我们用`savedGroup = [“Sam”, “Bob”, “Jane”]`和`currentListOfOptions = [“Bob”, “Sam”]`。接下来，让我们将这两个变量输入到算法中，并在控制台记录返回值。

```
const savedGroup = ["Sam", "Bob", "Jane"]
const currentListOfOptions = ["Bob", "Sam"]console.log(checkGroups(savedGroup, currentListOfOptions)) //true
```

看起来这没有通过我们的第二个测试。让我们继续第三个测试案例。

第三种情况，我们用`savedGroup = [“Sam”, “Bob”]`和`currentListOfOptions = [“Bob”, “Sam”, “Jane”]`。接下来，让我们将这两个变量输入到算法中，并在控制台记录返回值。

```
const savedGroup = ["Sam", "Bob"]
const currentListOfOptions = ["Bob", "Sam", "Jane"]console.log(checkGroups(savedGroup, currentListOfOptions)) //false
```

看起来这通过了我们的第三个测试。

嗯……看起来这个算法并没有像预期的那样工作。在分析了我的代码之后，看起来每个参数的长度差异可能会导致代码失效。如果是这种情况，让我们改变算法来适应这一点。

# 检验组算法的第二次尝试

下面的代码是我第二次尝试检查组算法。

```
function checkGroups(savedGroup, currentListOfOptions) { const savedGroupHash = {} for (let i = 0; i < savedGroup.length; i++) {
    savedGroupHash[savedGroup[i]] = 1
  } for (let j = 0; j < currentListOfOptions.length; j++) {
    if (savedGroupHash.hasOwnProperty(currentListOfOptions[j])) {
      savedGroupHash[currentListOfOptions[j]] -= 1
    } else {
      savedGroupHash[currentListOfOptions[j]] = 1
    }
  }

  let sum = Object.values(savedGroupHash).reduce( (acc, current) => acc += current) if (sum === 0) {
    //this means the same group already exists
    return true
  } else {
    //this means the group does not already exist
    return false
  }}
```

为了提供该算法的概述，有两个 for 循环、一个累加器和一个 if/else 语句。第一个 for 循环与第一个算法中的第一个 for 循环做的事情几乎相同，但它提供的是默认值 1。第二个 for 循环操作`currentListOfOptions`中每个选项的`savedGroupHash`。如果选项在`savedGroupHash`中，将值减 1，这将导致 0，如果不是，则添加一个新的键-值对，其中键是选项本身，值是 1，类似于第一个 for 循环。

接下来，累加器将对`savedGroupHash`中的所有值求和。`sum`的目的是帮助确定`savedGroup`数组和`currentListOfOptions`数组之间是否存在长度差异。我在 if/else 语句的条件比较中使用了`sum`，以便返回`true`或`false`。

现在是最伤脑筋的部分:测试这个新算法。

# 测试检验组算法的第二次尝试

为了保持一致性，我将使用与第一次测试算法时相同的测试用例。为了简洁起见，我还将结合所有三个测试用例的代码和结果。

```
//Test Case #1
const savedGroup = ["Sam", "Bob"]
const currentListOfOptions = ["Bob", "Sam"]console.log(checkGroups(savedGroup, currentListOfOptions)) //true//Test Case #2
const savedGroup = ["Sam", "Bob", "Jane"]
const currentListOfOptions = ["Bob", "Sam"]console.log(checkGroups(savedGroup, currentListOfOptions)) //false//Test Case #3
const savedGroup = ["Sam", "Bob"]
const currentListOfOptions = ["Bob", "Sam", "Jane"]console.log(checkGroups(savedGroup, currentListOfOptions)) //false
```

是啊！看起来算法现在正在按预期工作！我们终于可以收工了，但是…

# 奥卡姆剃刀

第二天，当我在开发分组随机数发生器应用程序时，我又看了一眼检查分组算法。我一直对这个算法的成功咧嘴笑着(因为我花了 1-2 个小时来开发它)，直到我意识到第二个算法完全是多余的。我这么说是什么意思？

让我们再来看看测试用例，以及为什么我改变了算法的第一次尝试。测试失败的根本原因的最初假设是每个参数的数组长度(如测试案例#2 所示)。在算法的第二次尝试中，我操纵了`savedGroupHash`散列，以便跟踪`savedGroup`和`currentListOfOptions`之间的数组长度差异。`sum`是数组长度之差。如果`sum`为 0，那么数组长度相同，返回`true`。任何其他值都将返回`false`。

从另一个角度来看，我实际上是在检查`savedGroup`和`currentListOfOptions`是否长度相同。因为我知道这两个参数是数组类型，所以我可以对每个参数调用`.length`来确认它们的长度是否相等。如果这些长度不同，我可以立即返回`false`。下面的代码代表了这个实现。

```
if (savedGroup.length !== currentListOfOptions.length) {
  return false
}
```

我现在可以用上面的代码修改算法的第一次尝试。

```
function checkGroups(savedGroup, currentListOfOptions) { //insert new code here
  if (savedGroup.length !== currentListOfOptions.length) {
    return false
  } savedGroupHash = {} for (let i = 0; i < savedGroup.length; i++){
    savedGroupHash[savedGroup[i]] = true
  } for (let j = 0; j < currentListOfOptions.length; j++) {
    if (!savedGroupHash.hasOwnProperty(currentListOfOptions[j])) {
      return false
    }
  }

  return true
}
```

接下来你可能会猜到，我用这个算法运行了所有三个测试用例，并且通过了所有三个测试用例！

我能够简化检查数组长度的代码

```
for (let j = 0; j < currentListOfOptions.length; j++) {
    if (savedGroupHash.hasOwnProperty(currentListOfOptions[j])) {
      savedGroupHash[currentListOfOptions[j]] -= 1
    } else {
      savedGroupHash[currentListOfOptions[j]] = 1
    }
  }

  let sum = Object.values(savedGroupHash).reduce( (acc, current) => acc += current)if (sum === 0) {
    //this means the same group already exists
    return true
  } else {
    //this means the group does not already exist
    return false
  }}
```

到

```
if (savedGroup.length !== currentListOfOptions.length) {
  return false
}
```

# 检查组算法的第一次尝试中的意外结果

当我在第一个未修改的算法和第二个算法之间进行测试时，我意识到第一个未修改的算法实际上在做什么。虽然它没有达到我的目的，即检查组是否相同，但它检查了一个组`currentListOfOptions`是否是另一个组`savedGroup`的*子集*。我无意中创建了一个检查子集的算法，而不是检查整个集合。现在，如果我将来需要检查子集，我不需要从头开始重新创建算法，并利用第一个未修改的算法。

# 关键要点

在创建这个算法的时候，我学到了两个主要的经验。

1.  **不要让你的想法过于复杂，把自己陷入兔子洞。**

在算法的第二次尝试中，我一直在脑海中思考这个算法一定包含某种复杂的解决方案，因为这不是一个共同的特征(这可能不是真的)。正如你所看到的，我用迂回的方式解决了这个 bug，而这本来可以用三行代码来完成(如果我不使用花括号，就用一行代码)。如果你开始认为你的解决方案开始变得复杂，后退一步，重新评估你试图实现的目标，看看是否可以用一种不同的方式来表达，以获得可能更好的方法。

2.总体来说，研究算法确实有所帮助。

像许多有抱负的软件开发人员一样，我会在 LeetCode 这样的网站上学习不同的数据结构和算法，并练习求解算法。起初，我真的不明白学习这些算法的目的，因为我觉得我永远不会在技术面试之外应用这些算法。然而，在我第一次尝试创建校验组算法时，我直观地使用了一个散列来进行比较。当我思考我为什么这样做时，我突然明白了，这是因为使用哈希是解决 LeetCode 算法的一种常见方法，可以将时间复杂度从 O(n)提高到 O(n)。我现在看到，我研究的算法可能在我最不期望的时候适用于我的代码。