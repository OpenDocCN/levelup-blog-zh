# Vue 提示—过滤器和错误

> 原文：<https://levelup.gitconnected.com/vue-tips-filters-and-errors-f29a1fd501b1>

![](img/ce2694a6191120d6d00e345e920b9c82.png)

[Raychan](https://unsplash.com/@wx1993?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将了解如何使用过滤器来格式化屏幕上的数据，以及如何避免控制台中的错误。

# 创建筛选器以重复使用格式

在屏幕上格式化数据可能很烦人。我们必须处理数字、百分比、日期、货币等多种格式。事情变得很复杂。

这就是为什么我们想做一段可重用的代码，这样我们只需要处理这些问题一次，然后就忘记了。

幸运的是，Vue 有过滤器，我们可以定义过滤器来格式化数据。它通过消除模板和组件中的格式化代码，使模板更加整洁。

我们可以定义一个 Vue 过滤器，并按如下方式使用:

`index.js`:

```
Vue.filter("localeDateString", date => {
  return date.toLocaleDateString();
});new Vue({
  el: "#app",
  data: {
    date: new Date()
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      {{date | localeDateString}}
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

在上面的代码中，我们使用了`Vue.filter`方法来定义一个过滤器。第一个参数是过滤器的名称。第二个是一个函数，它接受一个我们想要格式化的值，然后我们返回一个格式化的字符串。

然后我们将`date`值设置为`new Date()`，并用管道操作符将`localeDateString`应用于它。

我们可以在任何地方重用它，因为它是一个全局过滤器声明。我们还可以使用组件选项对象中的`filters`属性进行本地过滤器声明，如下所示:

`index.js`:

```
new Vue({
  el: "#app",
  data: {
    date: new Date()
  },
  filters: {
    localeDateString(date) {
      return date.toLocaleDateString();
    }
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      {{date | localeDateString}}
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

唯一的区别是我们将过滤函数移到了`filters`对象中。我们只能在组件中使用局部过滤器。

我们还可以将参数传递给过滤器，如下所示:

`index.js`:

```
Vue.filter("readMore", (text, moreText) => {
  return `${text.toUpperCase()} ${moreText}`;
});new Vue({
  el: "#app",
  data: {
    message: "foo"
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      {{message | readMore('bar')}}
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

我们只是像其他函数一样传入参数，除了参数以第二个参数的值结束。

此外，我们可以按照我们希望应用过滤器的顺序，从左到右逐个添加过滤器，从而将它们链接起来。

![](img/1662cd4d623f140a70475cb642b690b1.png)

照片由[泰勒 B](https://unsplash.com/@carstyler?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 避免恼人的错误和警告

我们应该知道开发人员控制台中显示的错误和警告的原因，这样我们就可以在不花费太长时间的情况下消除它们。

例如，属性或方法“prop”没有在实例上定义，而是在渲染时被引用…”当我们引用模板中的数据，但我们没有在用`data`返回的对象中初始化它时，就会出现错误。

如果我们有一个属性或变量，但我们拼错了属性或变量的名称，也会发生这种情况。

在这种情况下，我们应该检查错别字。例如，如果我们有这样的错误，我们会得到:

`index.js`:

```
new Vue({
  el: "#app",
  data: {
    message: "foo"
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      {{messag}}
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

在上面的代码中，模板中有`{{messag}}`，但是组件代码中有`message: “foo”`。

所以我们应该确定它们是一样的，这样错误就不会存在了。

我们还在模板引用的不同组件的属性中定义了它。这也是我们常犯的错误。

# 结论

创建和使用过滤器使得在模板上格式化数据变得容易。我们可以对它们进行全局或局部定义。我们可以通过链接来组合它们，也可以向它们传递参数来更改选项。

来自 Vue 的最常见的错误消息是“属性或方法“prop”未在实例上定义，但在渲染过程中被引用…”错误。

这意味着我们正在引用模板中的项目，这些项目没有被定义为我们用`data`返回的对象中的属性。