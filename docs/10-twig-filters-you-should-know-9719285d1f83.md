# 你应该知道的 10 个小过滤器

> 原文：<https://levelup.gitconnected.com/10-twig-filters-you-should-know-9719285d1f83>

![](img/2ad7b730693d439692f2c0b7dedf6e61.png)

照片由[赛·基兰·阿纳加尼](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 以一种简单优雅的方式修改你的小枝变量

如果你以前使用过 Twig，你可能已经知道过滤器，并且你可能经常使用它们。过滤器非常强大，允许您以简单而优雅的方式修改变量。有很多可用的过滤器，可能你还不知道所有的过滤器。

这正是我们将在本文中查看过滤器列表的原因。但在我们继续之前，我想让你知道，你可以在[twigfiddle.com](https://twigfiddle.com/)上摆弄这些滤镜。这个网站提供了一个小的在线游乐场，你可以在那里摆弄树枝。这可以省去在本地机器上设置环境的麻烦。

事不宜迟，让我们直接进入你应该知道的 10 个树枝过滤器。

# 1.系统默认值

*默认*过滤器返回指定的默认值，如果该值未定义或为空，则返回该值，否则返回变量值。该过滤器不仅适用于变量，也适用于对象和数组。

```
{{ name|default('No name defined') }}

{{ person.age|default('Age is not defined') }}

{{ person['age']|default('Age is not defined') }}
```

# 2.nl2br

`nl2br`代表*换行到 br* ，这意味着这个过滤器在一个字符串中的所有换行符之前插入 HTML 换行符。所有的新行(`\n`)都被一个 HTML `<br>`标签所取代。

```
{{ "Hey, how are you?\nJust fine."|nl2br }}*{# outputs*
Hey, how are you?*<br />*
 *Just fine.*
*#}*
```

# 3.生的

默认情况下，当启用自动转义时，Twig 中的所有内容都会被转义。如果你不想对一个变量进行转义，你必须显式地将它标记为安全的，这可以通过使用 *raw* 过滤器来实现。仅当原始滤镜是应用于该滤镜的最后一个滤镜时，这种方法才有效。

例如，当您想要呈现包含 HTML 的变量时，此过滤器会很有用。在这种情况下，您不希望转义变量，而是按原样呈现它。

```
{{ data|raw }} *{# The data variable won't be escaped #}*
```

# 4.日期

*日期*过滤器将日期变量格式化为给定的格式。您可以将所需的格式作为第一个参数传入。第二个参数是可选的，用于指定时区。

```
{{ updated_at|date("d/m/Y") }}
{{ "now"|date("d/m/Y") }}
{{ updated_at|date("d/m/Y", "Europe/Amsterdam") }}
```

# 5.一批

*批处理*过滤器分割列表列表中的一个列表——每个子列表被称为一个*批处理*。第一个参数指定批处理的大小。第二个参数是一个占位符，用于填充缺失的项目。

```
{% set names = ['John', 'Lisa', 'Frank', 'Ted'] %}

<table>
{% for row in names|batch(3, 'No name') %}
    <tr>
        {% for column in row %}
            <td>{{ column }}</td>
        {% endfor %}
    </tr>
{% endfor %}
</table>
```

这是渲染后的样子:

```
<table>
    <tr>
        <td>John</td>
        <td>Lisa</td>
        <td>Frank</td>
    </tr>
    <tr>
        <td>Ted</td>
        <td>No name</td>
        <td>No name</td>
    </tr>
</table>
```

# 6.替换

*替换*过滤器通过替换占位符来格式化给定的字符串。替换过滤器有一个参数，它是一个对象。键指定需要替换什么，相应的值指定需要替换什么。

注意，使用 *%* 作为分隔符完全是惯例，因此是可选的。

```
{{ "This is %name% and my hobby is %hobby%."|replace({'%name%': "Fred", '%hobby%': hobby}) }}
*{# This is Fred and my hobby is tennis. #}*{{ "I have a dog"|replace({'dog': 'cat'}) }}
*{# I have a cat #}*
```

# 7.第一

*第一个*过滤器返回序列、映射或字符串的第一个元素。

```
{{ ['a', 'b', 'c', 'd']|first }}
*{# outputs a #}*

{{ { a: 1, b: 2, c: 3, d: 4 }|first }}
*{# outputs 1 #}*

{{ 'abcd'|first }}
*{# outputs a #}*
```

就像第一个*滤波器和最后一个*滤波器一样。**

```
{{ ['a', 'b', 'c', 'd']|last }}
*{# outputs d #}*

{{ { a: 1, b: 2, c: 3, d: 4 }|last}}
*{# outputs 4 #}*

{{ 'abcd'|last}}
*{# outputs d #}*
```

# 8.无限的

*无空格*过滤器移除 HTML 标签之间的空格。注意它**没有**删除 HTML 标签中的空白。

这个标签是为了避免 HTML 标签之间的额外空白，以避免浏览器在某些情况下呈现异常。这并不意味着将生成的 HTML 的大小保持在最小。

```
"<div>
        <label>Some text</label>
    </div>
"|spaceless }}

*{# output will be <div><label>Some text</label></div> #}*
```

# 9.轮次

*舍入*过滤器将数字舍入到给定的精度。第一个参数是精度，默认值为 0。第二个参数是舍入方法，默认为 common。其他可能的舍入方法有*天花板*和*地板*。

```
{{ 14.65|round }}
*{# outputs 15#}*

{{ 14.65|round(1, 'floor') }}
*{# outputs 14.6 #}*{{ 14.65|round(1, 'ceil') }}
*{# outputs 14.7 #}*
```

# 10.键

*keys* 过滤器返回一个数组的键，当你想要迭代一个数组的键时，这个过滤器很方便。

```
{% for key in items|keys %}
    ...
{% endfor %}
```