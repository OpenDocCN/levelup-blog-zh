# 最简单的状态管理工具

> 原文：<https://levelup.gitconnected.com/hookstate-the-simplest-state-management-tool-b02f7d3b01a4>

*小的、最小的、干净的、可扩展的、* **和*基于钩子的*** *状态管理库*

![](img/29020b4138ffd321bd4dba689d479de6.png)

过去，Redux 是 React 开发人员状态管理的首选方法。Redux 样板代码和复杂性使得开发人员开始转向简单的解决方案，如 [React Context API](https://reactjs.org/docs/context.html) 、 [React-Query](https://react-query.tanstack.com/) 、[反冲](https://recoiljs.org/)、 [Zustand](https://zustand.surge.sh/) 、 [Jo-tai](https://github.com/pmndrs/jotai) 。它们都有自己的方式，以一种轻量级且不太复杂的方式来处理状态管理。

**Hookstate** 是一个状态管理工具，它简化了状态管理，没有动作或减少器，可以自由地改变应用程序状态，同时保持不变性概念。这篇文章让我们看到这个新的竞争者如何在国家管理领域发挥作用。

# 什么是 Hookstate？

Hookstate 是一个基于 React 和 Hooks 的小型快速灵活的状态管理工具。Hookstate 简单易学，具有实用灵活的 API。尽管 Hookstate 很小，但它有一些很好的开发人员友好的特性。Hookstate 是一个 typescript 优先库，因此 Hookstate 完全支持 IntelliSense，非常适合任何复杂的托管状态数据结构。它还支持增强开发者体验的插件。Hookstate 遵循 React 和 Hooks 的步骤。

# 是什么让它独一无二？

与其他状态管理工具不同，Hookstate 速度更快，性能更好，这是因为它采用了独特的方法来跟踪使用/呈现和更新的状态段。Hookstate 引入了一些状态概念，比如

## [地方州](https://hookstate.js.org/docs/local-state)

Hookstate 中的本地状态类似于 React 中的 useState。它最适合只处理一个组件，也许是它的子组件。创建状态的 API 函数是类似 React.useState 的 useState，但它内置了更多功能。

```
*const* countState = **useState**(0);
*const* userState = **useState**({name:'',power:'',description:''});
```

## [全局状态](https://hookstate.js.org/docs/global-state)

这种状态类似于 Redux、Zustand 等等中的商店状态。此状态由[**createState**](https://hookstate.js.org/docs/typedoc-hookstate-core#createstate)**创建，其中第一个参数是初始状态值/对象。结果值是 [**状态**](https://hookstate.js.org/docs/typedoc-hookstate-core#state) 的一个实例，可以直接用于获取和设置 React 组件外部的状态值。**

```
const globalState = **createState**({
  pokemon:{ name:'',power:'',description:''},
  pokemons:[],
  isEditing:false
})
```

## **[嵌套状态](https://hookstate.js.org/docs/nested-state)**

**嵌套状态类似于本地和全局状态，只是增加了一个接口来访问和改变复杂状态。该接口引入了一个名为 nested 的 API 函数来访问内部的嵌套状态属性，这在处理更复杂的状态对象时非常有用。**

```
const pokemonState = useState(**globalState**);/// Retrive the nested property 'name' of pokemon with **nested** function
pokemonState.**nested**('pokemon').**name**
```

**您也可以在没有**嵌套**功能的情况下访问嵌套状态**

```
const pokemonState = useState(**globalState**);
/// Retrive the nested property 'name' of pokemon
pokemonState**.pokemon.name**
```

## **[作用域状态](https://hookstate.js.org/docs/scoped-state)**

**作用域状态是 Hookstate 中最突出、最独特的特性。它是唯一提供最简单有效方法的库。作用域状态背后的概念是使用深度嵌套的状态挂钩，以允许 Hookstate 只受深度嵌套的子组件的状态更改的影响而重新呈现。为了实现这一点，我们需要用 **useState** 函数包装子状态**

```
*const* taskState = **useState**(state.anySubStatePassed);
```

**作用域状态可以与任何其他状态一起使用，如全局、局部和嵌套状态。它基本上是为了渲染优化技术而存在的。**

## **[异步状态](https://hookstate.js.org/docs/asynchronous-state)**

**根或应用程序状态可以设置为承诺。当执行 API 调用(REST、GraphQL 等)直到承诺被解决时，这将是理想的。
这将通过由 Hookstate 将承诺(Fetch 或 Axios 对象)传递给 **useState** API 来执行。**

```
*const* **fetchResource** = () => fetch(resourcePath).then(r => r.json())
*const* **state** = useState(**fetchResource**);if(**state.error**){
 //handle error, promise rejected
}if(**state.promised**){
 //handle success, promise resolved
}
```

**这个 useState 函数处理承诺，您可以通过**承诺**和**错误**状态访问承诺的状态(已解决或已拒绝)。**

# **Hookstate API**

**在这一节中，让我们集中讨论 Hookstate 中最常用的 API 特性。**

## **[使用状态](https://hookstate.js.org/docs/typedoc-hookstate-core/#usestate)**

**这类似于 React 的使用状态，主要用于在组件内部创建全局或局部状态。useState 也可用于创建限定范围的状态。**

## **[创建状态](https://hookstate.js.org/docs/typedoc-hookstate-core/#createstate)**

**创建一个新状态并返回它。这主要用于创建应用程序的全局状态。与 useState 不同，您可以通过调用 destroy()方法删除状态来销毁此状态(仅适用于特殊场景)。**

**[**none**](https://hookstate.js.org/docs/typedoc-hookstate-core/#const-none)
None 是一个特殊的符号，可以从调用的对象中删除属性。因此，您可以传递 **state.set(none)** 来删除对象或属性，而不是使用 **delete** 或 **filter** 。**

## **[state.get()](https://hookstate.js.org/docs/typedoc-hookstate-core/#get)**

**此方法返回此状态实例的路径所引用的基础状态值。它将返回与**状态值**相同的结果。**

## **[state.set()](https://hookstate.js.org/docs/typedoc-hookstate-core/#set)**

**此方法为状态设置新值。它类似于 React.useState 钩子返回的 **setState** 变量。它可以是一个值，一个解析方法的承诺，或者一个返回其中之一的函数。**

## **[state.merge()](https://hookstate.js.org/docs/typedoc-hookstate-core/#merge)**

**类似于 **set()** 方法更新状态值。如果当前状态值是一个对象，它会对状态对象进行部分更新。如果状态值是一个数组，而参数是一个数组，则它将通过传递的参数连接当前值，并将其设置为状态。**

## **例外目录**

**Hookstate 有一组异常，向程序员传达他们在开发过程中出现的问题和错误。异常键以 **HOOKSTATE** 开头，后跟一个异常号。**

**[**HOOKSTATE-101**](https://hookstate.js.org/docs/exceptions#hookstate-101)**

**[**HOOKSTATE-102**](https://hookstate.js.org/docs/exceptions#hookstate-102)**

**这些只是许多例外中的一部分。**

# **React 和 Hookstate 入门**

**让我们看看如何在 React 中使用 Hookstate。对于这个示例，让我们构建一个口袋妖怪应用程序。这个应用程序将由一个商店和几个访问商店数据的 React 组件组成。**

**1.Store.js:保存全局状态的应用程序商店。
2。PokemonForm.jsx:访问商店，向商店添加或更新 pokemon 数据。
3。jsx:访问商店并呈现口袋妖怪的列表**

## **步骤 01:安装**

**让我们通过以下命令在 react 应用程序上安装 Hookstate**

```
yarn add @hookstate/core @hookstate/persistence @hookstate/devtools
```

**这个命令为 hookstate 核心安装软件包，为 hookstate 安装另外两个插件。persistence 插件用于持久化浏览器中的 hookstate 存储。localStorage 和 dev tools 插件启用 Redux dev 工具(假设您已经在浏览器上安装了 Redux dev tools 扩展)。**

## **步骤 02:创建商店**

**让我们通过使用 store.js 文件中的 **createState** 来创建一个全局存储**

```
import { **createState** } from '@hookstate/core'const store = **createState**({
 pokemon: { name: '', power: '', description: '' },
 pokemons: [{
  id: 1, name: 'pikachu', power: 'fire', description: 'fluffy' 
 }],
 isEditing: false,
})export default store;
```

**上面的存储是由 Hookstate 创建的全局存储的基本示例。Hookstate 不像 Redux 或 React 上下文那样使用**减少器**和**选择器**。Hookstate 允许通过存储对象中可用的 **set()** 、 **merge()** 函数对存储对象进行变更或变异。有几种方法可以实现这一点。**

**1.您可以直接从 React 组件更新状态(这可能会使您的 React 组件变得复杂，甚至引入重复的代码/逻辑)。**

**2.您可以创建一个包装器来包装商店并为其添加更多的功能，因为它易于维护和清理。**

**在这个例子中，我们将使用一个名为 **useGlobalState** 的包装函数来执行 CRUD 操作。**

```
import { createState, useState, none } from '@hookstate/core'const store = **createState**({
 pokemon: { name: '', power: '', description: '' },
 pokemons: [{
  id: 1, name: 'pikachu', power: 'fire', description: 'fluffy' 
 }],
 isEditing: false,
})const **useGlobalState** = () => {
  const state = useState(store); return ({
  get: () => state.value,
  addPokemon: (pokemon) => {
    state.pokemons.merge([pokemon])
  },
  deletePokemon: (id) => {
   state.pokemons.keys.**forEach**(index => {
     if (state.pokemons[index].id.get() === id) {
        **state.pokemons[index].set(none)**
   }})
  },
  updatePokemon: (data) => {
    state.pokemons.keys.**forEach**(index => {
      if (state.pokemons[index].id.get() === data.id) {
          state.pokemons[index].merge(data)
      }
    })
   state.merge({ isEditing: false })
  state.merge({ pokemon: { name: '', power: '', description: '' } })
 },
 selectPokemon: (pokemon) => {
  state.merge({ isEditing: true })
  state.merge({ pokemon: pokemon })
 },
 clearPokemon: () => {
  state.merge({ isEditing: false })
  state.merge({ pokemon: { name: '', power: '', description: '' } })
 }
})
}export default **useGlobalState**;
```

**注意，使用上面的包装器，全局状态突变是可能的(使用 forEach()代替 map()或 reduce，这将创建新的数组)。deletePokemon 函数使用由 Hookstate **引入的符号 **none** 。****

## **步骤 Hookstate 的插件**

**Hookstate 允许一些有用的插件来增强开发体验。下面是其中的一些**

**1.[@ hookstate/initial](https://www.npmjs.com/package/@hookstate/initial)
2。[@ hookstate/validation](https://www.npmjs.com/package/@hookstate/validation)
3。[@ hookstate/已播出](https://www.npmjs.com/package/@hookstate/broadcasted)
4。 [@hookstate/persistence](https://www.npmjs.com/package/@hookstate/persistence)**

**这些插件是独立的 npm 库。Hookstate 文档也提供了如何创建插件的细节。在这个应用程序中，我们将使用持久性插件在 localStorage 中保存状态。**

```
**state.attach(Persistence('pk'))**
```

**要启用持久性插件，我们必须通过包装器将它附加到应用程序状态，如下所示**

**最终 Store.js**

## **步骤 04:访问存储状态**

**让我们访问 PokemonForm.jsx 和 PokemonList.jsx 文件中的商店。首先从表单的组件开始。**

**访问商店的 PokemonForm.jsx**

**注意 create pokemon、update pokemon 和 onChange 方法是如何使用 Hookstate API 方法的。让我们看看如何从列表组件访问商店。**

> ***注意:如果您同时在 React 和 hookstate 中使用 useState，请确保从 [@hookstate/core](http://twitter.com/hookstate/core) 中别名 useState，以消除与 React 的 useState 的命名冲突。**

**您可以清楚地看到在应用程序商店中访问和执行突变是多么容易。**

## **步骤 o5:处理异步状态**

**您可以通过向 useState 方法传递一个 promise(异步方法)来执行异步状态。对于使用 REST API 的应用程序，它将是一个 fetch 对象或一个 Axios 对象。**

**您可以通过这个 [Github repo](https://github.com/TRomesh/pokemon-hookstate) 来了解如何使用 Create-React-App 在 React 应用程序中配置和使用 Hookstate。Hookstate 也支持 [**Preact**](https://hookstate.js.org/docs/performance-preact) ，因为[**Preact**](https://hookstate.js.org/docs/performance-preact)**是一种与相同的现代 API 反应的替代方法。****

# ****结论****

****总之，Hookstate 库是最轻的状态管理库之一。它还提供了通过插件扩展其功能的灵活性，以增强开发体验。****

****例如，Hookstate 引入了作用域状态和对 Redux DevTools、Persist 的支持，以及对 React Hooks、TypeScript 等的支持。Hookstate 附带了一个异常库来识别问题和错误。因此，Hookstate 看起来是一个很好的州管理候选。****

****该库的简单性也使它成为使用 React 进行状态管理的初学者的一个好选择。最后，感谢您花时间阅读本文。我想看看你下面的问题和评论。****

****干杯！****

# ****了解更多信息****

****[](/build-your-own-self-hosted-ci-cd-workflow-with-github-actions-ec9ee1dcd800) [## 使用 GitHub 操作构建您自己的自托管 CI/CD 工作流

### GitHub 引入了 GitHub Actions，使开发人员能够直接从他们的 GitHub 库自动化工作流…

levelup.gitconnected.com](/build-your-own-self-hosted-ci-cd-workflow-with-github-actions-ec9ee1dcd800) [](/jotai-atom-based-state-management-for-react-1ce8fd380296) [## jotai:React 的基于原子的状态管理

### 在过去的几年里，国家管理有了很大的发展。有很多库和方法可以让你…

levelup.gitconnected.com](/jotai-atom-based-state-management-for-react-1ce8fd380296) [](/urql-the-highly-customizable-and-versatile-graphql-client-69e4e3406904) [## 高度可定制和多功能的 GraphQL 客户端

### 在过去的十年里，REST 架构已经成为 web 应用程序的行业标准，因为 REST 提供了一个…

levelup.gitconnected.com](/urql-the-highly-customizable-and-versatile-graphql-client-69e4e3406904) [](/lets-go-and-build-graphql-api-with-gqlgen-bfea2f346ea1) [## 让我们开始用 gqlgen 构建 Graphql API

### Golang 是过去十年中最受欢迎的编程语言之一，主要是因为它的快速…

levelup.gitconnected.com](/lets-go-and-build-graphql-api-with-gqlgen-bfea2f346ea1) 

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)****