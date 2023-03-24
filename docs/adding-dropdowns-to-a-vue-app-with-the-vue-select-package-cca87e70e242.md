# 使用 Vue Select 包向 Vue 应用程序添加下拉菜单

> 原文：<https://levelup.gitconnected.com/adding-dropdowns-to-a-vue-app-with-the-vue-select-package-cca87e70e242>

![](img/ba75393c3deda8a65c5a0107d052baa8.png)

[Keagan Henman](https://unsplash.com/@henmankk?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了使下拉更容易，我们可以使用 Vue Select 插件来添加下拉菜单。

# 入门指南

我们可以通过运行以下命令安装来添加软件包:

```
npm install vue-select
```

或者

```
yarn add vue-select
```

然后我们可以通过在`main.js`中注册组件来使用它

```
import Vue from "vue";
import App from "./App.vue";
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";Vue.component("v-select", vSelect);
Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

然后在我们的组件文件中，我们添加:

```
<template>
  <div id="app">
    <v-select :options="['Canada', 'United States']"></v-select>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

`options`道具让我们为下拉菜单添加选项。

我们还可以用数组填充选择选项:

```
<template>
  <div id="app">
    <v-select :options="options"></v-select>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      options: [
        { label: "Canada", code: "ca" },
        { label: "United States", code: "us" }
      ]
    };
  }
};
</script>
```

然后会显示`label`值。

我们可以用要显示的字符串来设置属性名。例如，我们可以写:

```
<template>
  <div id="app">
    <v-select label="countryName" :options="options"></v-select>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      options: [
        { countryName: "Canada", code: "ca" },
        { countryName: "United States", code: "us" }
      ]
    };
  }
};
</script>
```

# Null /空选项

如果我们不想显示任何选项，我们可以将`options`设置为空数组。

# 获取和设置

我们可以用`v-model`将选择的值绑定到一个状态。

例如，我们可以写:

```
<template>
  <div id="app">
    <v-select v-model="selected" :options="options"></v-select>
    {{selected}}
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      selected: "",
      options: ["Canada", "United States"]
    };
  }
};
</script>
```

`selected`将自动更改为所选的值。

如果我们只想设置所选的值，我们也可以设置`value`属性:

```
<template>
  <div id="app">
    <v-select :value="selected" :options="options"></v-select>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      selected: "Canada",
      options: ["Canada", "United States"]
    };
  }
};
</script>
```

# 用`reduce`返回单键

我们可以用`reduce`道具返回一个单键。

例如，如果我们有:

```
<template>
  <div id="app">
    <v-select
      v-model="selected"
      :reduce="country => country.code"
      label="countryName"
      :options="options"
    ></v-select>
    {{selected}}
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      selected: "",
      options: [
        { countryName: "Canada", code: "ca" },
        { countryName: "United States", code: "us" }
      ]
    };
  }
};
</script>
```

然后`v-model`会和被选对象绑定到被选对象的`code`值。

`reduce`处理深度嵌套的值。例如，我们可以写:

```
<template>
  <div id="app">
    <v-select
      v-model="selected"
      :reduce="country => country.meta.code"
      label="countryName"
      :options="options"
    ></v-select>
    {{selected}}
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      selected: "",
      options: [
        { countryName: "Canada", meta: { code: "ca" } },
        { countryName: "United States", meta: { code: "us" } }
      ]
    };
  }
};
</script>
```

`selected`值现在被绑定到所选对象的`meta.code`属性。

# 结论

vue-select 包允许我们向下拉菜单添加比常规 select 元素更多的功能。