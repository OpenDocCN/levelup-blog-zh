# 请停止在 CSS 类中包含颜色名称。

> 原文：<https://levelup.gitconnected.com/please-stop-including-color-names-in-your-css-classes-f1090f6f2e29>

![](img/a5a683975d2d15935aa3f7f31cf680d5.png)

罗伯特·卡茨基在 [Unsplash](https://unsplash.com/s/photos/colors?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

这是一个很傻的类名:

```
.button-blue
```

也傻(来自顺风):

```
.bg-red-900
```

通过在类名本身中指定颜色名称，您部分地违背了拥有一个类的目的。

如果品牌团队决定将默认按钮颜色从蓝色改为绿色，您必须在标记和 CSS 上进行搜索和替换。在这一点上，你还不如把“背景色:蓝色；因为无论如何你都要用“蓝色按钮”类来重构一切。

> 同样，我们不将断点/响应类命名为“bp-900px”，而是使用“sm”和“lg”，我们不应该在类名中包含像“red”和“blue”这样的样式值。

# 聪明的家伙，那我该怎么给我的颜色课命名呢？

这是我最近处理的一个 SCSS 文件的摘录，在那里我设置了一些颜色变量:

```
$interface-action-color-default: #2271b1;
$interface-action-color-inactive: #59a3df;
$interface-container-padding-default: 20px;
$interface-container-color-border: #CCC;
$interface-container-border-radius: 2px;
$interface-container-border-color: #CCC;
```

你可能不喜欢我如何命名这些变量的**细节**。这很好。(实际上，我在这里对我的命名约定写了一个小的评论，然后为了简洁起见删除了它)。

就本文而言，命名约定的细节不如名称中的抽象重要。换句话说:我已经将**实际颜色值与类名—** 分开，这才是真正重要的。

这样，当颜色不可避免地因品牌更新或 A/B 测试而改变，显示绿色总是比蓝色好时，我可以在一个地方改变一次变量。这是我们为什么首先使用类的一个重要原因。

我的 CSS 类可能看起来像这样:

```
.interface-action {
  background-color: $interface-action-color-default;

  &--disabled {
    background-color: $interface-action-color-disabled;
  }
}
```

下面是一些示例标记:

```
<button class="interface-action">Edit</button>
<button class="interface-action interface-action--disabled">Delete</button>
```

这允许我避免在类名中使用像“blue”和“800”这样的值。如果有其他类型的动作，我将使用类似“接口-动作-alt”或“接口-动作-辅助”的类名。

# 使用顺风主题获得更抽象的类名

我最初对 Tailwind 在类名中使用颜色“值”感到厌恶。它可以通过使用[主题](https://tailwindcss.com/docs/theme)来解决，尽管我真的希望主题层在键和值的嵌套方面提供更多的灵活性。但是同样的惯例可以适用于那里；您可以在 tailwind.config.js 中定义这样的主题:

```
module.exports = {
  theme: {
    colors: {
      action: {
        default: '#2271b1', 
        disabled: '#742a2a'
      },
      toolbar: {
        default: '#EEE',
        alt: '#CCC'
      }
    }
  }
}
```

您可以像这样在 CSS 中使用主题:

```
button {
  background-color: theme('colors.action.default');
}

button.disabled {
  background-color: theme('colors.action.disabled');
}

/* Sidenote: I generally avoid styling the actual button element and 
 * instead opt for an abstracted class name there too, but I'm doing it here
 * for the sake of example.
*/ 
```

如果您想完全跳过 CSS，Tailwind 会自动生成相关的类名:

```
<button class="bg-action-default">Edit</button>
<button class="bg-action-disabled">Delete</button>
```

# 对所有不同的元素类型进行分类，给它们起一个有意义但很好抽象的名字，并考虑到缺省值的变化，这看起来是一项很大的工作。

没错。如果你的设计系统没有足够的一致性来允许这个惯例，调整方法是完全没问题的。决定如何正确命名元素以及它们可能处于的状态(启用、禁用等)需要做大量的前期工作。

但是如果你真的在考虑你的标记和 CSS 的架构，我不确定你为什么会选择“类内颜色名称”这条路。