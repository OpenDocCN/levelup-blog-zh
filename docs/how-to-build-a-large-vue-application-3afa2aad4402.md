# 如何构建大型 Vue 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-build-a-large-vue-application-3afa2aad4402>

![](img/b6dfb7d594fc3188ca14427b01fe30ee.png)

可观测模型的 MVVM 使 Vue 在中小型 Web 应用中具有天然优势，但随着 Vue 的日益普及，Vue 在大型项目中的使用略显尴尬。

显然，在高复杂度的项目中，类型检查已经成为一个必需的特性，而 Vue2 在 TypeScript 中的类型检查支持不够好，重要的是缺乏 Vuex 的状态逻辑的模块化设计。

因此，以下是建议的解决方案:

*   业务逻辑的模块化— [usm-vuex](https://github.com/unadlib/usm) 将解决模块化的重要问题
*   类型脚本— vue-cli3
*   TSX —更好的模板类型检查
*   依赖注入——最佳依赖注入库:投资
*   子包—用 lerna 构建 Monorepo

lerna 初始化后，进行领域驱动设计，得到大领域模块。如果有必要，可以在启用动态导入延迟加载或模块加载器(如 RequireJS)的同时进行分包，以提高运行时性能和构建性能。

在使用 Vue-cli3 结构的核心应用子程序包的初始化中，选择 TypeScript 作为主要语言，它会自动引入 Webpack ts-loder。

这是核心目录结构:

```
|-- App.vue
|-- main.ts
+-- modules/
  |-- Todos/
  |-- Navigation/
  |-- Portal/
  |-- Counter/
  ...
+-- lib/
  |-- loader.ts
  |-- moduleConnect.ts
  ...
+-- components/
...
```

`main.ts`是默认条目。

```
*// ...*
*// omit some modules*
import { load } from './lib/loader';const {
  portal,
  app,
} = load({
  bootstrap: 'Portal',
  modules: {
    Counter,
    Todos,
    Portal,
    Navigation
  },
  main: App,
  components: {
    home: {
      screen: TodosView,
      path: '/',
      module: 'todos',
    },
    counter: {
      screen: CounterView,
      path: '/counter',
      module: 'counter',
    },
  }
});
Vue.prototype.portal = portal;
new Vue(app).$mount("#app");
```

`App.vue`是一个主视图文件。

```
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/counter">Counter</router-link> 
    </div>
    <router-view />
  </div>
</template>
```

`modules`包含了所有的业务逻辑，包括视图层状态和导航模块等等。它将由 Vuex 运营。

例如，下面是`Counter`模块:

```
import { injectable } from "inversify";
import Module, { state, action } from "../../lib/baseModule";@injectable()
export default class Counter extends Module {
  @state count: number = 0; @action
  calculate(num: number, state?: any) {
    state.count += num;
  } getViewProps() {
    return {
      count: this.count,
      calculate: (num: number) => this.calculate(num)
    }
  }
}
```

`lib/loader.ts`是应用程序配置加载器。

```
import { Container } from 'inversify';export function load(params: any = {}) {
  const { bootstrap, modules, ...option } =  params;
  const container = new Container({ skipBaseClassChecks: true });
  Object.keys(modules).forEach(key => {
    container.bind(key).to(modules[key]);
  });
  container.bind("AppOptions").toConstantValue(option);
  const portal: any = container.get(bootstrap);
  portal.bootstrap();
  const app = portal.createApp();
  return {
    portal,
    app,
  };
}
```

`lib/moduleConnect.ts`是 ViewModule 的视图连接器。这是一个高阶组件的连接器。

```
import { Component, Vue } from "vue-property-decorator";export default (ViewContainer: any, module: string) => {
  @Component({
    components: {
      ViewContainer
    }
  })
  class Container extends Vue {
    get module() {
      return this.portal[module];
    } render(createElement: any) {
      const props = this.module.getViewProps();
      return createElement(ViewContainer, {
        props
      })
    }
  }
  return Container;
}
```

`components/Counter/index.tsx`是一个`Counter`视图组件。

```
import { Component, Vue, Prop } from "vue-property-decorator";
import './style.scss';type Calculate = (sum: number) => void;@Component
export default class CounterView extends Vue {
  @Prop() count!: number;
  @Prop(Function) calculate!: Calculate;   render(){
    return (
      <div class="body">
        <button onClick={()=> this.calculate(1)}>+</button>
        <span>{this.count}</span>
        <button onClick={()=> this.calculate(-1)}>-</button>
      </div>
    )
  }
}
```

结合 TSX 的视图组件模块，整体设计并基于此架构，将更容易用 TypeScript 检查类型。

Vue 架构的核心设计部分应该是`usm-vuex`。它使业务模块化的 Vuex 变得简单明了，可以使当前的架构设计与视图层的 ViewModule 高内聚、低耦合。

通过依赖注入，大大提高了体系结构的可重用性和可维护性，模块间的依赖关系清晰易懂。当然，对于大型应用程序，还有很多其他细节需要完善，本文不在此讨论。

`usm-vuex` Repo: [https://github.com/unadlib/usm](https://github.com/unadlib/usm)

This Demo Repo: [https://github.com/unadlib/usm-vuex-demo](https://github.com/unadlib/usm-vuex-demo)