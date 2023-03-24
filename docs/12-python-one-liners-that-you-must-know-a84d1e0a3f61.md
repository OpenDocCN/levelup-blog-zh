# 你必须知道的 12 个 Python 单句程序

> 原文：<https://levelup.gitconnected.com/12-python-one-liners-that-you-must-know-a84d1e0a3f61>

## Python 一行程序帮助您更快更有效地编码

![](img/8b548ccfa728fbaea382cd67c3f8c1d6.png)

Alessandro Bianchi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种流行的高级编程语言，以其简单性和多功能性而闻名。Python 的一个关键特性是能够编写简洁的单行语句，这些语句可以用很少的代码完成很多事情。

这些简洁的单行语句通常被称为“一行程序”，它们对于快速有效地解决问题非常有用。

一行程序也是学习和体验 Python 语言的一种很棒的方式。因为它们短小精悍，所以您可以快速尝试不同的想法并了解语言是如何工作的，这可以帮助您更深入地理解其功能和特性。

下面是几个 Python 中的一行程序示例，展示了该语言的一些高级技术和功能:

## 1.在没有额外变量的情况下交换两个变量

这段代码可以用来交换两个值，而不需要额外的变量。

```
a, b = 5, 10

#Method 1
a, b = b, a

#Method 2
def swap(a,b):
  return b,a
swap(a,b)
```

简单又容易，不是吗？

## 2.列出理解

List comprehension 用于在一行代码中基于现有列表的元素创建一个新列表。

它创建一个新列表，该列表只包含现有列表中满足特定条件的元素。

```
def get_vowels(string):
    return [vowel for vowel in string if vowel in 'aeiou'] #list comprehension

print("Vowels are:", get_vowels('This is some random string'))

Output:
Vowels are:  ['i', 'i', 'o', 'e', 'a', 'o', 'i']
```

## 3.列表中的最大值或最小值

要找到列表中的最大值或最小值，我们可以使用 python 中的`max()`或`min()`函数。

```
list1 = [1,4,2,7,11,6]

max_element = max(list1)
min_element = min(list1)

print(max_element)
print(min_element)

Output:
11
1
```

## 4.检查字符串列表是否只包含大写字符串

```
strings = ['HELLO', 'WORLD', 'FOO', 'BAR']

# Check if all elements in the list are uppercase strings
all_uppercase = all(s.isupper() for s in strings)

# Output: False
print(all_uppercase)
```

这个一行程序使用`all()`函数和一个生成器表达式来检查`strings`列表中的所有元素是否都是大写字符串。

生成器表达式`s.isupper() for s in strings`遍历`strings`列表，对每个元素`s`应用`isupper()`方法，并返回一个生成结果布尔值的生成器对象。

## 5.在字典列表中查找最大值

```
data = [{'name': 'Alice', 'age': 25}, {'name': 'Bob', 'age': 30}, {'name': 'Charlie', 'age': 20}]

# Find the maximum age value in the list
max_age = max(d['age'] for d in data)

print(max_age)
# Output: 30
```

这个一行程序使用`max()`函数和一个生成器表达式来查找`data`列表中的最大年龄值。

生成器表达式`d['age'] for d in data`遍历`data`列表，从每个字典`d`中提取`'age'`值，并返回生成年龄值的生成器对象。

## 6.将词典列表合并成一个词典

我们可以使用`(**)`操作符将多个字典合并成一行。我们需要在{}中传递字典以及(**)操作符，这样就完成了。

```
dictionary1 = {"name": "Jeff", "age": 26}
dictionary2 = {"name": "Jeff", "city": "New York"}

merged_dict = {**dictionary1, **dictionary2}

print("Merged dictionary is:", merged_dict)

# Output:
Merged dictionary: {'name': 'Joy', 'age': 25, 'city': 'New York'}
```

## 7.找到两个列表的交集

```
list1 = [1, 2, 3, 4]
list2 = [3, 4, 5, 6]

# Find the intersection of the lists using the set() and & operator
result = set(list1) & set(list2)

print(result)
# Output: {3, 4}
```

这个一行程序使用`set()`函数和交集操作符`&`来寻找两个列表的交集。

`set()`函数从指定的 iterable 创建一个新的 set 对象，交集操作符返回一个新的 set 对象，其中包含两个集合共有的元素。

## 8.检查文件是否存在

在进行文件处理和任何其他操作时，有必要知道我们在代码中使用的文件是否存在。

使用这个代码片段，我们可以知道文件是否存在于我们的目录或给定的路径中。

```
from os import path

def check_for_file():
    print("Does file exist:", path.exists("data.csv"))

if __name__=="__main__":
    check_for_file()
```

## 9.找出两个列表的区别

```
list1 = [1, 2, 3, 4]
list2 = [3, 4, 5, 6]

# Find the difference between the lists using the set() and - operator
difference = set(list1) - set(list2)

# Output: {1, 2}
print(difference)
```

这个一行程序使用`set()`函数和差分运算符`-`来查找两个列表之间的差异。

`set()`函数从指定的 iterable 创建一个新的 set 对象，difference 操作符返回一个新的 set 对象，其中包含第一个集合中存在但第二个集合中没有的元素。

## 10.检查一个列表是否是另一个列表的置换

```
list1 = [1, 2, 3, 5]
list2 = [3, 2, 1, 5]

# Check if the lists are permutations of each other by comparing their sorted versions
is_permutation = sorted(list1) == sorted(list2)

# Output: True
print(is_permutation)
```

这个一行程序使用`sorted()`函数和等式操作符`==`来检查一个列表是否是另一个列表的置换。

`sorted()`函数从指定 iterable 的元素中返回一个新的排序列表，相等操作符比较两个列表的元素是否相等。

## 11.检查字典列表是否包含特定的键值对

```
data = [{'name': 'Alice', 'age': 25}, 
        {'name': 'Bob', 'age': 30}, 
        {'name': 'Charlie', 'age': 20}]

# Check if any dictionary in the list has the key 'name' with the value 'Bob'
has_name = any(d.get('name') == 'Bob' for d in data)

# Output: True
print(has_name)
```

这个一行程序使用`any()`函数和一个生成器表达式来检查`data`列表中的任何字典是否有值为`'Bob'`的键`'name'`。

生成器表达式`d.get('name') == 'Bob' for d in data`遍历`data`列表，使用`get()`方法从每个字典`d`中检索`'name'`键的值，并返回生成结果布尔值的生成器对象。

## 12.统计特定元素在列表中出现的次数

```
numbers = [1, 2, 3, 4, 1, 2, 3, 4, 1, 2]

# Count the number of occurrences of the element 2 in the list
count = numbers.count(2)

# Output: 3
print(count)
```

这个一行程序使用`count()`方法来计算元素`2`在`numbers`列表中出现的次数。

`count()`方法返回指定元素在列表中出现的次数。

## **结论**

这些只是用 Python 中的一行程序可以做的许多事情中的几个例子。随着您继续学习和尝试这种语言，您会发现有许多其他的任务只需要几行代码就可以完成。

感谢阅读！

> *你走之前……*

如果你喜欢这篇文章，并希望**继续关注**更多**精彩的**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。