# Vue.js 计算属性和观察器简介

> 原文：<https://levelup.gitconnected.com/introduction-to-vue-js-computed-properties-and-watchers-2707e4b0413d>

![](img/4b4e1b26209ecdb0c27e3dde3d984bd1.png)

保罗·吉尔摩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究 Vue.js 计算属性和观察器。

# 计算属性

为了使更多的模板表达式更加简洁和可重用，我们可以创建计算属性，以便在现有属性的值发生变化时计算它们的结果。

我们可以在 Vue 实例中定义计算出的属性，方法是将它们放入 options 对象的`computed`属性中，然后传递给`Vue`构造函数。

例如，我们可以在`src/index.js`中写下如下内容:

```
new Vue({
  el: "#app",
  data: {
    message: "foo"
  },
  computed: {
    spelledMessage() {
      return this.message.split("").join("-");
    }
  }
});
```

然后，我们可以像使用 HTML 模板中的任何其他字段一样使用它。所以在`index.html`中，我们可以写:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <p>{{message}}</p>
      <p>{{spelledMessage}}</p>
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

然后我们应该得到:

```
foof-o-o
```

在我们的屏幕上。

`spelledMessage`方法是 Vue.js 使用的一个 getter。它获取它的返回值，然后把它放在`{{spelledMessage}}`的位置。

因此，模板中占位符的名称必须与返回我们在`computed`对象中想要的值的方法名称相同。

我们还可以从返回的 Vue 实例中获取值，如下所示:

```
const vm = new Vue({
  el: "#app",
  data: {
    message: "foo"
  },
  computed: {
    spelledMessage() {
      return this.message.split("").join("-");
    }
  }
});console.log(vm.spelledMessage);
```

我们应该看到从`console.log`记录的`f-o-o`。

我们可以像绑定任何其他属性一样绑定到模板中的计算属性。Vue 知道`vm.spelledMessage`依赖于`vm.message`，所以`vm.message`更新时绑定也会更新。

computed getter 方法不会产生副作用，这使得它易于测试和理解。

# 计算缓存与方法

计算属性的一个很好的特性是它的结果被缓存。

从方法返回的结果没有缓存。

因此，在我们从现有属性中计算某些东西的情况下，我们应该使用计算属性，而不是使用方法。

只有在反应属性更新后，才会重新计算计算属性。

例如，如果我们有:

```
const vm = new Vue({
  el: "#app",
  data: {
    message: "foo"
  },
  computed: {
    now() {
      return Date.now();
    }
  }
});
```

在第一次计算后，它将永远不会更新，因为它不依赖于来自`data`属性的任何反应属性。

如果不缓存计算的属性，则计算的属性会一直作为它们所依赖的反应性属性进行更改，这意味着应用程序会随着计算的属性所依赖的反应性属性的更改而变慢。

![](img/e9e975650a0d713961f0303d535eb76b.png)

约翰·托尔卡西奥在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 计算属性与监视属性

顾名思义，监视属性对于监视属性的变化非常有用。

然而，由于计算属性的缓存特性，我们应该尽可能多地使用它们。

例如，我们可以简化以下内容:

```
new Vue({
  el: "#app",
  data: {
    name: "Joe",
    age: 1,
    person: undefined
  },
  watch: {
    name: {
      immediate: true,
      handler(newVal) {
        this.person = `${newVal} - ${this.age}`;
      }
    },
    age: {
      immediate: true,
      handler(newVal) {
        this.person = `${this.name} - ${newVal}`;
      }
    }
  }
});
```

收件人:

```
new Vue({
  el: "#app",
  data: {
    name: "Joe",
    age: 1
  },
  computed: {
    person() {
      return `${this.name} - ${this.age}`;
    }
  }
});
```

正如我们所看到的，第一个例子要复杂得多。我们必须在每个属性中设置`immediate`到`true`,这样它就可以观察初始值的变化。然后，我们必须为每个反应属性定义值更改处理程序。

然后在每个处理程序中，我们必须设置`person`属性，这样我们就可以组合`name`和`age`。

另一方面，对于计算属性，我们只有一个方法返回以我们想要的方式组合的字段。

这对于我们来说更方便，而且由于缓存，我们创建的应用程序也更快。

# 计算设定值

我们还可以为计算属性创建一个 setter 函数。

例如，我们可以写:

```
new Vue({
  el: "#app",
  data: {
    name: "Joe",
    age: 1
  },
  computed: {
    person: {
      get() {
        return `${this.name} - ${this.age}`;
      },
      set(newValue) {
        const [name, age] = newValue.split("-");
        this.name = name.trim();
        this.age = age.trim();
      }
    }
  }
});
```

getter 函数返回和以前一样的东西，但是现在我们还有一个 setter 函数。

setter 函数分割`newValue`，它具有 getter 函数计算的值，我们分割结果并将结果设置回相应的 reactive 属性。

我们可以如下使用 setter。我们可以将以下内容放入`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    firstName: "Joe",
    lastName: "Smith"
  },
  computed: {
    name: {
      get() {
        return `${this.firstName} ${this.lastName}`;
      },
      set(newValue) {
        const [firstName, lastName] = newValue.split(" ");
        this.firstName = (firstName || "").trim();
        this.lastName = (lastName || "").trim();
      }
    }
  }
});
```

在`index.html`中，我们放入:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <p>{{name}}</p>
      <input type="text" v-model="name" />
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

然后，当我们改变输入中的值时，我们用 setter 把新值赋回给`firstName`和`lastName`字段。

# 结论

我们可以使用计算属性来计算现有属性的值。

缓存计算属性的返回值，以减少所需的计算量。

计算属性也可以有 setter，在这里我们可以用计算属性 getter 返回的新值做一些事情。