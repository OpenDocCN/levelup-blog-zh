# Vue 最佳实践—模板和变量

> 原文：<https://levelup.gitconnected.com/vue-best-practices-templates-and-variables-99cc7e1fd42b>

![](img/f56d77f69dd8958c1893dae61b753d4b.png)

[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue 是一个易于使用的前端框架。它让我们轻松地创建应用程序。

然而，当我们用它们编写应用程序时，仍然有许多事情需要我们注意。

在这篇文章中，我们将看看在构建 Vue 应用时应该遵循的一些基本规则。

# 没有未使用的组件

我们的项目中不应该有未使用的组件。

所以与其写:

```
<template >
  <div id="app"></div>
</template>
<script>
import HelloWorld from "./components/HelloWorld";
export default {
  name: "App",
  components: {
    HelloWorld
  }
};
</script>
```

我们应该写:

```
<template >
  <div id="app">
    <HelloWorld/>
  </div>
</template>
<script>
import HelloWorld from "./components/HelloWorld";
export default {
  name: "App",
  components: {
    HelloWorld
  }
};
</script>
```

# v-for 中没有未使用的变量定义

当我们定义一个`v-for`循环时，我们应该使用所有被定义的变量。

例如，不写:

```
<template >
  <div id="app">
    <ol v-for="i in 5">
      <li>foo</li>
    </ol>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

我们应该写:

```
<template >
  <div id="app">
    <ol v-for="i in 5">
      <li>{{ i }}</li>
    </ol>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

# 不要同时使用 v-if 和 v-for

`v-for`和`v-if`不应该一起使用，因为它比使用计算属性慢。

我们应该使用 competed 属性来呈现更少的项目，而不是使用`v-if`来检查每个项目是否应该被呈现。

例如，不写:

```
<template >
  <div id="app">
    <ul v-for="a in arr" v-if="a % 2 === 0" :key="a">
      <li>{{ a }}</li>
    </ul>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      arr: [1, 2, 3, 4, 5]
    };
  }
};
</script>
```

我们写道:

```
<template>
  <div id="app">
    <ul v-for="a in evenNums" :key="a">
      <li>{{ a }}</li>
    </ul>
  </div>
</template>
<script>
export default {
  name: "App",
  computed: {
    evenNums() {
      return this.arr.filter(a => a % 2 === 0);
    }
  },
  data() {
    return {
      arr: [1, 2, 3, 4, 5]
    };
  }
};
</script>
```

# 将 is Prop 添加到 Component 组件

为了动态显示组件，我们必须使用`component`组件来显示项目。

属性设置要显示的 com[onent]的名称。

例如，不写:

```
<template >
  <div id="app">
    <component/>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

我们写道:

```
<template >
  <div id="app">
    <component :is="name"></component>
  </div>
</template>
<script>
import HelloWorld from "./components/HelloWorld";
export default {
  components: {
    HelloWorld
  },
  name: "App",
  data() {
    return {
      name: "HelloWorld"
    };
  }
};
</script>
```

# 属性类型应该是构造函数

我们应该将构造函数作为`props`属性的值，以确保它们是某个构造函数的实例。

例如，不写:

```
<template >
  <div id="app"></div>
</template>
<script>
export default {
  name: "App",
  props: {
    name: "String"
  }
};
</script>
```

我们应该写:

```
<template >
  <div id="app"></div>
</template>
<script>
export default {
  name: "App",
  props: {
    name: String
  }
};
</script>
```

如果我们的道具可以有多种类型，它也可以是一个数组:

```
<template >
  <div id="app"></div>
</template>
<script>
export default {
  name: "App",
  props: {
    name: [String]
  }
};
</script>
```

# 渲染函数应该返回一些东西

渲染函数应该返回我们想要渲染的东西。

例如，不写:

```
Vue.component("hello", {
  render(createElement) {
    if (Math.random() < 0.5) {
      return createElement("div", "hello");
    }
  }
});
```

我们应该写:

```
Vue.component("hello", {
  render(createElement) {
    return createElement("div", "hello");    
  }
});
```

我们确保渲染函数总是返回一些东西。

# 为 v-for 渲染的项目添加关键道具

我们需要对任何由`v-for`渲染的东西使用`key`道具，这样 Vue 就可以正确地跟踪它。

例如，我们应该写:

```
<template >
  <div id="app">
    <div v-for="a in 5">{{a}}</div>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

我们写道:

```
<template >
  <div id="app">
    <div v-for="a in 5" :key="a">{{a}}</div>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

现在 Vue 可以正确地跟踪渲染的项目了。

![](img/77433c6d7f2070c9fc7ab91c68483292.png)

汤姆·约瑟夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们应该确保我们有一个`key`道具添加到由`v-for`渲染的任何东西上。

不使用的组件应该从我们的代码中删除。

渲染函数应该总是返回要渲染的内容。

适当类型应该是构造函数。否则，道具类型检查将不起作用。