# vue-i18n —格式化文本和数字

> 原文：<https://levelup.gitconnected.com/vue-i18n-formatting-text-and-numbers-f256522a831e>

![](img/05f163c1b35509ca6c210695624a18f6.png)

由[拉蒙·弗隆](https://unsplash.com/@ramon_vloon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将了解如何使用 vue-i18n 创建多语言 Vue 应用程序。

# 本地化数字

我们可以用 vue-i18n 在我们想要的地区显示数字。

为此，我们添加了我们想要的格式:

```
import Vue from "vue";
import App from "./App.vue";
import VueI18n from "vue-i18n";Vue.use(VueI18n);
Vue.config.productionTip = false;const numberFormats = {
  "en-ca": {
    currency: {
      style: "currency",
      currency: "CAD"
    }
  },
  fr: {
    currency: {
      style: "currency",
      currency: "EUR",
      currencyDisplay: "symbol"
    }
  }
};const i18n = new VueI18n({
  numberFormats
});new Vue({
  i18n,
  render: h => h(App)
}).$mount("#app");
```

我们添加带有`style`属性的数字样式。

`currency`告诉我们它是什么货币，`currencyDisplay`告诉我们如何显示货币值。

然后我们可以在组件中使用`$n`函数:

```
<template>
  <div id="app">
    <p>{{ $n(100, 'currency', 'fr') }}</p>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

格式化数字。

现在我们得到:

```
100,00 €
```

在屏幕上。

# 自定义格式

我们可以用自己的方式格式化货币。

`i18n-n`组件让我们格式化货币。

例如，我们可以写:

```
<template>
  <div id="app">
    <i18n-n :value="100" format="currency" locale="fr"></i18n-n>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

`value`具有货币价值。`format`告诉我们如何格式化数字。

`locale`是语言环境。

该组件还接受其插槽中的内容，以便进行更多的定制。

我们可以向`currency`槽添加内容，将其格式化为货币:

```
<template>
  <div id="app">
    <i18n-n :value="100" format="currency" locale="fr">
      <template v-slot:currency="{ currency }">
        <span style="color: red">{{ currency }}</span>
      </template>
    </i18n-n>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们用自己的内容填充货币槽。

我们在插槽中添加了一个 span，并将其命名为红色。

现在货币符号是红色的。

还有一个用于整数部分的`integer`槽。

`group`槽有千组。

`fraction`槽有小数部分。

# 区域设置消息语法

我们可以用各种方式添加地区信息。

例如，我们可以写:

```
import Vue from "vue";
import App from "./App.vue";
import VueI18n from "vue-i18n";Vue.use(VueI18n);
Vue.config.productionTip = false;const messages = {
  en: {
    foo: "foo",
    nested: {
      message: "bar"
    },
    messages: [
      "baz",
      {
        internal: "internal message"
      },
      ["qux"]
    ]
  },
  fr: {
    //...
  }
};const i18n = new VueI18n({
  messages,
  locale: "en"
});new Vue({
  i18n,
  render: h => h(App)
}).$mount("#app");
```

然后我们可以写:

```
<template>
  <div id="app">
    <p>{{ $t('foo') }}</p>
    <p>{{ $t('nested.message') }}</p>
    <p>{{ $t('messages[0]') }}</p>
    <p>{{ $t('messages[1].internal') }}</p>
    <p>{{ $t('messages[2][0]') }}</p>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

在我们的组件中。

我们可以在对象、数组中包含消息，并且我们可以使用`$t`通过它们通常的路径获取它们。

所以我们有:

```
foobarbazinternal messagequx
```

显示在屏幕上。

# 链接的区域设置消息

语言环境消息也可以引用其他语言环境消息。

我们可以用`@:`来做。

例如，我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueI18n from "vue-i18n";Vue.use(VueI18n);
Vue.config.productionTip = false;const messages = {
  en: {
    foo: "foo",
    foobar: "@:foo bar"
  },
  fr: {}
};const i18n = new VueI18n({
  messages,
  locale: "en"
});new Vue({
  i18n,
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <p>{{ $t('foo') }}</p>
    <p>{{ $t('foobar') }}</p>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

`@:`符号表示我们将消息与其后的密钥进行匹配。

我们还可以格式化链接的消息。

修改者可以是`upper`、`lower`和`capitalize`。

例如，我们可以写:

```
import Vue from "vue";
import App from "./App.vue";
import VueI18n from "vue-i18n";Vue.use(VueI18n);
Vue.config.productionTip = false;const messages = {
  en: {
    foo: "foo",
    foobar: "@.upper:foo bar"
  },
  fr: {}
};const i18n = new VueI18n({
  messages,
  locale: "en"
});new Vue({
  i18n,
  render: h => h(App)
}).$mount("#app");
```

使`'foo'`成为大写。

现在我们得到:

```
fooFOO bar
```

显示在屏幕上。

![](img/ec6c988507f8a58d5b7b0b4df5087bee.png)

照片由 [Cibi Chakravarthi](https://unsplash.com/@chakravarthi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

用 vue-i18n 格式化数字和地区字符串有很多方法。

消息可以引用其他消息。数字可以格式化为货币。