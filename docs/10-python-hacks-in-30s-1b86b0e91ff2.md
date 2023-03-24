# 30 秒内 10 次 Python 攻击

> 原文：<https://levelup.gitconnected.com/10-python-hacks-in-30s-1b86b0e91ff2>

![](img/204ba902c42de4a0ff114bad97502f18.png)

## 1.检查列表中的所有元素是否相同

```
def all_equal(lst):
 """
 It returns True if all the elements in the list are equal, and False otherwise

 :param lst: The list to check
 :return: True or False
 """
 return lst[1:] == lst[:-1]

all_equal([1, 2, 3, 4, 5, 6]) # False
all_equal([1, 1, 1, 1]) # True
```

# 2.检查列表中所有元素的唯一性

```
def all_unique(lst):
 """
 If the length of the list is the same as the length of the set of the list, then all the elements in
 the list are unique.

 :param lst: a list of values
 :return: True or False
 """
 return len(lst) == len(set(lst))

x = [1,2,3,4,5,6]
y = [1,2,2,3,4,5]
all_unique(x) # True
all_unique(y) # False
```

# 3.基于过滤器将一个列表分成两个列表

```
def bifurcate(lst, filter):
    """
    It takes a list and a filter, and returns a list of two lists, the first containing all the elements
    of the original list that match the filter, and the second containing all the elements that don't

    :param lst: The list to be bifurcated
    :param filter: a list of boolean values
    :return: A list of two lists. The first list contains all the elements of the original list that
    correspond to a True value in the filter list. The second list contains all the elements of the
    original list that correspond to a False value in the filter list.
    """
    return [
        [x for i, x in enumerate(lst) if filter[i] == True],
        [x for i, x in enumerate(lst) if filter[i] == False]
    ]
```

# 4.在一个列表中查找元素，但在另一个列表中不查找

```
def difference(a, b):
    """
    It returns a list of all elements in a that are not in b

    :param a: the list of all the words in the corpus
    :param b: the list of words to be removed from the vocabulary
    :return: A list of items in a that are not in b.
    """
    _b = set(b)
    return [item for item in a if item not in _b]

difference([1, 2, 3], [1, 2, 4]) # [3]
```

# 5.展平嵌套列表

```
def flatten(lst):
    """
    For each element in the list, for each element in that element, add that element to the new list.

    :param lst: the list to be flattened
    :return: A list of all the elements in the list of lists.
    """
    return [x for y in lst for x in y]

flatten([[1,2,3,4],[5,6,7,8]]) # [1, 2, 3, 4, 5, 6, 7, 8]

# flatten infinitely nested lists
def flatten(lst):
    """
    It takes a list of lists and returns a flattened list.

    :param lst: a list of lists
    :return: a flattened list
    """
    return [x for y in lst for x in flatten(y)] if type(lst) is list else [lst]
flatten([[1,2,[3,4]],[5,6,7,8]]) # [1, 2, 3, 4, 5, 6, 7, 8]
```

# 6.将数字转换成数字列表

```
def digitize(n):
    """
    It converts an integer into a list of its digits

    :param n: The number to be digitized
    :return: A list of the digits of the number n.
    """
    return list(map(int, str(n)))

digitize(123) # [1, 2, 3]
```

# 7.无序列表

```
from random import randint
from copy import deepcopy

def shuffle(lst):
    """
    We create a copy of the list, then we iterate through the list, and for each element, we swap it
    with a random element in the list

    :param lst: the list to be shuffled
    :return: A list of the same length as the input list, but with the elements in a random order.
    """
    temp_lst = deepcopy(lst)
    m = len(temp_lst)
    while (m):
        m -= 1
        i = randint(0, m)
        temp_lst[m], temp_lst[i] = temp_lst[i], temp_lst[m]
    return temp_lst

foo = [1,2,3]
shuffle(foo) # [2,3,1] , foo = [1,2,3]
```

# 8.如果在范围内或最近的边界，则返回数字

```
def clamp_number(num, a, b):
    """
    Return the number if it's between a and b, otherwise return the closest of a or b.

    :param num: the number to be clamped
    :param a: the minimum value of the range
    :param b: the number of bits in the binary representation of the number
    :return: the maximum of the minimum of the number and the maximum of a and b, and the minimum of a
    and b.
    """
    return max(min(num, max(a, b)), min(a, b))

clamp_number(2, 3, 5) # 3
clamp_number(1, -1, -5) # -1
```

# 9.查找字符串字节大小

```
def byte_size(string):
    """
    It returns the length of the string in bytes

    :param string: The string to get the length of
    :return: The length of the string in bytes.
    """
    return len(string.encode('utf-8'))

byte_size(' ') # 4
byte_size('Hello World') # 11
```

# 10.最大公约数

```
def gcd(numbers):
    """
    It takes a list of numbers and returns the greatest common divisor of all the numbers in the list

    :param numbers: a list of numbers
    :return: The greatest common divisor of the numbers in the list.
    """
    return reduce(math.gcd, numbers)

gcd([8,36,28]) # 4
```

# 结论:

以上是 10 个 python 技巧，希望它能引起你对一些 python 基础的兴趣。如果有更多的出现，我很乐意添加更多。

请随意查看我关于数据工程的其他文章——[用几个 python 脚本进行 ETL](https://medium.com/@caopengau/data-engineering-made-easy-attached-python-scripts-to-head-startyour-etl-tasks-960e766f3ae3),使用 Python 类升级您的[数据工程，并在您的 Python 数据管道中添加](https://medium.com/gitconnected/using-python-class-for-data-engineering-5edc4c3c9132)[数据验证](https://medium.com/gitconnected/python-data-pipeline-first-and-foremost-step-data-validation-e15017b7ef8d)。

# 进一步阅读

*   [71 个用于平台工程的 Linux 命令](https://blog.devgenius.io/71-linux-commands-for-platform-engineering-563cf8f16c5)
*   [用 Python 破解 Wifi 密码的分步指南](/a-step-by-step-guide-to-crack-wifi-password-with-python-461a714d82ce)
*   [30 秒内 10 次 Javascript 破解](/10-javascript-hacks-in-30-seconds-a9b0213971bd)
*   [5 分钟 10 个设计图案](/10-design-patterns-in-5-minutes-a8643b1e1b1)

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，在 medium 上访问我和所有其他优秀作家的所有优质文章。