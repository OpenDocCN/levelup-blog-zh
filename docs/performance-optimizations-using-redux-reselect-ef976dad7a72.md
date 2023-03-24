# 使用 Redux 重选进行性能优化

> 原文：<https://levelup.gitconnected.com/performance-optimizations-using-redux-reselect-ef976dad7a72>

![](img/9aeee8b78c20888543de050545ba3882.png)

这篇博客的目的是解释为什么需要 npm 包`reselect`来优化 React/Redux 应用程序的性能。

## 假设:

您熟悉一般的 React 和 Redux 架构。

## 问题:

假设您有一个用于 React 组件的 mapStateToProps **(MSTP)** 函数。

```
mapStateToProps = (state, ownProps) = {
 const people = state.peopleReducer.people.filter(person =>     ownProps.neededPeople.includes(person))
 return { people }
}
```

在这个 MSTP 函数中，我们将一组人存储在某个地方的 reducer 中，任意过滤这些人。乍一看，这个功能似乎还可以。但是，这个函数可能会成为 UI 性能下降的潜在原因！

每次 Redux 中的 reducer 有更新时，每个挂载的 MSTP 函数都会运行。如果 MSTP 函数检测到它返回的值发生了变化，它将触发重新呈现。在上面的例子中，每次 MSTP 函数触发时，我们执行的过滤函数将创建一个全新的数组对象，从而导致应用程序中不必要的重新呈现。

现在想象一下，被过滤的人的数组由数千或数百万个对象组成。这段代码会让你的 UI 超级慢！那么我们如何解决这个问题呢？

# 我们需要创建一个选择器！

当我们创建选择器函数时，我们的 MSTP 中的过滤器逻辑如下所示:

```
const people = filterPeople(state, ownProps);
```

在一个名为 `peopleSelector.js`的文件中，我将编写选择器逻辑，并将它导入到包含低效 MSTP 函数的组件文件中。

在我们下载了`reselect`包之后，我们将像这样在我们的选择器文件中导入`createSelector`。

```
import { createSelector } from ‘reselect’;
```

这将允许我们创建一个函数，它将函数列表作为第一个参数，将另一个函数作为第二个参数。

```
const filterPeople = createSelector(
 [getPeople, getNeededPeople], (people, neededPeople) => {
 return people.filter(person => neededPeople.includes(person))
 }
)
```

**功能列表**

我通常在我打算声明的选择器函数上面声明这些函数。当我们调用函数时，它们将从传递给函数的状态和属性中提取所需的数据。

```
const getPeople = (state) => state.peopleReducer.people;const getNeededPeople = (_, props) => props.neededPeople;
```

第二个参数是一个函数，它接受我们作为第一个参数传入的函数列表的返回值。

createSelector 函数监视我们传入的第一个函数列表的返回值。假设它们与前面调用 MSTP 中的选择器函数时返回的值相同。在这种情况下，这个选择器函数将返回一个由第二个参数函数返回的记忆值。

如果函数数组中的任何返回值与先前返回的不同，那么第二个参数函数将根据需要重新计算选择器返回值。

**注意**:我强烈建议你在 MSTP 函数中有创建新数组或对象(过滤、映射、排序等)的逻辑时，创建一个选择器函数。).

一旦我们声明了这个函数，我们就可以将它导入到需要它的文件中，并在我们的 MSTP 函数中使用它。

```
mapStateToProps = (state, ownProps) = {
 const people = filterPeople(state, ownProps)
 return { people }
}
```

性能优化万岁！

# 资源:

[](https://github.com/reduxjs/reselect) [## GitHub-reduxjs/reseselect:Redux 的选择器库

### 一个用于创建记忆“选择器”函数的库。通常与 Redux 一起使用，但也可用于任何普通的 JS 不可变…

github.com](https://github.com/reduxjs/reselect) [](https://redux.js.org/) [## redux-JavaScript 应用程序的可预测状态容器。|还原

### JS Apps Redux 的可预测状态容器帮助您编写行为一致的应用程序，运行在不同的…

redux.js.org](https://redux.js.org/)