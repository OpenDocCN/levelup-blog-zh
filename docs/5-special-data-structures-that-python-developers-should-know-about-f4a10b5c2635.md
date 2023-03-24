# Python 开发人员应该了解的 5 种特殊数据结构

> 原文：<https://levelup.gitconnected.com/5-special-data-structures-that-python-developers-should-know-about-f4a10b5c2635>

作为 Python 开发人员，您必须熟悉 Python 的内置数据结构，例如:

*   词典
*   目录
*   一组
*   元组

但是，还有许多其他方法可以更好地组织您的数据，并帮助您的下一个项目。

来说说他们吧！

![](img/da90ff30dd8f40fc4bd0889624ab6987.png)

由 [Boitumelo Phetla](https://unsplash.com/@writecodenow?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 在`collections`模块中提供了许多专门的数据容器。

为了使用它们，我们从`collections`模块中导入它们。

下面就来探讨这些吧！

# 链式地图

这是一套有序的词典。

**定义链图**

```
from collections import ChainMaplocation_info = {'shop_no': '1', 'street':'London Road'}
menu_info = {'coffee_1' : 'Latte', 'coffee_1':'Flat White'}
shop_dim_info = {'length':'200m','width':'200m'}coffee_shop_data = ChainMap(location_info, menu_info, shop_dim_info)
```

**使用键**访问数值

像字典一样在链表中查找该值。

返回与第一个找到的键相关联的值。

```
print(coffee_shop_data['coffee_1'])#Output: 'Latte'
```

**使用键**修改数值

修改与第一个找到的关键字相关联的值。

```
coffee_shop_data['coffee_1'] = 'Mocha'print(coffee_shop_data['coffee_1'])#Output: 'Mocha'
```

![](img/c48905678e0b0c8db69930db4e736542.png)

由[玛丽亚·李森科](https://unsplash.com/@manunalys?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 双端队列

它是“双端队列”的简称。

它在内部是一个双向链表。

当一个新元素从其开始插入/弹出时，时间复杂度为 O(n)的列表。

与此相反，deque 以 O(1)的时间复杂度执行追加并从自身的任一侧弹出。

**定义一个队列**

```
from collections import dequecompany_names = deque()
```

**向右添加数值**

```
company_names.**append**('Tesla')
company_names.**append**('SpaceX')print(company_names)#Output: deque(['Tesla','SpaceX'])
```

**向左添加值**

```
company_names.**appendleft**('DeepMind')print(company_names)#Output: deque(['DeepMind','Tesla','SpaceX'])
```

**从右侧移除数值**

```
company_names.**pop**()#Output: deque(['DeepMind','Tesla'])
```

**从左侧移除数值**

```
company_names.**popleft**()#Output: deque(['Tesla'])
```

![](img/6db555d6650a383784c37b2e672486bc.png)

托马斯·博尔曼斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 默认字典

当访问或修改字典中缺失/无效的键-值对时，会抛出一个`KeyError`。

这会终止代码执行，必须处理这种行为以避免这种情况。

*简单的解决方案？*

一个`defaultdict`的功能就像一个字典，但是当一个人试图访问或修改一个丢失/无效的键时，会返回一个默认值而不是`KeyError`。

**定义默认字典**

我们使用关键字`lambda`定义了一个`defaultdict`及其缺省值。

```
from collections import defaultdict

coffee_shop = defaultdict(**lambda:'No key found'**)
```

**添加键值对**

这可以像在字典中一样完成。

```
coffee_shop['name'] = 'Outdoor Coffee'
coffee_shop['location'] = 'London'
```

**使用键**访问数值

这可以像在字典中一样再次完成。

```
print(coffee_shop['name'])#Output: 'Outdoor Coffee'
```

**访问带有无效关键字的值**

这将返回如上所述的默认值，而不是像“标准”字典那样返回一个`KeyError`。

```
print(coffee_shop['price'])#Output: 'No key found'
```

![](img/b06d65a6699e57283405ad39a361e8bc.png)

照片由[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 计数器

这种数据结构使得计算另一个对象中的值对我们来说非常容易！

让我们以一个包含一堆项目的列表为例:

```
items = ['shoe', 'flower','pen','pencil','pen','clock','pencil','shoe','pencil','pen','clock','clock','pencil','shoe']
```

可以使用包含如下计数的循环和字典来计数项目的数量:

```
items_counter = {}for item in items:
    if item not in items_counter:
        items_counter[item] = 1
    else:
        items_counter[item] += 1 print(item_counter)#Output: {'shoe': 3, 'flower': 1, 'pen': 3, 'pencil': 4, 'clock': 3}
```

`Counter`数据结构帮助我们跳过下面提到的这个麻烦。

```
from collections import Counteritems_counter = Counter(items)print(items_counter)#Output: Counter({'pencil': 4, 'shoe': 3, 'pen': 3, 'clock': 3, 'flower': 1})
```

*简单！*

![](img/3cc15afaff849ffe2c6426012f81a89e.png)

照片由 [Djim Loic](https://unsplash.com/@loic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# **命名元组**

与 Python 中的常规元组不同，命名元组帮助我们为元组中的每个位置赋予意义。

使用命名元组，可以按名称访问字段，而不是像在元组中那样按位置索引。

**定义命名元组**

可以使用以下约定`namedtuple(*typename*, *field_names)*` 来定义。

```
from collections import namedtupleCoffeeData = namedtuple('CoffeeData', ['type','price'])
```

上面，我们已经定义了一个`namedtuple`的实例，带有一个`CoffeeData`的`typename`以及代表我们的元组数据的标签的`fieldnames`。

让我们如下定义新创建的命名元组的一个实例。

```
coffee = CoffeeData('Latte',2.45)
```

我们将这款咖啡的`type`打印如下:

```
print(coffee.type)#Output: 'Latte'
```

我们将这款咖啡的`price`打印如下:

```
print(coffee.price)#Output: 2.45
```

![](img/41b0aab1087c0f088e2e096dbe82c936.png)

斯科特·古默森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

文章到此结束。

*感谢阅读！*

*如果你是 Python 或编程的新手，可以看看我的新书《Python 学习指南》*[](https://bamaniaashish.gumroad.com/l/python-book)*****下面:*****

****[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)****