# 用 Python 生成序列的最快方法

> 原文：<https://levelup.gitconnected.com/the-fastest-way-to-generate-a-sequence-in-python-a61da7f87852>

## 您已经知道的 Python 方法之间的细微差别

![](img/c3fb3ffcba0fe1049f98a05f3539f4e6.png)

科技版 WOC 的照片

在整个大学期间，我会在数学、应用数学和物理课上听到讲师重复说“剥猫皮有几种方法”。尽管我觉得马克·吐温的这句话有些荒谬和暴力，但它(奇怪的是)提醒我，要实现一个目标，总有许多可能的途径。当我决定在编码中使用哪些方法时，这给了我很大的帮助。

在我早期，我所知道的就是 *range()* 作为一种在 Python 中生成单调递增(或递减)序列的方法。然后我被介绍给了 *xrange()* 和 *numpy.arange()* 这让我开始嗯，作为一个新手，决定使用哪些功能的策略是什么？

## 1.内置方法:range()

内置方法 *range(* [ *start* ，】 *stop* ，[ *step* ] *)* 是我第一次在 Python 中介绍生成序列。函数中的可选参数显示在方括号中。 *range()* 方法生成一个不可变的对象，它是一个数字序列。通过将 *range()* 对象包含在 list 方法中，可以很容易地将其转换为 Python 列表，例如，创建一个介于 10 ( *start* )和 0 ( *stop* )之间的值列表，以 2 为增量递减( *step* )。

```
>>list(range(10,0,-2))
[10, 8, 6, 4, 2]
```

这种方法已经被证明在 for 循环和 list comprehensions 中非常有用，因为它是内置且高效的。为了测量它的速度，我编写了这个非常简单的脚本，它给出了执行 range(10)的运行时的一个大样本。运行时(rt)的大范围分布提供了一个公平的方法来衡量平均创建一个 *range()* 对象的速度。

```
rt=[]for _ in range(1000000):
    t1 =time.time()
    seq = range(val)
    rt.append(time.time()-t1)rt_mean = np.mean(rt)*1.e9
rt_std  = np.std(rt)*1.e9print(u'range() has an average runtime of %.0f \u00B1 %.0f ns'%(rt_mean,rt_std))
```

这个简短的脚本以纳秒为单位提供了平均运行时间和 1σ不确定性(或标准偏差)。人们可以使用神奇的方法%timeit 来获得类似的度量。运行这个脚本给我们提供了:

```
range() has an average runtime of 478 ± 739 ns
```

看起来够快了，但是有更快的选择吗？

## 2.Python 2 的内置方法:xrange()

另一个这样的内置方法你可能在切换到 Python 3 之前已经发现了，就是*xrange(*[*start*，] *stop* ，[ *step* ] *)。它输出一个生成器对象。像 *range()* 一样，它在循环中很有用，也可以转换成列表对象。使用与前面类似的测试，我们可以测量它的平均运行时间，结果如下:*

```
xrange() has an average runtime of 370 ± 682 ns
```

给定误差范围， *range()* 和 *xrange()* 的执行速度相当相似。主要区别在于输出——一个由 *range()* 给出的 iterable 和一个由 *xrange()输出的 generator 对象。*比较两个对象之间的内存使用情况，

```
>> print('Memory use of iterable: %d'%(sys.getsizeof(range_seq)) )
Memory use of iterable: 152>> print('Memory use of generator object: %d'%(sys.getsizeof(xrange_seq)) )
Memory use of generator object: 40
```

虽然在效率上没有差别，但是 *xrange()* 对象的字节大小要小得多。我们可以考虑的另一个序列构建器是存在于 NumPy 库中的序列构建器。

## 3.NumPy 方法:numpy.arange()

numpy 的方法*NumPy . arange(***[***start***，】** *stop* **，[** *step* **，]** *dtype=None)* 提供了生成序列的功能。它生成一个 NumPy 数组对象(或 *numpy.ndarray* )。运行类似的脚本来确定平均运行时产量，

```
numpy.arange() has an average runtime of 1111 ± 958 ns
```

根据该测试， *numpy.arange()* 的平均执行时间是 *range()* 和 *xrange()* 的两倍以上。

由于 *xrange()* 被限制在 Python 2 中，并且 *numpy.arange()* 在生成序列时比 *xrange()* 和 *range()* 慢两倍，答案似乎很清楚。方法 *range()* 是在 Python 中生成单调递增或递减序列的最有效的方法。虽然这可能是真的，但是每种方法产生的对象消耗内存的方式不同。

# 警告——每种方法的输出

的确， *range()* 执行比 *numpy.arange()* 快，输出一个可以转换成 iterable list 的对象。另一方面， *numpy.arange()* 给你一个 *numpy.ndarray* 。毫无疑问， *range()* 在 for 循环中是更好的选择。然而，当涉及到对结果序列进行操作时，NumPy 数组在内存消耗和速度方面都有明显的优势。

从内存开始，我们可以发现一个 Python list 对象和 NumPy 数组消耗了多少内存。

```
>> print('Memory use of iterable: %d'%(sys.getsizeof(range_seq)) )
Memory use of iterable: 152>> print('Memory use of numpy.ndarray: %d '%(sys.getsizeof(arange_seq)) )
Memory use of numpy.ndarray: 80
```

NumPy 数组在编译器内存存储中占用的空间更少，使得它们的操作效率更高。比较在*列表*和*numpy . ndar arrays*上执行基本算术运算的速度，可以很容易地测试这一点，正如这篇 [UCF 网络课程文章](https://webcourses.ucf.edu/courses/1249560/pages/python-lists-vs-numpy-arrays-what-is-the-difference)中所展示的。

总而言之，在一个人的 Python 之旅中发现给猫剥皮的许多方法，可以帮助理解不同数据类型和方法的速度、功能和内存使用的重要性。最重要的是，编写简短的测试来比较运行时可以帮助人们辨别哪些方法会增加代码的整洁度和效率。

## 其他参考资料:

[1][‘极客为极客’的帖子。标题:“Python 中的 range()vs xrange()”](https://www.geeksforgeeks.org/range-vs-xrange-python/)

[2][‘极客为极客’的帖子。标题:“Python | range()不返回迭代器”](https://www.geeksforgeeks.org/python-range-does-not-return-an-iterator/)

[3] [Reddit 在 subreddit 上的帖子: *r/learnpython。*题目:*“range 和 arange 的区别？”*](https://www.reddit.com/r/learnpython/comments/3g0hiz/difference_between_range_and_arange/)