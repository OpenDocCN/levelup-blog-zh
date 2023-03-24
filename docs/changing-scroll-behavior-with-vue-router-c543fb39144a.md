# 使用 Vue 路由器更改滚动行为

> 原文：<https://levelup.gitconnected.com/changing-scroll-behavior-with-vue-router-c543fb39144a>

![](img/e7171b2a240748ada5649656e098161c.png)

Mick Haupt 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

Vue 路由器是一个 URL 路由器，它将 URL 映射到组件。

在本文中，我们将研究如何改变 Vue 路由器路由的滚动行为。

# 更改滚动行为

有时，我们可能希望在导航到新路线时滚动到顶部，或者像重新加载真实页面一样保留历史实体的滚动位置。

Vue Router 允许我们在加载路由时按照自己喜欢的方式调整滚动行为。

这只有在浏览器支持`history.pushState`的情况下才有效。

我们可以通过提供一个`scrollBehavior`函数来调整滚动行为。

它有一个`to`和`from`路由对象作为前两个参数。第三个参数是`savePosition`，仅当这是由浏览器的后退或前进按钮触发的`popstate`导航时才可用。

该函数可以返回一个滚动位置对象，其形式为:

```
{ x: number, y: number }
```

或者:

```
{ selector: string, offset? : { x: number, y: number }}
```

从 Vue 路由器 2.6.0 开始支持`offset`

例如，我们可以将`scrollBehavior`添加到我们的路线中，如下所示:

`src/index.js:`

```
const Foo = {
  template: `
    <div>
      <div v-for='n in 100'>{{n}} foo</div>
    </div>
  `
};
const Bar = {
  template: `
  <div>
    <div v-for='n in 100'>{{n}} bar</div>
  </div>
  `
};const routes = [
  {
    path: "/foo",
    component: Foo
  },
  {
    path: "/bar",
    component: Bar
  }
];const router = new VueRouter({
  routes,
  scrollBehavior(to, from, savedPosition) {
    return { x: 0, y: 0 };
  }
});new Vue({
  el: "#app",
  router
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://unpkg.com/vue/dist/vue.js](https://unpkg.com/vue/dist/vue.js)"></script>
    <script src="[https://unpkg.com/vue-router/dist/vue-router.js](https://unpkg.com/vue-router/dist/vue-router.js)"></script>
  </head>
  <body>
    <div id="app">
      <router-view></router-view>
      <router-link to="foo">Foo</router-link>
      <router-link to="bar">Bar</router-link>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们有:

```
scrollBehavior(to, from, savedPosition) {
  return { x: 0, y: 0 };
}
```

导航时将下一页滚动回顶部。因此，当我们点击页面底部的链接时，我们将返回到顶部。

如果我们返回一个假的东西或者一个空的对象，就不会发生滚动。

如果我们将`scrollBehavior`改为:

```
scrollBehavior(to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition;
  } else {
    return { x: 0, y: 0 };
  }
}
```

然后，当我们使用前进和后退按钮来回导航时，我们保持页面的滚动位置。

我们可以通过在元素上设置 ID 来创建滚动到锚点行为，然后如下更改`scrollBehavior`:

`src/index.js`:

```
const Foo = {
  template: `
    <div>
      <div v-for='n in 100' :id='n'>{{n}} foo</div>
    </div>
  `
};const routes = [
  {
    path: "/foo",
    component: Foo
  }
];const router = new VueRouter({
  routes,
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        selector: to.hash
      };
    }
  }
});new Vue({
  el: "#app",
  router
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://unpkg.com/vue/dist/vue.js](https://unpkg.com/vue/dist/vue.js)"></script>
    <script src="[https://unpkg.com/vue-router/dist/vue-router.js](https://unpkg.com/vue-router/dist/vue-router.js)"></script>
  </head>
  <body>
    <div id="app">
      <router-view></router-view>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

然后当我们转到`/#/foo#100`时，我们滚动到底部，然后当我们转到`/#/foo#20`时，我们看到`20 foo`在屏幕顶部。

![](img/cb0ce95a291c4e7d2031648f6d73f8b2.png)

照片由 [Mark Rasmuson](https://unsplash.com/@mrasmuson?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 异步滚动

从 Vue Router 2.8.0 开始，我们还可以通过返回一个承诺来使用`scrollBehavior`进行异步滚动，该承诺解析为与同步滚动相同的对象。

例如，我们可以将上面的示例改为:

`src/index.js`:

```
const Foo = {
  template: `
    <div>
      <div v-for='n in 100' :id='n'>{{n}} foo</div>
    </div>
  `
};const routes = [
  {
    path: "/foo",
    component: Foo
  }
];const router = new VueRouter({
  routes,
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve({
            selector: to.hash
          });
        }, 1000);
      });
    }
  }
});new Vue({
  el: "#app",
  router
});
```

然后当我们在浏览器中进入`/#/foo#100`时，我们会在一秒钟后滚动到页面底部。

# 结论

在路线导航过程中的滚动行为可以通过给我们的`router`对象添加一个`scrollBehavior`函数来改变。它采用作为路线对象的`to`和`from`参数，以及带有导航路线的保存滚动位置的第三个`savedPosition`参数。

自 Vue Router 2.8.0 以来，滚动行为可以是同步或异步的。

我们可以用 CSS 选择器或位置来滚动到一个元素。