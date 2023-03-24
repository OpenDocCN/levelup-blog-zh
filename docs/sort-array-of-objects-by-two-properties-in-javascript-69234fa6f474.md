# 根据 JavaScript 中的两个属性对对象数组进行排序

> 原文：<https://levelup.gitconnected.com/sort-array-of-objects-by-two-properties-in-javascript-69234fa6f474>

## 一个排序函数，两个排序顺序

我最近正在研究一种基于两列按字母顺序对对象数组进行排序的方法。这意味着应该保持两种级别的字母排序。请参见下面的示例。

```
var fruits = [
  {colA: 'apple', colB: 'red delicious'},
  {colA: 'pear', colB: 'red anjou'},
  {colA: 'orange', colB: 'navel'},
  {colA: 'orange', colB: 'blood'},
  {colA: 'pear', colB: 'green anjou'},
  {colA: 'apple', colB: 'granny smith'},
  {colA: 'orange', colB: 'clementine'}
]
```

上面的列表应该像下面的数组一样排序。

```
[
  {colA: "apple", colB: "granny smith"},
  {colA: "apple", colB: "red delicious"},
  {colA: "orange", colB: "blood"},
  {colA: "orange", colB: "clementine"},
  {colA: "orange", colB: "navel"},
  {colA": "pear", colB: "green anjou"},
  {colA: "pear", colB: "red anjou"}
]
```

现在，您的第一个想法可能是首先简单地按`colA`对`fruits`进行排序。然后一旦你有了一个新的排序后的数组，基于`colB`执行第二次排序。让我们看看如果我们那样做会发生什么。

```
console.log(fruits.sort((a, b)=> a.colA < b.colA ? -1 : 1).sort((a, b)=> a.colB < b.colB ? -1 : 1))> [{colA: "orange", colB: "blood"},
  {colA: "orange", colB: "clementine"},
  {colA: "apple", colB: "granny smith"},
  {colA: "pear", colB: "green anjou"},
  {colA": "orange", colB: "navel"},
  {colA: "pear", colB: "red anjou"},
  {colA: "apple", colB: "red delicious"}]
```

> 通过创建一个[中型合作伙伴计划账户](https://matt-croak.medium.com/membership)和[订阅我的电子邮件](https://matt-croak.medium.com/subscribe)，获取我所有的最新内容。:)

如您所见，该数组基于`colB`按字母顺序排列，但这忽略了基于`colA`按字母顺序排列的约束。

![](img/e28eb7bc6e86410b256932f6f508a81a.png)

那么我们应该如何先按`colA`排序，然后再按`colB`排序，而不破坏原来的排序顺序呢？

诀窍是，我们不想对排序后的列表进行重新排序。这忽略了原始的排序顺序。通过实现一个新的排序函数，你建立了一个完全不同的排序顺序，而不是和现有的排序顺序一致。所以我们需要做的是让**有一个**排序函数考虑**和**两个约束。我们可以这样做。

首先，检查第一个(`a.colA < b.colA`)排序约束。我们需要检查这两个属性实际上是否不同。如果它们不同，则执行排序。如果不是，那么我们就不能根据第一个约束对它们进行排序。

这个是我们将利用第二个比较点(`a.colB < b.colB`)的时候。通过这种方式，我们比较数组中具有相同`colA`(意味着已经处于正确顺序)的条目，然后在当前顺序中执行第二次排序。参见下面的代码。

```
console.log(fruits.sort((a, b)=> {
  if (a.colA === b.colA){
    return a.colB < b.colB ? -1 : 1
  } else {
    return a.colA < b.colA ? -1 : 1
  }
}))> [{colA: "apple", colB: "granny smith"},
  {colA: "apple", colB: "red delicious"},
  {colA: "orange", colB: "blood"},
  {colA: "orange", colB: "clementine"},
  {colA: "orange", colB: "navel"},
  {colA": "pear", colB: "green anjou"},
  {colA: "pear", colB: "red anjou"}]
```

![](img/e53efbc644923554cb814c49ca61bf4a.png)

给你。通过检查是否满足第一个排序约束，如果满足，则执行第二个，我们可以很容易地对列表进行排序，以同时适应第一个和第二个约束。

点击此处，将你的免费媒体会员升级为付费会员，每月只需 5 美元，你就可以获得数千位作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！