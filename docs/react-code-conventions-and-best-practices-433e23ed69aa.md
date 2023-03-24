# 对代码约定和最佳实践做出反应

> 原文：<https://levelup.gitconnected.com/react-code-conventions-and-best-practices-433e23ed69aa>

![](img/934081c0b4792c820b299e67238a6273.png)

# 使用林挺和自动格式化程序

React 的所有主要工具都提供了林挺规则。如果你喜欢，可以随意编辑它们以适应你的风格，但是要经常使用一些并自动完成林挺和格式化的过程。首选工具是 [eslint](https://eslint.org/) 和[pretty](https://prettier.io/)。

# 进口订单

向您的 eslint 配置中添加一些进口订单规则。这将确保导入总是以相同的顺序进行，并按照预定义的规则进行分组。

示例:

```
{
  'import/order': [
    2,
    {
      'newlines-between': 'always',
      groups: [
        'builtin',
        'external',
        'internal',
        'parent',
        'sibling',
        'index',
        'unknown',
        'object',
        'type',
      ],
      alphabetize: {
        order: 'asc',
        caseInsensitive: true,
      },
      pathGroups: [
        {
          pattern: 'react*',
          group: 'external',
          position: 'before',
        },
      ],
    },
  ],
}
```

# 命名规格

## 在组件、接口或类型别名中使用 PascalCase

## 将 camelCase 用于 JavaScript 数据类型，如变量、数组、对象、函数等。

## 文件夹和非组件文件名使用 camelCase，组件文件名使用 PascalCase

```
src/utils/form.ts
src/hooks/useForm.ts
src/components/banners/edit/Form.tsx
```

# 更喜欢使用打字稿桶

*桶是一种将几个模块的输出汇总到一个方便的模块中的方法。*

这种方法适用于共享组件、实用程序、帮助函数等实体。为了使您的工作更容易，桶可以通过使用[桶由](https://www.npmjs.com/package/barrelsby)自动生成。

# 避免默认导出

默认导出不会将任何名称与要导出的项目相关联，这意味着在导入过程中可以应用任何名称。这可能给你一个灵活性，但是如果多个开发者用不同的名字或者甚至是非描述性的名字导入同一个模块，我们就完蛋了。

命名导出是显式的，强制消费者使用原作者想要的名称进行导入，消除了任何歧义。

# 组件结构

为了保持所有组件文件的一致性，请遵循以下模式:

# 使用`PropsWithChildren`

# 如果超过 1 行，则从 JSX 中分离功能

# 避免使用索引作为关键道具

# 使用片段

# 首选解构属性

最好使用析构属性，这样就可以清楚地知道组件中使用了什么属性。

# 关注点分离

将业务逻辑从表示(呈现)中分离出来，使得组件代码更具可读性。大多数情况下，这适用于页面/屏幕/容器组件，在这些组件中，您将使用多个挂钩或 useEffects。然后最终的代码开始变得大到无法阅读。

## 定制挂钩

为了分离职责，首先你应该创建定制的钩子，而不是仅仅把一个 useEffect 或者多个 useStates 直接放到你的组件中。

借自[https://usehooks.com/useWindowSize/](https://usehooks.com/useWindowSize/)

## 作为高阶函数的分量控制器

一旦组件过载了状态和定制钩子，就该创建控制器了。

**辅助函数** — *utils/wrap.ts*

**页面控制器**—*usetodocontroller . ts*

**页面** — *TodoList.tsx*

# 避免巨大的组件

只要有可能，就把你的组件分成更小的块。通常适用于使用条件呈现或为数据网格定义列等情况。也适用于使用大量定制挂钩的情况——查看上一节。

# 尽可能对州进行分组

# 使用布尔属性的简写

# 避免字符串属性的花括号

# 避免使用内嵌样式

理想情况下，使用第三方来处理样式，如 CSS 模块、JSS、样式组件等。或者创建自定义挂钩。

# 使用三元运算符的首选条件渲染

# 对字符串值使用常量或枚举

# 使用类型别名

# 不要直接使用第三方库

不要直接导入第三方库，而是在一个集中的地方重新导出它们。

*src/lib/store.ts*

`**export** { useDispatch, useSelector } **from** 'react-redux';`

*src/lib/query.ts*

`**export** { useQuery, useMutation, useQueryClient } **from** 'react-query';`

# 依赖抽象而不是实现

# 更喜欢声明式编程

# 使用描述性变量名称

# 避免函数参数的长列表

# 使用对象析构

# 首选使用模板文字

# 在小函数中使用隐式返回

*额外感谢* [*雅各布*](https://medium.com/u/9353c9c6e764?source=post_page-----433e23ed69aa--------------------------------) *的咨询。*

非常感谢你的阅读！