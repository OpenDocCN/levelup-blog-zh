# 你能解决这 3 个(看似)简单的 Python 问题吗？

> 原文：<https://levelup.gitconnected.com/can-you-solve-these-3-seemingly-easy-python-problems-2c793967cd2c>

## 解决方案会让你大吃一惊。

![](img/e46e28f79e0aa87a3cef4e945070283e.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1641959) 的 [FreePhotosART](https://pixabay.com/users/FreePhotosART-3184606/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1641959)

试着解决下面的问题，然后检查下面的答案。

所有的问题都有一些共同点，所以在解决剩下的问题之前检查第一个问题的解决方案可以减少挑战。

## **问题 1**

假设我们有几个变量:

```
x = 1
y = 2
l = [x, y]
x += 5a = [1]
b = [2]
s = [a, b]
a.append(5)
```

打印`l`和`s`会产生什么结果？

[跳转到解决方案](#707e)

## 问题 2

让我们定义一个简单的函数:

```
def f(x, s=set()):
    s.add(x)
    print(s)
```

如果你打电话会发生什么？

```
>>f(7)
>>f(6, {4, 5})
>>f(2)
```

?

[跳转到解决方案](#aa97)

## 问题 3

让我们定义三个简单的函数:

```
def f():
    l = [1]
    def inner(x):
        l.extend([x])
        return l
    return innerdef g():
    y = 1
    def inner(x):
        y += x
        return y
    return innerdef h():
    l = [1]
    def inner(x):
        l += [x]
        return l
    return inner
```

以下命令的结果会是什么？

```
>>f_inner = f()
>>print(f_inner(2))>>g_inner = g()
>>print(g_inner(2))>>h_inner = h()
>>print(h_inner(2))
```

[跳转到解决方案](#42ad)

你对自己的答案有多自信？让我们看看你是不是对的。

## **问题 1 的解决方案**

```
>>print(l)
[1, 2]>>print(s)
[[1, 5], [2]]
```

为什么第二个列表会对其第一个元素`a.append(5)`的变化做出反应，而第一个列表却完全忽略了类似的变化`x+=5`？

## 问题 2 的解决方案

让我们看看会发生什么:

```
>>f(7)
{7}>>f(6, {4, 5})
{4, 5, 6}>>f(2)
{2, 7}
```

等等，最后输出的不应该是`{2}`吗？

## 问题 3 的解决方案

产出如下:

```
>>f_inner = f()
>>print(f_inner(2))
[1, 2]>>g_inner = g()
>>print(g_inner(2))
UnboundLocalError: local variable ‘y’ referenced before assignment
```

为什么`g_inner(2)`不输出`3`？为什么`f()` 的内部函数记得它的外部作用域，而`g()`的内部函数却不记得？他们实际上是一样的！

# 说明

让我们来探讨一下 Python 中**可变**和**不可变**对象的区别。

**像列表、集合或字典**这样的可变对象**可以就地改变**(变异)。**不可变对象**如整数、字符串和元组**不能**——这种对象的“改变”导致新对象的创建。

## 问题 1 的解释

```
x = 1
y = 2
l = [x, y]
x += 5a = [1]
b = [2]
s = [a, b]
a.append(5)>>print(l)
[1, 2]>>print(s)
[[1, 5], [2]]
```

由于`x`是不可变的，操作`x+=5`不会改变原来的对象，而是创建一个新的对象。列表的第一个元素仍然指向原始对象，因此它的值保持不变。

如果是可变对象`a`，`a.append(5)`改变了原来的对象，因此 list `s`会“看到”这种改变。

## 问题 2 的解释

```
def f(x, s=set()):
    s.add(x)
    print(s)>>f(7)
{7}>>f(6, {4, 5})
{4, 5, 6}>>f(2)
{2, 7}
```

前两个输出总的来说是有意义的:首先将`7`的值添加到默认的空集，产生`{7}`，然后将`6`的值添加到`{4, 5}`的集合，产生`{4, 5, 6}`。

但是接下来奇怪的事情发生了:`2`的值不是添加到默认的空集，而是添加到`{7}`的一个集合。为什么？**可选参数 s 的默认值仅评估一次**——仅在第一次调用期间`s`将被初始化为空集。由于`s`在调用`f(7)`后**是可变的**,所以它被就地修改。第二次调用`f(6, {4, 5})`不会影响默认参数——所提供的设置`{4, 5}`隐藏了它，换句话说`{4, 5}`是一个不同的变量。第三次调用`f(2)`使用了第一次调用中使用的相同的`s`变量，但是 s 没有被重新初始化为一个空集——而是使用了它之前的值`{7}`。

这就是为什么**你不应该使用可变的默认参数**。在这种情况下，应按照以下方式修改函数:

```
def f(x, s=None):
 **if s is None:
        s = set()**
    s.add(x)
    print(s)
```

## 问题 3 的解释

```
def f():
    l = [1]
    def inner(x):
        l.extend([x])
        return l
    return innerdef g():
    y = 1
    def inner(x):
        y += x
        return y
    return innerdef h():
    l = [1]
    def inner(x):
        l += [x]
        return l
    return inner>>f_inner = f()
>>print(f_inner(2))
[1, 2]>>g_inner = g()
>>print(g_inner(2))
UnboundLocalError: local variable ‘y’ referenced before assignment>>h_inner = h()
>>print(h_inner(2))
UnboundLocalError: local variable ‘l’ referenced before assignment
```

在这个问题中，我们处理**闭包**——内部函数记得它们的封闭名称空间在定义时的样子。

那么发生了什么？如果您只比较前两个函数的行为，您可能会再次认为差异与作为可变列表的`l`和作为不可变字符串的`x`有关。

查看带有函数`h`的示例，可以看出还有其他的东西。这里就像在函数`f`中一样，我们试图改变一个可变列表，但是这次我们失败了。你可能会认为操作`l.extend([x])`和
`l += [x]`是等价的，但实际上它们之间有一个微妙的区别:在幕后`l.extend`将导致编译器调用`LOAD_DEREF`指令，该指令将加载非局部列表`l`(然后列表将被修改)，而使用`l += [x]`编译器将调用`LOAD_FAST`，该指令在内部函数的局部范围内寻找变量`l`并因此失败。

为了避免使用`UnboundLocalError`，你可以使用`nonlocal`关键字:

```
def g():
    y = 1
    def inner(x):
        **nonlocal** y
        y += x
        return y
    return innerdef h():
    l = [1]
    def inner(x):
        **nonlocal** l
        l += [x]
        return l
    return inner>>g_inner = g()
>>print(g_inner(2))
3>>h_inner = h()
>>print(h_inner(2))
[1, 2]
```

# **结论**

这里有几条建议:

注意可变变量和不可变变量之间的区别。

**不要使用可变的默认参数。**

**在内部函数中改变闭包变量时要小心。使用 nonlocal 关键字声明变量不是局部的。**

请随意分享您的回答中由于误用可变和不可变对象而导致的潜在问题的其他例子。如果你想了解可变和不可变对象在列表复制中扮演什么角色，请查看[我的下一篇文章](/five-ways-to-copy-a-list-in-python-f6f179d9785b)。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)