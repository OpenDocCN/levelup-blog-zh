# 通过集合库引入 Python 中的高性能数据类型

> 原文：<https://levelup.gitconnected.com/introducing-high-performance-datatypes-in-python-with-the-collections-library-3d8c334827a5>

## 艺术收藏…蟒蛇艺术！

![](img/624c5c0f9a471970fc2e614315e6643a.png)

来看看 Python 精彩的艺术收藏吧！

> 想获得灵感？快来加入我的 [**超级行情快讯**](https://www.superquotes.co/?utm_source=mediumtech&utm_medium=web&utm_campaign=sharing) 。😎

Python 最大的优势之一是它广泛的模块和包选择。这些将 Python 的功能扩展到许多热门领域，包括机器学习、数据科学、web 开发、前端等等。其中最好的一个是 Python 内置的*集合* *模块*。

一般来说，Python 中的*集合*是用来存储数据集合的容器，比如 list、dict、tuple 和 set。这些容器内置于 Python 中，可以开箱即用。 [*集合模块*](https://docs.python.org/2/library/collections.html) 提供了额外的、高性能的数据类型，可以增强您的代码，使事情变得更加简洁和容易。

让我们浏览一下 collections 模块最流行的数据类型以及如何使用它们的教程！

# (1)计数器

**计数器**是字典对象的子类。collections 模块中的`Counter()`函数接受一个 iterable，比如一个 list 或 tuple，并返回一个计数器字典。字典的键将是 iterable 的唯一元素，每个键的值将是 iterable 中元素的计数。

首先，让我们从集合中导入`Counter`数据类型:

```
from collections import Counter
```

要创建一个`Counter`对象，将它赋给一个变量，就像你对任何其他对象类所做的那样。你想传递给它的唯一参数是你的 iterable。

```
lst = [1, 2, 3, 3, 2, 1, 1, 1, 2, 2, 3, 1, 2, 1, 1]
counter = Counter(lst)
```

如果我们在对象`print(counter)`周围使用一个简单的打印函数打印出我们的计数器，我们将得到看起来有点像字典的东西:

```
Counter({1: 7, 2: 5, 3: 3})
```

您可以使用如下所示的键来访问任何计数器项目。这与从标准 Python 字典中提取元素的方式完全相同。

```
lst = [1, 2, 3, 3, 2, 1, 1, 1, 2, 2, 3, 1, 2, 1, 1]
counter = Counter(lst)
print(counter[1])
```

## 最常见的()函数

到目前为止，`Counter`对象最有用的函数是`most_common()`函数。当它应用于一个`Counter`对象时，它返回一个列表，列出了`*N*`个最常见的元素及其数量，从最常见到最不常见。

```
lst = [1, 2, 3, 3, 2, 1, 1, 1, 2, 2, 3, 1, 2, 1, 1]
counter = Counter(lst)
print(counter.most_common(2))
```

上面的代码打印出以下元组列表:

```
[(1, 7), (2, 5)]
```

每个元组的第一个元素是列表中的唯一项，每个元组的第二个元素是计数。这是一种快速简单的方法，比如“获取列表中最常见的前 3 个元素及其数量”。

要阅读更多关于计数器功能的信息，请查阅[官方文档](https://docs.python.org/2/library/collections.html#collections.Counter)。

# (2)违约声明

**defaultdict** 的工作方式和普通的 python 字典完全一样，额外的好处是当你试图访问一个不存在的键时，它不会抛出错误。

相反，它用默认值初始化密钥。默认值是根据创建`defaultdict`对象时作为参数传递的数据类型自动设置的。请看下面的代码示例。

```
*from* collections *import* defaultdict

names_dict = defaultdict(int)
names_dict["Bob"] = 1
names_dict["Katie"] = 2
sara_number = names_dict["Sara"]
print(names_dict)
```

在上面的例子中，`int`作为默认值被传递给我们的`defaultdict`对象。接下来，为每个键定义值，即“Bob”和“Katie”的数字。但是在最后一行中，我们试图访问一个尚未定义的键，即“Sara”的键。

在普通的字典中，这将抛出一个错误。但是使用`defaultdict`，一个新的键被自动初始化为“Sara ”,值为 0，对应于我们的`int`数据类型。因此，最后一行打印出包含所有 3 个名称和相应值的字典。

```
defaultdict(<class 'int'>, {'Bob': 1, 'Katie': 2, 'Sara': 0})
```

如果我们用一个类似于`names_dict = defaultdict(list)`的列表来初始化我们的`defaultdict`，那么“Sara”会用一个空列表`[]`来初始化，代码会打印出如下内容:

```
defaultdict(<class 'int'>, {'Bob': 1, 'Katie': 2, 'Sara': []})
```

要了解更多关于 defaultdict 的功能，请查看官方文档。

# (3)得缺

[*队列*](https://en.wikipedia.org/wiki/Queue_(abstract_data_type)) 是计算机科学中遵循先入先出(FIFO)原则的基本数据结构。简单地说，这意味着添加到队列中的第一个对象也必须是第一个被删除的对象。我们只能在队列的前面插入东西，并且只能从后面移除东西——队列中间没有动作。

*集合*库的**队列**实现了精确功能的优化版本。该实现的一个关键特性是保持队列的大小——也就是说，如果您将队列的最大大小设置为 10，那么`deque`将根据 FIFO 原则添加和删除元素，以保持最大大小为 10。这是目前 Python 中最好的队列实现。

先说个例子。我们将创建一个`deque`对象，然后用从 1 到 10 的整数初始化它。

```
*from* collections *import* deque

my_queue = deque(maxlen=10)

*for* i *in* range(10):
    my_queue.append(i+1)

print(my_queue)
```

在上面的代码中，我们首先初始化了我们的`deque`，指定我们希望它总是保持最大长度 10。第二，我们通过一个循环将值插入到我们的队列中。请注意填充队列的方式与我们处理常规 Python 列表的方式完全相同。最后，我们打印出结果。

```
deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], maxlen=10)
```

因为我们的队列有一个`maxlen=10`并且我们的循环增加了 10 个元素，所以我们的队列包含了从 1 到 10 的所有数字。现在让我们看看当我们添加更多的数字时会发生什么。

```
*for* i *in* range(10, 15):
    my_queue.append(i+1)

print(my_queue)
```

在上面的代码中，我们向队列中添加了另外 5 个元素，数字从 11 到 15。但是我们的队列只有一个`maxlen=10`，所以它必须删除一些元素。因为队列必须遵守 FIFO 原则，所以它删除了插入到队列中的前 5 个元素，精确地按照它们的插入顺序:[1，2，3，4，5]。print 语句的结果如下:

```
deque([6, 7, 8, 9, 10, 11, 12, 13, 14, 15], maxlen=10)
```

要了解更多关于`deque`的功能，请查看[官方文档](https://docs.python.org/2/library/collections.html#collections.deque)。

# (4)命名元组

当您在 Python 中创建一个常规元组时，它的元素是通用的和未命名的。这迫使您记住每个元组元素的确切索引。**名为**的车钩就是这个问题的解决方案。

`namedtuple()`返回一个元组，该元组中每个位置都有固定的名称，还有一个通用名称用于 *namedtuple* 对象。要使用一个`namedtuple`，首先要为它创建一个模板。下面的代码创建了一个名为“Person”的`namedtuple`模板，带有属性“name”、“age”和“job”。

```
*from* collections *import* namedtuple

Person = namedtuple('Person', 'name age job')
```

一旦创建了模板，就可以用它来创建`namedtuple`对象。让我们为两个人创建两个`namedtuple`，并打印出他们的表示。

```
Person = namedtuple('Person', 'name age job')

Mike = Person(name='Mike', age=30, job='Data Scientist')
Kate = Person(name="Kate", age=28, job='Project Manager')

print(Mike)
print(Kate)
```

上面的代码非常简单——我们用`namedtuple` 模板的所有属性初始化一个“人”。上面的打印语句将给出以下结果:

```
Person(name='Mike', age=30, job='Data Scientist')
Person(name='Kate', age=28, job='Project Manager')
```

因此，`namedtuples`使得元组对象的使用、可读性和组织更加容易。

要了解更多关于`namedtuple`的功能，请查看[官方文档](https://docs.python.org/2/library/collections.html#collections.namedtuple)。

# 喜欢学习？

在推特[上关注我，我会在这里发布所有最新最棒的人工智能、技术和科学！也在 LinkedIn](https://twitter.com/GeorgeSeif94) 上与我联系！