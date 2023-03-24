# Python 函数-参数和自变量

> 原文：<https://levelup.gitconnected.com/python-functions-parameters-and-args-693d43c98341>

![](img/7c3be547e5b71b83ac1586c394dffc9d.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

这周我一直在阅读关于形参和变量的内容。我知道有五种类型的参数和两种类型的变量。
在本文中，我将通过自定义示例解释以下内容:

*   具有默认值的参数
*   *args 允许我们传入可变数量的位置参数
*   **kwargs 允许我们传入可变数量的关键字参数

![](img/bfdc406111e7ce7186a4a52dfd1f27e2.png)

两艘船停靠在卡塞尔岛的一个停机坪上[戈斯二世]

## 建造一艘星际公民舰的功能:

> [实参和形参有什么区别？](https://docs.python.org/3/faq/programming.html#id15)
> 
> [参数](https://docs.python.org/3/glossary.html#term-parameter)由出现在函数定义中的名称定义，而[参数](https://docs.python.org/3/glossary.html#term-argument)是调用函数时实际传递给函数的值。参数定义了函数可以接受的参数类型。
> - Python 文档

例如，给定函数定义:

```
def star_citizen_ship(**max_speed, adjective=None, **kwargs**):
    pass
```

*max_speed，形容词，*和 *kwargs* 是明星 _ 公民 _ 船的参数。

但是，当调用 star_citizen_ship 时，例如:

```
star_citizen_ship(**'1,347 m/s'**, adjective=**'shiny'**, action=**'racing'**)
```

值 *1，347 米/秒、【闪亮】和*是自变量。

根据 [Python 词汇表](https://docs.python.org/3/glossary.html#term-argument)的说法，参数有两种形式:

> 关键字参数:在函数调用中以标识符(例如`name=`)开头的参数，或者在以`**`开头的字典中作为值传递的参数。
> 
> *位置参数*:不是关键字参数的参数。位置参数可以出现在参数列表的开头和/或作为前面有`*`的[可迭代](https://docs.python.org/3/glossary.html#term-iterable)的元素传递。

![](img/0bc691cac00bec75eaab5f18e91462a2.png)

卡塞尔[高斯 II]方法

## 使用我的 star_citizen_ship 函数

```
def star_citizen_ship(**max_speed**, adjective='shiny',
                      action='zooming', model='Origin 350r'):

    print(f'The max speed of the {model} is {str(max_speed)}.')
    print(f'It can only be described as {adjective} as it is seen       {action} around the Verse.')
```

> 必需的位置参数

上面我定义了一个新函数，第一个参数是**‘max _ speed’**。这是上述函数中唯一的强制参数。必须给出“max_speed ”,以便正确调用该函数。

```
star_citizen_ship(**'1347 m/s'**)
```

输出:

> Origin 350r 的最大速度是 1，347 米/秒。它只能用闪亮来形容，因为它在整个宇宙中缩放。

```
def star_citizen_ship(max_speed, **adjective='shiny'**,
                      **action='zooming', model='Origin 350r'**):
```

> 默认参数

接下来的三个参数**形容词**、**动作和模型**都有默认值。这些参数根本不需要传递给函数。如果没有给出值，那么将使用上面函数定义中给出的值。要覆盖默认值，我们可以调用函数并在关键字后断言一个值，如下所示:

```
star_citizen_ship('1,345 m/s', adjective=**'sleek'**,
                  action='**racing**', model='**Origin M50**')
```

输出:

> Origin M50 的最大速度为 1，345 米/秒。它只能用圆滑来形容，因为它是在宇宙中比赛。

![](img/9c9d21a3dcb98b9d12e4cb375ab4927d.png)

原点 M50

只能通过在参数后添加“/”来使参数具有位置性。

```
def star_citizen_ship(**max_speed, mass, adjective='shiny',/,**
                      action='zooming', model='Origin 350r'):
```

以上参数*‘最大速度’，‘质量’和‘形容词’*现在只是位置性的。

通过在参数前添加一个*号，参数也可以是仅关键字。

```
def star_citizen_ship(max_speed, mass, ***, adjective='shiny',
                      action='zooming', model='Origin 350r'**):
```

*‘形容词’、‘动作’和‘模型’*现在都是关键字。

## *Args 和**Kwargs

可以提供可变数量的位置或关键字参数。例如，如果你想增加船员或者船上有多少挂载点，你可以使用*args。在这里，我使用**kwargs 来添加船只访问的地点:

```
def star_citizen_ship(max_speed, mass, *, adjective='shiny',
                      action='zooming', model='Origin 350r', ***hardpoints, **locations_visited**):
```

“*挂载点”var-positional 将作为元组传入函数，而“* * locations _ visited”var-keyword 参数将作为字典传入。

## 结论

感谢您的阅读。如果你喜欢这篇文章，为什么不看看我的更多内容！以下是一些密切相关的指南和文章:

*   创建一个小的可执行程序，同时了解终端和环境变量！— [链接](https://medium.com/@algakovic/hello-user-executables-and-your-environment-bda87cd412d1)
*   Windows 终端一个多功能的开源终端模拟器— [链接](https://medium.com/datadriveninvestor/windows-terminal-a-flexible-open-source-terminal-emulator-5ff672b4b5e1)
*   Python 中的递归函数— [链接](https://medium.com/swlh/recursive-functions-python-85f6c9e90d24)
*   准备 Python 技能考试？— [链接](https://medium.com/@algakovic/preparing-for-a-python-skills-test-40ff25c26d05)
*   用 Python 使用和调用 API—[链接](https://medium.com/swlh/using-and-calling-an-api-with-python-494a18cb1f44)

![](img/29138176b66d01c03184417ac181bad4.png)

微技术方法

# 来源

1.  [参数和自变量的区别](https://docs.python.org/3/faq/programming.html#what-is-the-difference-between-arguments-and-parameters) — Python 文档
2.  [参数](https://docs.python.org/3/glossary.html#term-parameter) — Python 词汇表
3.  [参数](https://docs.python.org/3/glossary.html#term-argument) — Python 词汇表
4.  [可重复](https://docs.python.org/3/glossary.html#term-iterable) — Python 词汇表