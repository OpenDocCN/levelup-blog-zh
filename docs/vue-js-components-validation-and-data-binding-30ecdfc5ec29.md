# Vue.js 组件—验证和数据绑定

> 原文：<https://levelup.gitconnected.com/vue-js-components-validation-and-data-binding-30ecdfc5ec29>

![](img/ca8934254fd91f31fde31988c31ebcb8.png)

照片由[费利克斯·拉姆](https://unsplash.com/@feliixlam?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以利用它来开发交互式前端应用。

在本文中，我们将看看如何用 props 以不同的方式绑定数据，并验证它们的值。

# 单向绑定

单向绑定让我们可以在子组件和父组件之间发送数据。当父属性更新时，数据将传播到子属性，而不是相反。

这可以防止子组件意外更改其父组件的状态。

这意味着我们不应该改变子组件中的道具。

为了防止道具变异，我们可以用道具集作为初始值来定义一个局部属性。

例如，我们可以这样做:

`src/index.js`:

```
Vue.component("child", {
  props: ["initialValue"],
  data() {
    return {
      foo: this.initialValue
    };
  },
  template: `<div>{{foo}}</div>`
});new Vue({
  el: "#app"
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child initial-value="Foo"></child>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

我们已经将`foo`设置为`initial-value`道具的值。

对于需要转换的属性值，我们可以在 computed property 函数中进行转换。

例如，我们可以添加一个计算属性函数，如下所示:

`src/index.js`:

```
Vue.component("child", {
  props: ["initialValue"],
  computed: {
    foo: function() {
      return this.initialValue.trim().toLowerCase();
    }
  },
  template: `<div>{{foo}}</div>`
});new Vue({
  el: "#app"
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child initial-value="Foo"></child>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

JavaScript 中的对象和数组是通过引用传递的，所以如果我们直接在子组件中修改属性值，就会影响父组件的状态。

# 正确验证

我们可以赋予适当值来键入它们的类型和我们指定的其他需求。这让我们在传入道具时避免了错误。

如果不符合要求，Vue 会在浏览器的开发控制台中警告我们。

为了验证正确的数据，我们向组件传递一个对象。例如，我们可以这样写:

`src/index.js`:

```
Vue.component("child", {
  props: {
    foo: String,
    bar: [String, Number],
    baz: {
      type: String,
      required: true
    },
    num: {
      type: Number,
      default: 1
    },
    obj: {
      type: Object,
      default() {
        return { message: "hello" };
      }
    },
    msg: {
      validator(value) {
        return ["success", "warning", "danger"].includes(value);
      }
    }
  },
  template: `
    <div>
      <div>{{foo}}</div>
      <div>{{bar}}</div>
      <div>{{baz}}</div>
      <div>{{num}}</div>
      <div>{{obj}}</div>
      <div>{{msg}}</div>
    </div>
  `
});new Vue({
  el: "#app"
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child
        foo="Foo"
        :bar="1"
        baz="baz"
        :num="1"
        :obj="{ message: 'hi'}"
        msg="success"
      ></child>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

我们可以传入上面的`props`属性中列出的各种要求的道具。

正如我们所看到的，我们可以设置单个类型，多个类型，使一个 prop 成为必需的，并提供设置默认值和验证值的函数。

当验证失败时，浏览器控制台会显示一条消息，告诉我们验证无效。

`:`是`v-bind`的简称，因此`:num`与`v-bind:num`相同。

![](img/ec162031e5b1690b39ffca572a2b6827.png)

照片由 [Erol Ahmed](https://unsplash.com/@erol?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 类型检查

道具可以是以下一种或多种类型:

*   `String`
*   `Number`
*   `Boolean`
*   `Array`
*   `Object`
*   `Date`
*   `Function`
*   `Symbol`

Type 也可以是一个自定义的构造函数，这样我们就可以传入我们自己的构造函数的实例并验证它们。

例如，我们可以验证`person`属性是`Person`类的一个实例，如下所示:

`src/person.js`:

```
export class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
```

`src/index.js`:

```
import { Person } from "./person";Vue.component("child", {
  props: {
    person: {
      type: Person
    }
  },
  template: `
    <div>
      <div>First Name: {{person.firstName}}</div>
      <div>Last Name: {{person.lastName}}</div>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    person: new Person("Jane", "Smith")
  }
});
```

验证发生在:

```
person: {
  type: Person
}
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child :person="person"></child>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

那么我们应该得到:

```
First Name: JaneLast Name: Smith
```

在`src/index.js`中，如果我们改变:

```
new Vue({
  el: "#app",
  data: {
    person: new Person("Jane", "Smith")
  }
});
```

收件人:

```
new Vue({
  el: "#app",
  data: {
    person: { firstName: "Jane", lastName: "Smith" }
  }
});
```

我们将得到错误:

```
[Vue warn]: Invalid prop: type check failed for prop "person". Expected Person, got Object
```

因为我们设置为`person`属性值的对象不是`Person`类的实例。

由于类只是构造函数的语法糖，我们也可以用构造函数替换`Person`类，如下所示:

`src/person.js`:

```
export function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
```

保持其他一切不变，我们会得到和以前一样的结果。

# 结论

当我们传入 props 时，我们必须确保不要在子组件中直接修改它们，因为我们不想修改父组件的状态。

Vue 具有适当验证功能。它让我们验证 prop 是某种类型，无论它是否是必需的，设置默认值并创建我们自己的验证函数。

我们也可以用同样的方式验证一个对象是否是我们自己的类或构造函数的实例。