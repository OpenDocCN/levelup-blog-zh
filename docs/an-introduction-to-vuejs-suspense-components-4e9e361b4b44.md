# VueJS 悬念组件介绍

> 原文：<https://levelup.gitconnected.com/an-introduction-to-vuejs-suspense-components-4e9e361b4b44>

![](img/11e1de35e6a9ddae4afdf799cbd0bb84.png)

**悬念组件是 Vue3 中众所周知的特性之一。**

它们允许我们的应用程序在等待异步组件时呈现一些后备内容——让我们创建一个流畅的用户体验。

令人欣慰的是，悬念组件非常容易理解并开始在你的组件中使用。他们甚至不需要任何额外的进口！

到本文结束时，您应该知道:

*   什么是悬念成分
*   何时使用它们
*   如何使用它们

说够了！让我们开始编码吧。

# 悬念成分到底是什么？

暂停组件用于在等待某种异步组件解决时显示回退内容。

您可能想知道，*“我们什么时候会使用异步组件？”*

老实说，比你想象的要多。每当我们希望组件等待获取数据时(通常在异步 API 调用中)，我们可以使用 [Vue3 组合 API](https://learnvue.co/2020/01/4-vue3-composition-api-tips-you-should-know/) 创建一个异步组件。

下面是异步组件有用的一些例子:

*   在页面加载前显示加载动画
*   显示占位符内容
*   处理延迟加载的图像

以前，在 Vue2 中，我们必须使用条件(例如`v-if` 或`v-else`)来检查我们的数据是否已经加载并显示回退内容。

但现在，Vue3 内置了悬念，所以我们不必担心数据加载时的跟踪和相应内容的呈现。

# 好吧…那么我们如何实现悬念呢？

在这个例子中，我们假设我们有一个异步的`ArticleInfo.vue`组件。

由于本文的重点是悬念而不是合成 API，所以我不会对细节进行疯狂的描述。如果你对更完整的 Composition API 教程感兴趣，[我这里有一个](https://learnvue.co/2020/09/setting-up-your-first-vue3-project-vue-3-0-release/)。

长话短说，只要知道设置方法可以像任何其他方法一样异步。

对于我们的例子，ArticleInfo 将有一个异步`setup`方法，在返回之前加载用户数据。

```
async function getArticleInfo() {
  // some asynchronous API call
  return { article }
}

export default {
  async setup () {
    var { article } = await getArticleInfo()

    return {
      article
    }
  }
}
```

然后，假设我们有一个包含 ArticleInfo 组件的`ArticlePost.vue` 组件。

如果我们希望在等待组件获取数据并解决问题时显示类似于*“Loading Profile…”*的内容，我们只需三个步骤就可以实现悬念。

1.  将我们的异步组件包装在一个`<template #default>`标签中
2.  在标签为`<template #fallback>`的异步组件旁边添加一个兄弟组件
3.  将两个组件包在一个`<suspense>`组件中

使用插槽，悬念将呈现回退内容，直到默认内容准备就绪。然后，它会自动切换到显示我们的异步组件。

它看起来有点像这样。

```
<Suspense>
      <template #default>
        <article-info/>
      </template>
      <template #fallback>
        <div>Loading Profile...</div>
      </template>
</Suspense>
```

# 您还可以捕捉组件错误

Vue 的另一个很酷的特性，尤其是当我们开始使用异步组件时，是我们可以捕捉错误并向用户显示一些错误消息。

即使在 Vue2 中，使用 [errorCaptured](https://vuejs.org/v2/api/#errorCaptured) 钩子也是可能的，但是在 Vue3 中，它已经被重命名为`onErrorCaptured`。

不管它叫什么，这个[钩子](https://learnvue.co/2019/12/a-beginners-guide-to-vuejs-lifecycle-hooks/)在捕获到来自任何后代组件的错误时运行。如果出现问题，我们可以使用这个悬念来呈现错误。

如果我们处理一个错误来显示一条错误消息，上面的组件看起来就是这样。

```
<template>
  <div v-if="errMsg"> {{ errMsg }} </div>
  <Suspense v-else>
      <template #default>
        <article-info/>
      </template>
      <template #fallback>
        <div>Loading Profile...</div>
      </template>
  </Suspense>
</template>

<script>
import { onErrorCaptured } from 'vue'

setup () {
  const errMsg = ref(null)
  onErrorCaptured(e => {
    errMsg.value = 'Uh oh. Something went wrong!'
    return true
  })}
  return { error }
</script>
```

# 你有它！

悬念只是 Vue 让开发者更容易解决常见问题的另一种方式。我们不必有条件地呈现组件，我们可以只使用悬念来为我们处理事情。

在我看来，这是对 Vue3 最整洁的补充之一。

现在，您应该对 Vue 中的悬念组件有了更多的了解，并且您已经想到了一些很酷的方法来开始将它们实现到您的项目中！

如果你想了解更多关于创建你的第一个 Vue3 项目的信息，请查看这篇[深入教程，以设置你的项目](https://learnvue.co/2020/09/setting-up-your-first-vue3-project-vue-3-0-release/)并开始运行复合 API。

如果你有兴趣了解更多关于 Vue 3 的知识，请下载我的免费 Vue 3 备忘单，里面有一些基本知识，比如组合 API、Vue 3 模板语法和事件处理。

*原载于 2020 年 1 月 22 日 https://learnvue.co*[](https://learnvue.co/2020/01/an-introduction-to-vuejs-suspense-components/)**。**