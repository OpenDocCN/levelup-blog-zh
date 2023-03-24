# 我的第一个有限状态机

> 原文：<https://levelup.gitconnected.com/my-first-finite-state-machine-6140a91cfef3>

![](img/b76c040abd863dc636bb41400f076cf7.png)

> 首先，我希望每个人在疫情期间都平安无事！

鉴于目前的情况，我参加了几个群聊，其中包括用表情符号描述电影名称的测验。我认为这很有趣，但我看到的是取决于小组的规模和人们有多空闲，人们通常会在小组聊天中回复答案，这意味着其他人没有机会在没有看到答案的情况下完成整个测验，这也意味着你经常会忘记问题。

我认为为此在网上创建一个简单的问答游戏会很有趣😀。

我开发了一个 Nuxt 应用程序，创建了一个 Firebase 项目，这样我就可以使用托管和 Firestore 服务，然后我开始画出我的简单想法(可以在网上看到[这里](https://quizzes.mattchaffe.uk))。

# 有限状态机

我已经通过同事和 Twitterverse 听说了很多关于 FSM 的事情，我认为这将是一个尝试的好机会。

# 什么是有限状态机

让我们从头开始，看看什么是 FSM 以及它们如何有用。FSM 是一种描述有限数量的状态的方法，在这些状态下，应用程序在任何给定时间都可以处于其中一种状态。这意味着我可以描述我的应用程序可能处于的所有状态，并且我可以确定它只处于这些状态中的一种。

# 我是如何创建 FSM 的

我从一个非常粗糙的游戏组件开始，它看起来像下面这样:

```
<template> <main> <Quiz v-if="!completed" :questions="questions" /> <Results v-if="completed && answered" :results="results" /> </main></template>
```

这可能看起来不太疯狂，因为这个应用程序相当简单，但它确实允许应用程序处于这样一种状态，如果游戏完成了，但没有答案，用户将什么也看不到。当然，这很容易解决，但是这一点也不好玩，我想玩 FSM😜。

我决定使用 [xstate](https://xstate.js.org/docs/) 创建我的第一个状态机，状态机看起来如下:

```
import { Machine, assign } from 'xstate' const gameMachine = Machine({ id: 'game', initial: 'play', context: { answered: 0, guesses: {} }, states: { play: { on: { RESOLVE: { target: 'results', actions: assign({ answered: (context, event) => event.answered, guesses: (context, event) => event.guesses }) } } }, results: { on: { RESET: 'play' }, initial: 'hide', states: { hide: { on: { TOGGLE: 'show' } }, show: { on: { TOGGLE: 'hide' } } } } }})
```

*我在开发时发现非常有用的是* [*在线可视化工具*](https://xstate.js.org/viz/) *，它允许我切换所有状态并查看它的运行情况。*

分解一下，我有两个状态`play`或`results`，在`results`中有*两个*子状态，允许用户显示/隐藏答案。这很简单。`play`状态中的动作允许我向`gameMachine`发送数据，这样它就可以保存额外的上下文。我的`GameComponent`现在看起来像:

```
<main> <component :is="currentComponent" :questions="questions" :answers="answers" :answered="context.answered" :guesses="context.guesses" :show-answers="current.matches('results.show')" @answer="validate" @reset="send('RESET')" @toggleAnswers="send('TOGGLE')" /></main>
```

我使用 Vue 的动态组件来呈现基于机器状态的组件，组件`$emit`到父组件，然后父组件发送信号到`gameMachine`来指示它移动到下一个状态。这意味着我的游戏永远不能处于什么都看不见的状态！

# 使用带 Vue 的 FSM

[xstate](https://xstate.js.org/docs/) 提供了一种简单的方法来使用 Vue 的状态机，那就是使用一个[解释器](https://xstate.js.org/docs/guides/interpretation.html#interpreting-machines)。它处理状态转换、执行事件等等。我建议阅读文档，了解它的全部功能。

要在 Vue / Nuxt 中使用它，您需要在您的组件中导入或创建您的机器(建议您将您的机器放在组件之外，以便它们可以在与组件隔离的情况下共享/测试)，然后启动机器并监听任何状态转换，以便您可以更新状态。要理解的内容太多了，让我们来看一些代码:

```
<template> <div> My Current State {{ current.value }} <button @click="toggle()"> </div></template><script>import { Machine, interpret } from 'xstate'const demoMachine = Machine({ id: 'demo', initial: 'hello', context: { answered: 0, guesses: {} }, states: { hello: { on: { TOGGLE: 'goodbye' } }, goodbye: { on: { TOGGLE: 'hello' } } }}) export default { data: () => ({ demoMachine: interpret(demoMachine), current: demoMachine.initialState }), created () { // Start service on component creation this.demoMachine .onTransition((state) => { // Update the current state component data property with the next state this.current = state }) .start() }, methods: { toggle () { this.demoMachine.send('TOGGLE') } }}</script>
```

分解一下，我们可以看到我们将解释的机器设置到组件的数据以及机器的初始状态。在`created`生命周期挂钩中，我们`start`解释机器并监听转换，在转换变更的回调中，我们更新来自机器的组件状态。

感谢阅读！

*最初发布于*[*https://mattchaffe . uk*](https://mattchaffe.uk/posts/my-first-state-machine)*。*