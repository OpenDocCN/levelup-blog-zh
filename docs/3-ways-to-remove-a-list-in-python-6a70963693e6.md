# 在 Python 中从列表中移除项目的 3 种方法

> 原文：<https://levelup.gitconnected.com/3-ways-to-remove-a-list-in-python-6a70963693e6>

![](img/3e71cdacbf8eac943cfe2ed5f3ebf846.png)

彼得·诺伊曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Python 中有三种移除列表元素的方法:pop()、remove()和 del()。这三种方法都有不同的目的，决定使用哪一种可能会令人困惑。在这篇博文中，我们将解释这些方法之间的区别，并帮助您决定何时使用每一种方法。

# 流行()

Pop()是一种从列表中移除元素的方法。它有一个参数，即要移除的元素的索引。Pop()返回被移除的元素，所以如果您想将它存储在一个变量中，您可以这样做:

```
my_list = [0, “a”, “b”, “c”]```removed_element = my_list.pop(0)print(my_list) # [“a”, “b”, “c”]print(removed_element) # 0
```

# 移除()

Remove()是一个从列表中移除元素的方法。它有一个参数，即您想要移除的元素。与 pop()不同，remove()不返回被移除的元素。这可能会令人困惑，因为如果您尝试将移除的元素存储在变量中，它将只是空的:

```
my_list = [0, “a”, “b”, “c”]my_list.remove(“a”)print(my_list) # [0, “b”, “c”]removed_element = my_list.remove(“b”)print(removed_element) # None
```

# 德尔()

Del 是关键字，不是方法。Del 可以用来从列表中移除一个元素或者删除一个变量。在列表中使用时，它有一个参数，即要删除的元素的索引:

```
my_list = [0, “a”, “b”, “c”]del my_list[0]print(my_list) # [“a”, “b”, “c”]
```

当用于变量时，它会删除变量:

```
my_variable = “hello”del my_variableprint(my_variable) # NameError: name ‘my_variable’ is not defined
```

如你所见，del 是一个非常强大的关键字。使用时要小心，因为它不能撤销！

现在您已经知道了 pop()、remove()和 del()之间的区别，您应该能够决定在任何给定的情况下使用哪一个。如果您想从列表中移除一个元素并将其存储在一个变量中，请使用 pop()。如果只想从列表中移除元素而不存储它，请使用 remove()。如果你想删除一个变量，使用 del。

希望这有所帮助！

## 在网上和我联系！

*   [和我一起工作](https://digyt.co)
*   [个人博客](http://www.christopherclemmons.com/)
*   电子邮件:christopher.clemmons2020@gmail.com