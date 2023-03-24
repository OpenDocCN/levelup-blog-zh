# 作为面试问题的 Python 编程练习

> 原文：<https://levelup.gitconnected.com/programming-exercises-in-python-as-examples-of-interview-questions-d0ae6fbefffc>

## Python 编程测试示例

![](img/60aa980c1f856a0944c634b16c1b4256.png)

Unsplash 中的图像

在面试中，为了评估你的编码风格、技能和思维方式，经常会被要求用 Python(或任何其他编程语言)构建一些函数。让我们举一些例子:

假设我们有一系列 N 个滚动骰子结果，取值从 1 到 6。假设您的输入将是类似“56611166626634416”的结果序列，构建以下函数:

# 问题 1

在试验中，有多少次恰好两个 6 一前一后被掷出？例如，在序列“ **56611166626634416** ”中出现了两次，恰好两个 6 相继抛出。

# 解决方案 1

```
def question_1(myseq):
    N = len(myseq)
    if N<=1:
        return(0)

    if N==2 and myseq=='66':
        return(1)

    count_6s = 0
    for d in range(len(myseq)-2):
        if d>0 and myseq[d:d+2]=='66' and myseq[d+2]!='6' and myseq[d-1]!='6':
            count_6s+=1
    # check for the last two elements
    if myseq[-2:]=='66' and myseq[-3:-2]!='6':
        count_6s+=1

    # check for the first two elements for sequences greater than 2
    if len(myseq)>2 and myseq[0:2]=='66' and myseq[2]!='6':
        count_6s+=1

    return(count_6s)
```

# 问题 2

求连续掷骰子序列中最长的一个的长度，其中不出现数值 6。(如果只掷出 6，这个数字可以为零。).例如，在试验“ **66423612345654** 中，不出现数值 6 的连续掷骰的最长子序列是 12345。它的长度是 5。

# 解决方案 2

```
def question_2(myseq):

    # if all the elements are equal to 6 then return 0
    if all(x=='6' for x in list(myseq)):
        return(0)
    # if all the elements are not equal to 6 then return the length of the sequence
    if all(x!='6' for x in list(myseq)):
        return(len(myseq))

    # The successive rolls end when the 6 occurs and that is why we store these positions
    end_of = []
    for i, d in enumerate(list(myseq)):
        if d=='6':
            end_of.append(i)

    # add the position of the last elemnt in case is not 6
    if myseq[-1]!=6:
        end_of.append(len(myseq))
    # then we get the first differences and finally the max
    lenlist = [end_of[i+1]-end_of[i]-1 for i in range(len(end_of)-1)]

    # add the first difference of the first element
    lenlist.append(end_of[0])

    return(max(lenlist))
```

# 问题 3

如果只有 5 和 6，我们就把试掷中的一系列连续掷骰子称为幸运系列。比如“**6556665”**是幸运系列，长度为 7。了解一下，幸运系列最常出现的长度是哪个？如果有一个以上的“最频繁”幸运系列长度，打印最长的。如果试用中没有幸运系列，则打印零。

```
def question_3(myseq):
    # if all the elements are less than 5 then return 0
    if all(int(x)<5 for x in list(myseq)):
        return(0)
    # if all the elements are greater than 4 then return the length of the sequence
    if all(int(x)>4 for x in list(myseq)):
        return(len(myseq))

    # The successive rolls end when a roll is less than 5
    end_of = []
    for i, d in enumerate(list(myseq)):
        if int(d)<5:
            end_of.append(i)

    # add the position of the last elemnt in case is not 5 or 6
    if int(myseq[-1])>4:
        end_of.append(len(myseq))

    # get the length of each sequence
    lenlist = [end_of[i+1]-end_of[i]-1 for i in range(len(end_of)-1)]

    # add the first difference of the first element
    lenlist.append(end_of[0])

    # ignore the 0 lengths
    lenlist = [el for el in lenlist if el!=0]

    # get the most frequenct length and in case of draw return the longest using the sort
    output = max(sorted(lenlist, reverse=True), key=lenlist.count)
    return(output)
```

# 例子

我们将提供以下三个序列的三个示例:

```
eg1 **=** "616161666"
eg2 **=** "456116513656124566"
eg3 **=** "56611166626634416"
```

对于每个问题(1，2，3)，我们得到每个示例的以下输出

```
print("Example 1")
# Print for the example 1
print(question_1(eg1))
print(question_2(eg1))
print(question_3(eg1))print("\nExample 2")
# Print for the example 2
print(question_1(eg2))
print(question_2(eg2))
print(question_3(eg2))print("\nExample 3")
# Print for the example 2
print(question_1(eg3))
print(question_2(eg3))
print(question_3(eg3))
```

输出:

```
Example 1
0
1
1Example 2
1
4
3Example 3
2
4
3
```

# 外卖

在一些面试过程中，要准备好面对这类问题。请注意，上述解决方案并未针对速度和效率进行优化。有指示性的解决办法。欢迎在下面的评论中提供你的解决方案！

*原载于*[*https://predictivehacks.com*](https://predictivehacks.com/interview-questions-vol-1/)*。*

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。