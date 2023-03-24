# 使用顺风 CSS 和查找表的 Vue(组合 API 和类型脚本)中的动态组件样式

> 原文：<https://levelup.gitconnected.com/dynamic-component-styles-in-vue-composition-api-and-typescript-using-tailwind-css-and-lookup-8322eff93a96>

![](img/b3154981b5a533fa60cd989157bc055a.png)

> *本文将假设一些关于 Vue 的知识*

你可以在这里找到选项 API 版本。

上周我写了一篇关于在 Nuxt 中使用动态组件样式时如何选择使用查找表的文章，你可以在这里找到。这个星期我一直在使用 Vue 3，就像使用 Nuxt 一样，在 Vue 中，当我使用动态组件样式时，我会使用查找表，如下所示:

# 顺风顺水

顺风 CSS 是目前前端开发中最热门的话题之一。由 Adam Wathan 创建的实用优先的 CSS 框架，在过去的几年中，它已经从一个辅助项目发展成为一个成功的企业。如果您曾经使用过 Tailwind，您可能会意识到它在构建时利用 PurgeCSS 来删除未使用的样式，并创建一个仅由 web 应用程序中使用的类组成的细长样式表。许多框架现在利用 PurgeCSS 在构建时从产品样式表中移除不必要的大量内容。

PurgeCSS 是一个很棒的插件，但是，它不能解析或运行 JavaScript，并且在大多数情况下只在构建时使用。因此，如果使用不当，可能会导致开发和生产环境之间出现意外的不一致。

# 使用 Tailwind CSS 开始一个全新的 Vue 项目

让我们开始创建一个全新的 Vue 安装，方法是打开您的终端并运行以下命令:

`vue create <your-project-name>`

我们将按照 CLI 说明设置 Vue 的“手动选择功能”选项，确保从功能列表中选择“选择 Vue 版本”和“类型脚本”,其余部分可以保留为默认设置。一旦我们的项目完成设置，我们可以导航到根目录并使用以下命令安装 Tailwind CSS:

`npm install tailwindcss`

一旦成功安装了 Tailwind CSS，我们需要使用下面的命令创建我们的`tailwind.config.js`:

`npx tailwindcss init`

当`tailwind.config.js`文件被创建后，我们需要配置它来扫描我们的`.vue`文件中的类。首先，我们将取消对`future`对象中属性的注释，这将使将来的升级更加容易。接下来，在`purge`数组中，添加下面一行:

`'src/**/*.vue',`

现在我们可以
创建我们的顺风样式表了。导航到`src/assets`文件夹，创建一个名为`css`的新目录，在其中创建一个名为`styles.css`的新文件，并用 Tailwind CSS 导入填充它:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

既然样式表已经设置好了，我们可以通过从`src`目录中打开`main.js`文件并添加下面一行来将其导入到我们的应用程序中:

```
import '@/assets/css/styles.css';
```

最后，我们需要创建 PurgeCSS 配置文件，同样在项目根中，创建一个新文件，这次称为`postcss.config.js`，并用以下代码对其进行配置:

```
// postcss.config.jsconst autoprefixer = require('autoprefixer');
const tailwindcss = require('tailwindcss');module.exports = {
    plugins: [
        tailwindcss,
        autoprefixer,
    ],
};
```

# 带顺风的 Vue 中的动态组件样式

Vue 中组件的一个关键特性是能够传递道具。道具是传递给组件的自定义属性，可用于控制外观和功能。让我们看看如何使用 Tailwind 创建一个简单的按钮组件，它接受两种颜色，“主要”和“次要”:

```
<template>
    <button 
        class="px-4 py-2 text-center transition-colors duration-300 ease-in-out border border-solid rounded shadow-md"
        :class="{ 
            'bg-blue-800 text-white border-blue-800 hover:bg-transparent hover:text-blue-800 hover:border-blue-800' : color == 'primary',
            'bg-transparent text-blue-800 border-blue-800 hover:bg-blue-800 hover:text-white hover:border-blue-800' : color == 'secondary'
        }"
    >
        <slot />
    </button>
</template><script lang="ts">
    import { defineComponent, PropType } from 'vue' export default defineComponent({
        name: 'component--button', props: {
            color: {
                required: false,
                type: String as PropType<string>,
                default: 'primary',
                validator: value => {
                    return ['primary', 'secondary'].includes(value)
                }
            }
        }
    })
</script>
```

因此，我们的按钮组件接受 2 种动态颜色，“主要”和“次要”，正如我们所设定的那样，然而，即使在这个简单的组件中，也很容易看到这些动态样式如何在更复杂的组件中失控。我们也有一个颜色道具验证器，我们必须手动与模板中的动态样式保持同步。

# 提取样式并保持验证器与查找表同步

