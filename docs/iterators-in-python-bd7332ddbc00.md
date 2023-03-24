# Python 中的迭代器

> 原文：<https://levelup.gitconnected.com/iterators-in-python-bd7332ddbc00>

## 蟒蛇皮短裤——第二部分

![](img/c39087c39630340e645006c36730f128.png)

克里斯·里德在 [Unsplash](https://unsplash.com/s/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Python 中的迭代器是一个可以迭代的对象——也就是说，它有一定数量的元素，我们可以循环遍历这些元素。

例如，这个概念用于 for 循环:

```
my_list = [0, 1, 2, 3, 4]
for el in my_list:
    print(el)
```

在底层，一个迭代器需要实现函数`__iter__`和`__next__`，我们稍后将对此进行研究。

许多其他函数，如`list()`或`sum()`，都使用迭代器:

```
print(sum(my_list))
```

# 在幕后

现在让我们更深入一点，理解幕后发生了什么——并实现我们自己的迭代器。

如上所述，迭代器需要实现函数`__iter__`和`__next__`。`__iter__`类似于常用类的构造函数(`__init__`)，但是需要返回迭代器本身。然后`__iter__`实际上用于遍历对象，每次调用返回一个元素——如果没有更多的元素可用，就引发一个`StopIteration()`。

让我们通过定义一个返回第一个`n`平方数的迭代器来展示这一点:

```
class SquareIterator:
    def __init__(self, n):
        self.i = 0
        self.n = n

    def __iter__(self):
        return self

    def __next__(self):
        if self.i < self.n:
            i = self.i
            self.i += 1
            return i**2
        else:
            raise StopIteration()
```

正如所承诺的，我们现在可以做所有我们可以用“默认”迭代器做的事情，比如:

```
for x in SquareIterator(5):
    print(x)

print(sum(SquareIterator(5)))
```

这篇文章是快速展示重要 Python 概念系列文章的一部分。你可以在这里找到其他部分:

*   第 1 部分:[Python 中的 Lambda 函数](https://medium.com/@hrmnmichaels/lambda-functions-in-python-a124cebc2e99)
*   第 3 部分:[Python 中的生成器和生成器表达式](https://medium.com/@hrmnmichaels/generators-and-generator-expressions-in-python-a8d2e700945e)
*   第 4 部分:[使用 enumerate()和 zip()在 Python 中进行高级迭代](https://medium.com/@hrmnmichaels/advanced-iteration-in-python-with-enumerate-and-zip-676b31ceac44)
*   第 5 部分:[用上下文管理器管理 Python 中的资源(带语句)](https://medium.com/@hrmnmichaels/managing-resources-in-python-with-context-managers-with-statement-f07afc1afb4f)
*   第 6 部分:[用 Python 生成临时文件和目录](https://medium.com/@hrmnmichaels/generating-temporary-files-and-directories-in-python-dfc11f017a97)
*   第 7 部分:[用 Python 登录](https://medium.com/@hrmnmichaels/logging-in-python-3df84ce78cef)
*   第八部分:[Python 中的部分函数](https://medium.com/gitconnected/partial-functions-in-python-66998eef1384)
*   第 9 部分:[Python 中的 f 字符串](https://medium.com/gitconnected/f-strings-in-python-80e30c64fb95)