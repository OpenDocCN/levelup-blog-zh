# Python 集:基础和用例

> 原文：<https://levelup.gitconnected.com/python-sets-basics-and-usecases-af1fbe8906f4>

![](img/294de9dbfeb513a4d57bd2446f77ff85.png)

凯文·卡纳斯在 [Unsplash](https://unsplash.com/s/photos/python-coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# Python 中的集合

Python 中的集合非常类似于列表。两者都可以定义为项目的集合，但是有一些差异使得集合成为非常有用的数据类型:

*   集合中的每个元素都是唯一的
*   集合中的元素是不可变的，即它们不能被改变，但是集合本身是可变的
*   集合是无序的

# 基础

**1。创建器械包:**

在 python 中，用大括号括起来的逗号分隔的项目列表表示一个集合。

```
set_of_numbers = {1,2,3,4,5}
mixed_set = {'a',1,2,'c','d'}
```

定义空集有点棘手，因为在 python 中，空花括号`{}`用于定义字典。所以我们使用`set()`函数来定义一个集合。此功能也可用于从列表中创建集合。

```
empty_set = set()
set_from_list = set([1,2,3,4])
```

**2。向集合添加元素:**

`add()`和`update()`方法分别用于向集合中添加单个或多个元素。Update 方法将字符串以及列表、元组、集合等可迭代对象作为输入。

```
demo_set = set()demo_set.add(2)
demo_set.update([2,4,5])
demo_set.update((1,3))
print(demo_set)--------------------------------------------------------------------
{1,2,3,4,5}
```

**3。从集合中移除元素:**

`discard()`和`remove()`的方法都可以用来去除元素。这两者之间的唯一区别是，如果要移除的元素不在集合中，remove 方法将引发错误。

```
demo_set = {1,2,3,4,5}demo_set.remove(2)
demo_set.discard(3)
print(demo_set)--------------------------------------------------------------------
{1,4,5}
```

类似地，有一个叫做`pop()`的方法可以从集合中随机删除一个元素。而`clear()`方法可以用来清除一个集合中的所有元素，使其成为一个空集。

**4。从集合中访问元素:**

为了检查一个元素是否存在于一个集合中，我们可以使用下面的语法，它将返回 true 或 false。

```
demo_set = {1,2,3,4,5}print(2 in demo_set)
print('a' in demo_set)--------------------------------------------------------------------
True
False
```

我们也可以使用 for 循环来循环访问集合。

```
demo_set = {1,2,3}for element in demo_set:
    print(element)--------------------------------------------------------------------
1
2
3
```

# 集合操作

在 python 中使用集合的主要好处是 python 提供的数学集合运算，如集合并、交集、差、对称差等。

**1。设置并集:**

集合并集将创建包含两个集合中所有元素的集合。可以使用`union()`方法或`|`运算符执行并集。

```
set_a = {1,2,3}
set_b = {3,4,5}set_c = set_a.union(set_b) 
#OR
set_c = set_a | set_bprint(set_c)--------------------------------------------------------------------
{1,2,3,4,5}
```

**2。设置交叉点:**

集合交集将创建一个集合，其中包含两个集合中的所有公共元素。可以使用`intersection()`方法或`&`运算符进行相交。

```
set_a = {1,2,3}
set_b = {3,4,5}set_c = set_a.intersection(set_b) 
#OR
set_c = set_a & set_bprint(set_c)--------------------------------------------------------------------
{3}
```

**3。设置差异:**

集合差异将创建一个集合，其中所有元素都在集合 1 中，而不在集合 2 中。可以使用`difference()`方法或`-`操作器进行区别。

```
set_a = {1,2,3}
set_b = {3,4,5}set_c = set_a.difference(set_b) 
#OR
set_c = set_a - set_bprint(set_c)--------------------------------------------------------------------
{1,2}
```

还有一种叫做`difference_update()`的方法，用两个集合的差异来更新集合。

```
set_a = {1,2,3}
set_b = {3,4,5}set_a.difference_update(set_b)print(set_a)--------------------------------------------------------------------
{1,2}
```

**4。设置对称差:**

集合对称差将创建一个集合，其元素同时存在于两个集合中，但不包括两个集合共有的元素。可以使用`symmetric_difference()`方法或`^`运算符执行对称差分。

```
set_a = {1,2,3}
set_b = {3,4,5}set_c = set_a.symmetric_difference(set_b) 
#OR
set_c = set_a ^ set_bprint(set_c)--------------------------------------------------------------------
{1,2,4,5}
```

类似地，还有一个叫做`symmetric_difference_update()`的方法，它用两个集合的对称差来更新集合。

```
set_a = {1,2,3}
set_b = {3,4,5}set_a.symmetric_difference_update(set_b) print(set_a)--------------------------------------------------------------------
{1,2,4,5}
```

**5。不相交:**

`isdisjoint()`如果两个集合中没有公共元素，方法返回 True，否则返回 False。

```
set_a = {1,2,3}
set_b = {4,5}print(set_a.isdisjoint(set_b))--------------------------------------------------------------------
True
```

6。是子集:

如果集合 2 包含集合 1 中的所有元素，方法返回 True，否则返回 False。

```
set_a = {1,2,3}
set_b = {1,2,3,4,5}print(set_a.issubset(set_b))--------------------------------------------------------------------
True
```

7。是超集:

`issuperset()`如果集合 1 包含集合 2 中的所有元素，方法返回 True，否则返回 False。

```
set_a = {1,2,3,4,5}
set_b = {1,2,3}print(set_a.issuperset(set_b))--------------------------------------------------------------------
True
```

# 使用案例

python 中有许多真实的集合用例，下面列出了几个。

**1。检查列表中是否存在元素:**

为了检查一个元素是否出现在列表中，我们总是可以使用 for 循环来迭代和检查，或者我们也可以使用 in 操作符。

```
list_a = [1,2,3,4,5]
for i in list_a: 
    if(i == 2) : 
        print Trueprint(2 in set_a)--------------------------------------------------------------------True
True
```

但是更有效的方法是将列表转换成集合，并使用 in 操作符检查是否存在。

```
list_a = [1,2,3,4,5]
set_a = set(list_a)print(2 in set_a)--------------------------------------------------------------------
True
```

**2。列出独特的元素:**

要创建一个所有列表元素都是唯一的列表，我们只需将列表转换成集合，然后再转换回列表。

```
list_a = [1,5,2,3,3,4,5,1]
set_a = set(list_a)
list_a = list(set_a)print(list_a)--------------------------------------------------------------------
[1, 2, 3, 4, 5]
```

**3。如果一个或多个元素是公共的，则合并集合:**

要合并具有公共元素的集合，我们可以使用`isdisjoint()`方法检查集合是否不相交，或者使用`intersection()`方法检查交集，如果两个集合相交，我们可以使用`union()`方法合并集合。

如果我们有一个这样的集合列表，那么我们必须使用循环和递归来合并所有的集合。

```
def merge_if_intersect(m_list):
    for i,set1 in enumerate(m_list):
        for j,set2 in enumerate(m_list[i+1:],i+1):
           if not set1.isdisjoint(set2): #or set1.intersection(set2)
              m_list[i]=set1.union(m_list.pop(j))
              return merge_if_intersect(m_list)
    return m_listlist_a = [{1,2,3},{4,5},{5,6},{3,7},{8}]merged_list = merge_if_intersect(list_a)
print(merged_list)--------------------------------------------------------------------
[{1, 2, 3, 7}, {4, 5, 6}, {8}]
```

# 参考

*   [在 python 中设置-geeksforgeeks](https://www.geeksforgeeks.org/sets-in-python/)
*   [设置在 python-stack bus](https://stackabuse.com/sets-in-python/)
*   [关于 Python 中的集合你应该知道的 10 件事](https://towardsdatascience.com/10-things-you-should-know-about-sets-in-python-9902828c0e80)