如果您没有听说过查找表，查找表是一个简单的键-值对对象，我们可以用它来匹配键和数据。我们可以利用查找表来提取动态样式，并确保我们的验证器始终与这些动态样式保持同步。

对于这个例子，我们将在`src`目录中创建一个名为`validators`的新文件夹来存储我们的查找表，尽管同样的技术也可以用来在组件文件中使用查找表。一旦你创建了一个名为`validators`的新文件夹，在里面创建一个名为`Button.ts`的新文件。在`Button.ts`中，我们将导出一个名为`ButtonColors`的`const`，一个包含动态样式的键值对的查找表，如下所示:

```
export const ButtonColors = {
    'primary': 'bg-blue-800 text-white border-blue-800 hover:bg-transparent hover:text-blue-800 hover:border-blue-800',
    'secondary': 'bg-transparent text-blue-800 border-blue-800 hover:bg-blue-800 hover:text-white hover:border-blue-800'
}
```

现在我们已经将动态样式提取到一个查找表中，我们需要对组件进行一些更改，首先，在开始的脚本标签下，我们需要导入我们的`ButtonColors const`:

```
<script lang="ts">
/**/
import { ButtonColors } from '../validators/Button'export default defineComponent({/**/})
</script>
```

接下来，在我们的`color` prop 中，我们需要让 TypesScript 知道该字符串应该等于我们的 ButtonColors 查找表中的一个键，并用来自`ButtonColors`查找表的一个键数组替换当前字符串数组:

```
<script lang="ts">
    export default defineComponent({
        /**/
        props: {
            color: {
                required: false,
                type: String as PropType<keyof typeof ButtonColors>,
                default: 'primary',
                validator: (value : string ) => {
                    return Object.keys(ButtonColors).includes(value)
                }
            }
        }
        /**/
    })
</script>
```

现在我们可以创建一个 computed 属性来处理组件模板中的动态类，首先，我们需要从`vue`导入`computed`，就像我们处理`defineComponent`和`PropType`一样:

```
<script>
    import { defineComponent, PropType, computed } from 'vue'
    /**/
</script>
```

现在我们可以使用`setup`方法并把它作为参数传递给`props`，这样我们就可以设置一个计算的`buttonColor`属性，如下所示:

```
<script>
/**/
export default defineComponent({
    /**/
    setup(props) {
        const buttonColor = computed(() : string  => {
            return ButtonColors[props.color]
        }) return {
            buttonColor
        }
    }
})
</script>
```

然后，我们可以用新的计算属性替换模板中的所有动态类:

```
<template>
    <button 
        class="px-4 py-2 text-center transition-colors duration-300 ease-in-out border border-solid rounded shadow-md"
        :class="[buttonColor]"
    >
        <slot />
    </button>
</template>
```

总之，这应该给我们一个组件模板，如下所示:

```
<template>
    <button 
        class="px-4 py-2 text-center transition-colors duration-300 ease-in-out border border-solid rounded shadow-md"
        :class="[buttonColor]"
    >
        <slot />
    </button>
</template><script lang="ts">
    import { defineComponent, PropType, computed } from 'vue' import { ButtonColors } from '../validators/Button' export default defineComponent({
        name: 'component--button', props: {
            color: {
                required: false,
                type: String as PropType<keyof typeof ButtonColors>,
                default: 'primary',
                validator: (value : string ) => {
                    return Object.keys(ButtonColors).includes(value)
                }
            }
        }, setup(props) {
            const buttonColor = computed(() : string  => {
                return ButtonColors[props.color]
            }) return {
                buttonColor
            }
        }
    })
</script>
```

一切看起来都很好，我们的动态样式被提取，我们的验证器将自动与我们添加的任何新的动态样式保持同步，然而不幸的是，目前我们的组件仍然不会像生产中预期的那样进行样式化。幸运的是，有一个非常简单的修复，从项目的根打开`tailwind.config.js`并在`purge`数组中添加`'src/validators/*.js'`，这将告诉 PurgeCSS 检查我们的 validators 文件夹中的样式，我们最终的`purge`对象应该看起来像这样:

```
module.exports = {
/**/
    purge: [
        'src/**/*.vue',
        'src/validators/*.ts'
    ]
/**/
}
```

# 测试

如果您想在生产中测试您的查找表是否正常工作，您可以在本地生产中测试您的项目。首先安装 Node.js 静态文件服务器:

`npm install -g serve`

安装后，导航到项目的根目录并运行:

`serve -s dist`

# 结论

希望你已经发现这是一个在 Vue options API、Tailwind 和 PurgeCSS 中清理动态类的有用练习。

如果你觉得这篇文章有用，请在[媒体](https://medium.com/@wearethreebears)、[开发人员](https://dev.to/wearethreebears)和/或[推特](https://twitter.com/wearethreebears)上关注我。