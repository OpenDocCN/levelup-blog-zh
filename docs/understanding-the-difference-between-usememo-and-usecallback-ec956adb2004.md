# 理解 useMemo 和 useCallback 之间的区别

> 原文：<https://levelup.gitconnected.com/understanding-the-difference-between-usememo-and-usecallback-ec956adb2004>

React 库为我们提供了两个内置的钩子来优化我们的应用程序的性能:useMemo & useCallback。乍一看，它们的用法似乎非常相似，所以对于何时使用它们可能会感到困惑。为了消除这种困惑，让我们深入了解实际的区别以及使用它们的正确方法。

![](img/1b20c6c5c107ca45ab94483d5823f050.png)

useMemo 还是 useCallback？

# 功能组件有问题

功能组件很棒。与类组件相比，它们与钩子的组合允许更高的代码可重用性和灵活性。然而，它们有一个问题:一个函数组件和我们以前在类组件中使用的渲染函数是一样的。这是一个在任何属性/状态改变时重新运行的函数。

**意思是:**

A.如果一个函数在组件内部被调用，它将在每次重新渲染时被一次又一次地重新计算。

B.如果在组件内部创建的函数被传递给子组件，它将被重新创建，这意味着指针将发生变化，导致子组件不必要地重新呈现或调用该函数(取决于具体情况)。

为了解决这个问题并防止可能的性能问题，React 为我们提供了两个钩子:useMemo 和 useCallback。

# 使用备忘录

让我们从第一个问题开始，看看如何避免不必要的函数求值。

在下面的演示中，我们有一个具有两种状态的组件:一个存储数字，另一个存储布尔值。
我们需要对我们状态中的数字进行一些计算，所以我们调用我们的`plusFive`函数并呈现结果。

```
**const plusFive = (num) => {
  console.log("I was called!");
  return num + 5;
};**export default function App() {
 **const [num, setNum] = useState(0);
  const [light, setLight] = useState(true);
  const numPlusFive = plusFive(num);**
  return (
  ...
```

不使用备忘录演示

如果你打开控制台，你会看到无论我们点击“更新数字”设置一个新的数字，还是“切换灯”更新布尔状态(与 numPlusFive 无关)，都会调用`plusFive`。

那么如何才能防止这种情况发生呢？通过纪念`plusFive`！
在我们收到新号码之前，该功能不会被调用。计算被跳过，我们将立即收到结果。

我们将通过使用`useMemo`来做到这一点，它将看起来像这样:

```
const numPlusFive = useMemo(() => plusFive(num), [num]);
```

useMemo 接收两个参数:一个返回函数调用的函数，以及一个依赖项数组。只有当其中一个依赖关系改变时，我们的函数才会被再次调用。useMemo 返回该函数执行的结果，并将它存储在内存中，以防止该函数在使用相同参数的情况下再次运行。

自己去看看吧(别忘了打开控制台):

使用备忘录演示

# 使用回调

既然我们知道了如何防止重新计算函数，让我们看看如何防止在组件内部创建的函数在每次渲染时被重新创建。

在下面的演示中，我们有一个子组件(<somecomp>)，它接收我们在父组件(<app>)中创建的一个函数作为道具。注意，这个函数是在 useEffect 钩子内部使用的，因为它被列为 useEffect 的依赖项，所以它将被再次调用。是的，甚至当我们改变其他状态或收到道具时，与我们的“plusFive”功能无关。</app></somecomp>

```
const App = () => {
 **const [num, setNum] = useState(0);
  const [light, setLight] = useState(true);
  const plusFive = () => {
    console.log("I was called!");
    return num + 5;
  };**
  return (
    <div className={light ? "light" : "dark"}>
      <div>
        **<SomeComp someFunc={plusFive} />**
  <button onClick={() => { setLight(!light); }}> Toggle the light </button>
      </div>
    </div>
  );
}const SomeComp = ({ **someFunc** }) => {
  const [calcNum, setCalcNum] = useState(0);
  useEffect(() => {
    // In this scenatio, someFunc will change on every render, so this useEffect will run.
     **setCalcNum(someFunc());**
  }, [**someFunc**]);return <span> Plus five: {calcNum}</span>;
};
```

不使用回调

为了防止我们的函数被重新创建并在每轮渲染中改变指针，我们可以使用`useCallback`。这个 React 挂钩接收两个参数:一个函数和一个依赖关系数组:

```
const plusFive = useCallback(() => {
  console.log("I was called!");
  return num + 5;
}, [num]);
```

这个钩子让我们保留这个函数，只有当它的一个依赖项改变时，它才会被重新创建。

这是工作演示:

使用回调

# 但是等等！不要误用这些钩子！

虽然这两个挂钩为实际问题提供了解决方案，但它们可能很容易被误用，甚至会造成更大的伤害。

例如，没有必要记住一个正在做一些基本计算的函数(就像演示中那样)。只有当你试图避免重新运行昂贵的函数时，才使用`useMemo`,这些函数会运行很多时间，或者使用很多资源。为什么？因为 useMemo 将函数执行的结果保存在内存中，它可能会变得很大，具有讽刺意味的是会损害你的应用程序的性能。

使用`useCallback`,事情可能会变得更糟:当不使用 useCallback 时，旧函数将被垃圾收集，但使用 useCallback 时，它将留在内存中，以防某个依赖项再次正确返回旧函数版本。

那么什么时候用`useCallback`合适呢？当您实际看到不使用它会损害您的性能，或者会导致不必要的繁重函数执行时(想象一下在 useCallback 演示中，这个函数执行一个 API 调用，而不仅仅是添加数字。这是值得防范的事情)。

# 总结一下:

**useMemo** 如果一个函数没有收到之前使用过的一组参数，则阻止该函数再次执行。它返回函数的结果。当您希望防止在每次渲染时调用一些繁重或高成本的操作时，请使用该选项。

**useCallback** 根据依赖列表，防止函数再次被重新创建。它返回函数本身。当您想要将它传播到子组件，并防止一个昂贵的函数重新运行时，请使用它。