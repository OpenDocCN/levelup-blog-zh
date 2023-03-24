# 当 Python 陷阱导致错误结果时

> 原文：<https://levelup.gitconnected.com/when-a-python-gotcha-leads-to-wrong-results-2447f379fdfe>

## 一个奇怪的难以调试的带有舍入数字的 Python 故障

![](img/37bc24ec8d755f3a620f2f34f142cf75.png)

作者照片

我志愿担任 [Dataquest](https://www.dataquest.io/) 的社区版主。有一次，我在回答一位学员提出的一个看似简单的问题。任务是写一个函数`divide_apples`,给定两个数字(苹果和人),返回每个人的苹果数量和剩下的苹果数量。该学生编写了下面的函数(为了更好的可读性，我对它进行了轻微的重构，并更改了变量名):

```
def divide_apples(num_apples, num_people):    
    apples_each = num_apples // num_people
    apples_divided_strictly = num_apples / num_people
    leftovers_per_person = apples_divided_strictly - apples_each
    apples_left = int(leftovers_per_person * num_people)
    return apples_each, apples_left
```

这可能不是最直接的解决方案(稍后，我们将看到一个更简洁的方案)，但无论如何，所有步骤似乎在技术上都是正确的。事实上，它在几个输入对苹果-人的情况下工作得很好，但是在 37 个苹果和 7 个人的情况下出现了问题:

```
num_apples = 37
num_people = 7print(divide_apples(num_apples, num_people))**Output:** (5, 1)
```

但很明显，这种情况下剩下的苹果数应该是 2，而不是 1。为了调试代码，让我们再添加两个从我们的函数返回的值:`apples_divided_strictly`(当剩余的苹果以外科医生的精度被切开，并且这些苹果块被平均分配给所有参与者)和`leftovers_per_person`。然后，我们将再次对 37 个苹果和 7 个人运行修改后的函数:

```
def divide_apples(num_apples, num_people):    
    apples_each = num_apples // num_people
    apples_divided_strictly = num_apples / num_people
    leftovers_per_person = apples_divided_strictly - apples_each
    apples_left = int(leftovers_per_person * num_people)
    return apples_each, apples_left, apples_divided_strictly, leftovers_per_person 

print(divide_apples(num_apples, num_people))**Output:** (5, 1, 5.285714285714286, 0.2857142857142856)
```

我们来看看`apples_divided_strictly`的结果值。如果我们用高精度计算器[将 37 除以 7，我们会得到一个循环十进制数:5。**285714**285714285714285714285714(此处精度为 31)。在我们的例子中，Python 采用了这个值并将其四舍五入到 16 位小数:5.285714285714286。现在，让我们跳过为什么正好是 16 的问题，这里重要的是它正确地做了。](https://apfloat.appspot.com/)

下一步是计算`leftovers_per_person`，这里我们期望从`apples_divided_strictly`(也是 5)的整个部分中提取整数(5)。该操作的逻辑结果是`leftovers_per_person`的小数部分，即 0.285714285714286。然而，`leftovers_per_person`的**实际**值略有不同:

```
Expected value: 0.285714285714286
Actual value:   0.2857142857142856
```

我们看到实际值多了一位小数。**更重要的是:**考虑到计算该值的初始循环十进制数 5.285714285714，该值在数学上舍入有误！记住这一点，让我们看看计算`apples_left`的那条线:

```
apples_left = int(leftovers_per_person * num_people)
```

试着计算括号中的代码段，包括`leftovers_per_person`的值(预期值和实际值)以及我们遇到技术故障的 7 个人:

```
print(0.285714285714286 * 7)
print(0.2857142857142856 * 7)**Output:**
2.0000000000000018
1.9999999999999991
```

现在我们可以清楚地看到问题是如何发生的！当然，`int(1.9999999999999991)=1`，所以我们的函数会返回剩下的 1 个苹果，给定 37 个苹果和 7 个人。否则，使用正确舍入的值`leftovers_per_person`(预期值)，我们将得到 2 个苹果，这是正确的答案。

然而，这仍然不是故事的结尾。Python 如此糟糕地舍入了这个值，难道我们只是运气不好吗？

让我们做一个小实验。数字 0.285714285714285714 也是一个循环十进制数，它实际上是`leftovers_per_person`的值，我们可以(这次是正确的)舍入到任何小数点。的确，我们的实验会是在不同的小数点上四舍五入(比如从 18 个小数点开始递减)，再乘以 7(同样的 7 个人)。对于每个舍入值，我们将执行两次该操作:数学上的和“python 化的”。您可以在[计算器](https://apfloat.appspot.com/)中运行每个运算，检查下面报告的数学结果。

```
print(  f'1\. Python: {0.285714285714285714*7} \n   Maths:  1.999999999999999998\n'
      f'\n2\. Python: {0.28571428571428571*7} \n   Maths:  1.99999999999999997\n'    
      f'\n3\. Python: {0.2857142857142857*7} \n   Maths:  1.9999999999999999\n'
      f'\n4\. Python: {0.285714285714286*7} \n   Maths:  2.000000000000002\n'
      f'\n5\. Python: {0.28571428571429*7} \n   Maths:  2.00000000000003\n'
      f'\n6\. Python: {0.2857142857143*7} \n   Maths:  2.0000000000001\n'
      f'\n7\. Python: {0.285714285714*7} \n   Maths:  1.999999999998\n'
      f'\n8\. Python: {0.28571428571*7} \n   Maths:  1.99999999997\n'
      f'\n9\. Python: {0.2857142857*7} \n   Maths:  1.9999999999\n'
      f'\n10.Python: {0.285714286*7} \n   Maths:  2.000000002\n')**Output:** 1\. Python: 2.0 
   Maths:  1.999999999999999998

2\. Python: 2.0 
   Maths:  1.99999999999999997

3\. Python: 2.0 
   Maths:  1.9999999999999999

4\. Python: 2.0000000000000018 
   Maths:  2.000000000000002

5\. Python: 2.0000000000000298 
   Maths:  2.00000000000003

6\. Python: 2.0000000000001004 
   Maths:  2.0000000000001

7\. Python: 1.9999999999979998 
   Maths:  1.999999999998

8\. Python: 1.99999999997 
   Maths:  1.99999999997

9\. Python: 1.9999999999 
   Maths:  1.9999999999

10.Python: 2.0000000019999997 
   Maths:  2.000000002
```

我们可以做出如下观察:

*   Python 将具有 16 个或更多小数点的值的结果四舍五入为 2.0(例 1–3)。严格地说，相同情况下的数学结果小于 2(因此，它们中每一个的整个部分都是 1)。
*   几乎在所有情况下，Python 舍入都不同于数学舍入。这是由于 Python 在处理浮点数时的不完善。在[这篇教程](https://docs.python.org/3/tutorial/floatingpoint.html)中，你可以读到更多关于它的内容，但简而言之，它试图将十进制数渲染成二进制数，这有时会导致怪异的结果，缺乏精度。
*   即使只考虑**的数学舍入，我们看到对于相同的运算，根据所选的精度，我们会得到一个小于 2 或大于 2 的数。这对于我们在 7 个人之间分配 37 个苹果的初始任务来说是不同的:我们需要获得 2，而不是 1 作为最终的整数值。**

让我们返回到`leftovers_per_person`的**预期**值，即 0.2828528526865 在上面的列表中，它对应于情况 4，对于这种情况，在数学上和 than 化上，我们将收到一个大于 2 的十进制数，因此是一个整数 2，即正确的结果。这意味着在这里我们将是幸运的，只是 Python 再次出错，增加了一个额外的小数点，并且舍入不正确。

幸运的是，我们可以使用[地板划分](https://www.educative.io/edpresso/floor-division)和[模数](https://en.wikipedia.org/wiki/Modulo_operation)以一种无陷阱(也更优雅)的方式修改我们的函数:

```
def divide_apples(num_apples, num_people):
    return num_apples // num_people, num_apples % num_peopleprint(divide_apples(num_apples, num_people))**Output:** (5, 2)
```

# 结论

像本文中讨论的小故障通常很难发现和调试。正如我们所见，有时它们会导致奇怪的结果，从而得出错误的结论。幸运的是，我们找到了解决这个问题的方法。此外，我们探讨了 Python 中舍入的一些潜在限制，以及一般数学中的限制。

感谢阅读！

你可能也会对这些文章感兴趣

[](https://medium.com/geekculture/creating-toyplots-in-python-49de0bb27ec1) [## 在 Python 🧸中创建玩具图

### 高质量的极简交互式可视化，非常适合电子出版

medium.com](https://medium.com/geekculture/creating-toyplots-in-python-49de0bb27ec1) [](https://betterprogramming.pub/read-your-horoscope-in-python-91ca561910e1) [## 如何用 Python 阅读你的星座运势

### 用 Python 找乐子

better 编程. pub](https://betterprogramming.pub/read-your-horoscope-in-python-91ca561910e1) [](https://towardsdatascience.com/the-easiest-ways-to-perform-logical-operations-on-two-dictionaries-in-python-88c120fa0c8f) [## 在 Python 中对两个字典执行逻辑运算的最简单方法

towardsdatascience.com](https://towardsdatascience.com/the-easiest-ways-to-perform-logical-operations-on-two-dictionaries-in-python-88c120fa0c8f)