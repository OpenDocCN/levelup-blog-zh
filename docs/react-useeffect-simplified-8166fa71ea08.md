# 反应:使用简化的效果

> 原文：<https://levelup.gitconnected.com/react-useeffect-simplified-8166fa71ea08>

## 功能生命周期和可变观察

![](img/558230ad0b8f838dde8ae0356c959fdc.png)

得出了[功能组件更好](https://medium.com/javascript-in-plain-english/functional-components-are-better-d6a889175b67)的结论后，让我们明确一点:钩子使得函数像类一样功能齐全。钩子以一种逐渐被接受的方式运送货物。

当你需要生命周期回调时，你可以使用 useEffect。

前缀“use”是 React 钩子的约定。这个想法是，这个钩子将允许我们分享“副作用”。

在函数式编程中，纯函数是只接收参数并返回值的函数。在函数本身之外引起变化——或者依赖非参数输入——被称为伴随*副作用*。

好吧，也许不是合伙，但是总的想法是纯函数对于它们的 IO 和依赖关系是清楚的，并且有助于保持代码可理解。

但是有时候，你*需要*副作用。

useEffect 的基本目的是允许 React 的渲染引擎到达我们的函数内部并启动一些动作，以产生一些效果。

使用 Effect 有四个基本功能:

*   当组件呈现时做一些事情
*   当组件呈现时做一些事情——但只是第一次
*   当一个特定的变量更新时做一些事情
*   当组件卸载时执行一些操作，例如清理

所有这四个都是通过相同的语法实现的:导入 useEffect，然后用一个函数作为第一个参数来调用它。如果它返回一个函数，它就清除副作用。如果有第二个参数，它是一个数组，指定要观察哪些变量来触发效果—如果数组为空，则仅在初始渲染时调用该函数。

像这样:

```
import React, { useEffect } from “react”;
useEffect(() => { 
  /* do work */
  /*optional cleanup */ return () => {} 
)}, /*optional*/ [/*0-N array members])
```

我们可以盯着它看一会儿。它包含了类生命周期钩子给我们的一切，没有混淆 didMount、willMount 和 isThinkingAboutMounting。

# 在初始渲染时运行一次

假设您想在组件第一次呈现时运行一些东西。在本例中，我们将开始一个时间间隔—它可以是服务订阅或诸如此类的内容。

```
useEffect(() => {
  setInterval(function(){
    console.info("Another second has slipped into the past.");    
    // This code contains a flaw! See the clean-up version below
 },1000);
}, []);
```

所以，上面的代码并不复杂，但是它包含了 useEffect 动物中最神秘的部分:作为第二个参数的空数组。

这告诉效果只在第一次渲染时运行它*。如果第二个参数根本不存在，那么 react 将在每次渲染时调用效果。也就是说，即使在滚动输入或与输入交互并检测到需要部分渲染时。*

# 打扫

正如评论中提到的:这个效果需要清理。想象一下，如果用户离开组件，然后返回。间隔可以很容易地仍然活着，你可以产生大量的他们。这可不好。

```
useEffect(() => {
  const interval = setInterval(function(){
    console.info("Another second has slipped into the past.");    
 },1000);
 return () => {
   clearInterval(interval);
 }
}, []);
```

叹气。有时候，在一个程序员的生活中，事情就这样走到了一起，在一个美好的时刻，简单和有用的东西对齐了。看上面就是其中之一。

多亏了 JavaScript 的词法作用域和 useEffect 语法，我们可以在一个地方定义一切:时间、内容和清理。

# 瞄准(观察)一个变量

现在，我们可能只想在某个值被更新时执行我们的效果。比方说，每当功能组件的属性发生变化时，我们都希望执行一个操作。

```
const MyComponent = (props) => {
  useEffect(() => {
    console.info("OK, it was updated: " + props.anImportantVar);
  }, [props.anImportantVar]);
}
```

事实上，这是一个相当强大的反应性安排，被压缩到一个小语法中。您可以从中获得一些非常强大的基于事件的组件行为。

您可以将此视为一种挂钩到反应式引擎并导致您需要的任何额外行为的方式。(当然，直接修改属性值除外。)

结合[功能道具](https://medium.com/javascript-in-plain-english/react-dead-simple-component-communication-4582c0cb18c1)你可以连接一些非常干净和强大的组件间反应行为。

相同的变量监视可以应用于通过 useState 钩子管理的状态。

当使用变量监视特性时，请记住，您需要包含函数使用的所有变量*，否则它可能会对过时的值进行操作。*