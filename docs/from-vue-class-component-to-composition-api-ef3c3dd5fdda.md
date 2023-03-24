# 从 vue 类组件到组合 API

> 原文：<https://levelup.gitconnected.com/from-vue-class-component-to-composition-api-ef3c3dd5fdda>

*Vue 3 现在上市有一段时间了。是时候抛弃 Vue 2 类组件，转而使用全新的组合 API 了。以下是方法。*

![](img/1b651d1a99e4b7954df0f9719caa395c.png)

照片由 [Xavier von Erlach](https://unsplash.com/@xavier_von_erlach?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue 3 给人留下了深刻的印象，因为它背后的团队引入了一种全新的方式来设计 Vue 组件:组合 API，在这里你基本上为组件编写`setup`函数，然后从其他几个函数中组合*它们。在此之前，很多项目使用了包 *vue-class-component* 和 *vue-property-decorator* 来绕过普通的 Vue 2 语法，并能够以类型脚本`class`的形式编写组件。如果您从事其中的一个项目，并且需要一条红线来将您的组件转换为组合 API，下面是这条红线:*

# 先决条件

如果你想直接进入 Vue 3，有一个完整的迁移指南，你必须遵循。如果你只是想使用组合 API，有一个有用的 npm 包`@vue/composition-api`，它可以为现有的 Vue 2 应用程序启用 API。只需安装并将其导入到您的应用程序中即可使用:

```
// terminal
npm install @vue/composition-api// main.ts
import ***VueCompositionApi*** from "@vue/composition-api";
Vue.use(***VueCompositionApi***);
```

# 组件定义

首先，我们要转换组件。你当前代码库中的每个 Vue 组件可能都是一个类，标有`@Component`。在 CompositionAPI 中，我们使用函数`defineComponent`来..嗯，定义一个组件。用于导入组件依赖关系的选项可以 1:1 应用。还要定义一个设置函数，我们稍后会用到它。

```
// OLD:
@Component({
  components: {DependencyComponent1,DependencyComponent2},
})
export default class MyComponent extends Vue {}// NEW:
export default defineComponent({
  name: "MyComponent",
  components: {DependencyComponent1,DependencyComponent2},
  setup(props, context) {
  },
});
```

# 简单的道具

如果你以前使用过`vue-class-component`，我打赌你也使用过`vue-property-decorator`的注释来定义组件类中的道具和其他东西。您当前应用程序中的一个道具将如下所示:

```
@Prop({
  type: Boolean,
  required: true,
  default: false,
})
myProp!: boolean;
```

像`Boolean`这种类型简单的道具，几乎可以不加改动的复制到 Composition API 风格中。在给`defineComponent`的选项中，定义一个属性`props`:

```
export default defineComponent({
  // .....
  props: {
    myProp: {
      type: Boolean,
      required: true,
      default: false,
    },
  }
});
```

如果您使用的只是定义一个类型的属性的简写，您也可以转移它们:

```
// OLD:
@Prop(String)
myProp!: string;// NEW:
props: {
  myProp: String,
}
```

# 定制类型道具

因为我们在这里使用像`Boolean`这样的内置包装器类型作为值(我们用属性和值定义一个对象，而不是一个接口！)，不能只把自己的接口类型当道具类型。

```
interface MyProp {
  foo: boolean;
  bar: number;
}props: {
  myProp: {
    type: MyProp, // Won't work!
    required: true,
    default: false,
  },
}
```

相反，使用 Vue 提供的通用`PropType`适配器接口为您的道具提供定制类型:

```
interface MyProp {
  foo: boolean;
  bar: number;
}props: {
  myProp: {
    type: Object as PropType<MyProp>, // Typed!
    required: true,
    default: false,
  },
}
```

这就是你要做的道具。所有的道具都会自动出现在组件的模板部分。如果你想在`setup`方法中使用它们，它们作为它的第一个参数:

```
setup(props, context) {
   console.log(props.myProp.value);
}
```

我们稍后会谈到`value`的事情。

# 组件数据

组件中最重要的东西是它的数据。当使用`vue-class-component`时，数据由组件`class`的属性表示。在 Composition API 中，我们可以在 setup 函数中将数据定义为`Ref`常量:

```
// OLD:
export default class MyComponent extends Vue {
   myDataValue: string = "";
}// NEW:
setup(props, context) {
  const myDataValue: Ref<string> = ref("");
}
```

当数据真的需要反应时，注意只使用`Ref`。如果它是一个静态值，或者在`setup`方法完成之前您只需要它，那么就使用一个普通的`const`。

为了使数据在你的组件的模板部分中可用，你需要在`setup`函数的末尾`return`它。最简单的方法是使用属性简写:

```
// NEW:
setup(props, context) {
  const myDataValue: Ref<string> = ref(""); return {
    myDataValue,
  };
}
```

请记住最后的这个`return`,因为我们将在接下来的所有工作中用到它。

## 访问数据

您会注意到，`myDataValue`现在是一个对象，尽管我们只是想要一个字符串。这是 Vue 确保变量不会被重新赋值而失去其值的方法。要读取或设置代码中的实际字符串值，可以使用`myDataValue.value`。这同样适用于道具，正如我们在上一节中已经了解的那样。

# 计算属性

作为一个老手，你可能会有很多被定义为类 getters 的计算属性。要将这些转换成组合 API 语法，我们可以使用`computed`函数:

```
// OLD:
export default class MyComponent extends Vue {
  get myLowerDataValue(): string {
    return this.myDataValue.toLowerCase();
  }
}// NEW:
setup(props, context) {
  // Not using arrow function shorthand here for readability.
  const myLowerDataValue: ComputedRef<string> = computed(() => {
    return myDataValue.value.toLowerCase();
  });
}
```

如您所见，我们在这里使用`.value`来访问上面定义的数据的实际值。

如果您还为您的计算属性定义了一个 setter，那么您可以使用`computed`函数的其他符号:

```
// OLD:
export default class MyComponent extends Vue {

  get myModel(): string {
    return this.myDataValue;
  }

  set myModel(newValue: string) {
    this.myDataValue = newValue;
  }// NEW:
setup(props, context) {
  const myLowerDataValue: ComputedRef<string> = computed({
    get: () => myDataValue.value,
    set: (newValue: string) => myDataValue.value = newValue
  });
}
```

# 方法

方法是函数。对于 JavaScript 来说是正确的，对于组合 API 来说也是正确的。只需将您的`class`方法移动到`setup`函数中，并在它们前面加上`function`关键字。不要忘记在你的`setup`函数的末尾`return`这个函数，使它可以被模板访问:

```
// OLD:
export default class MyComponent extends Vue {
  doStuff(withThis: string): boolean {
    return true;
  }
}// NEW:
setup(props, context) {
  function doStuff(withThis: string): boolean {
    return true;
  } return {
    doStuff,
  }
}
```

# 发射事件

这是一个简单的问题。你从老版 Vue 2 中了解到的`$emit`函数现在隐藏在`context`中，它是作为第二个参数提供给设置函数的。如果对上下文参数使用对象析构，甚至只需要去掉$就可以了。

**但是** **注意:**如果你直接在组件的模板中使用`$emit`，你也需要返回新的`emit`函数！

```
// OLD:
export default class MyComponent extends Vue {
  emitStuff(withThis: string): boolean {
    this.$emit("loaded", withThis);
    return true;
  }
}// NEW:
setup(props, { emit }) {
  function doStuff(withThis: string): boolean {
    emit("loaded", withThis);
    return true;
  }return {
    doStuff,
    emit,
  }
}
```

# 钩住

最后但同样重要的是，组件生命周期挂钩(如 created 和 mounted)也需要翻译。对于其中的每一个，您都用一个特殊的关键字作为名称定义了一个方法，Vue 会自动将它们识别为生命周期挂钩(尽管我从来没有真正喜欢过这个概念，因为当您扫描代码时，它不是非常明确)。

现在，对于每个生命周期钩子，都有一个由前缀为`on`的组合 API 提供的函数，比如`onMounted`或`onCreated`。只需使用这些函数，并将您之前的方法作为箭头函数传入:

```
// OLD:
export default class MyComponent extends Vue {
  mounted(): void {
    console.log("is mounted.");
  }
}// NEW:
setup(props, { emit }) {
  onMounted(() => {
    console.log("is mounted.");
  }
}
```

# 最后的话

我希望本文提供了将 Vue 2 应用程序转移到 Composition API 的快速而简单的概述。从这里开始，您可以随时免费升级到
Vue 3，但是您已经为这一变化准备好了所有组件！请记住，你可以一个一个地翻译你的组件，同时仍然有一个工作的应用程序，因为新旧语法可以并存。