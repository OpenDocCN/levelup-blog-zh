# Python 中的错误和异常处理

> 原文：<https://levelup.gitconnected.com/errors-and-exception-handling-in-python-e3560a879119>

## 学习 Python 中错误和异常处理的指南

![](img/bdbbcbb690156721f8d0388cd88f477e.png)

穆斯塔法·梅拉吉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

编写代码时，错误是必然会发生的，但是可以进行错误处理来为这些错误做准备。通常，当出现错误时，脚本会停止工作。但是错误处理可以用来让脚本继续运行，即使有错误。

为此，我们需要了解 3 个关键词。它们如下:

**1)试试**

这是要运行的代码块，可能会导致错误

**2)除:**

如果 try 块中出现错误，将执行这段代码。

**3)最后:**

不管有没有错误，这段代码都将被执行。

所以让我们假设一个函数“求和”,它将两个数相加。该功能运行正常。

```
**>>> def** summation(num1,num2):
        print(num1**+**num2)>>> summation(2,3)
5
```

现在让我们从用户那里获取一个输入，然后运行这个函数。

```
>>> num1 **=** 2
>>> num2 **=** input("Enter number: ")
Enter number: 3>>> summation(num1,num2)>>> print("This line will not be printed because of the error")
**---------------------------------------------------------------------------**
**TypeError**                                 Traceback (most recent call last)
**<ipython-input-6-2cc0289b921e>** in <module>
**----> 1** summation**(**num1**,**num2**)**
      2 print**("This line will not be printed because of the error")**

**<ipython-input-1-970d26ae8592>** in summation**(num1, num2)**
      1 **def** summation**(**num1**,**num2**):**
**----> 2** print**(**num1**+**num2**)**

**TypeError**: unsupported operand type(s) for +: 'int' and 'str'
```

所以我们得到一个错误“TypeError ”,因为我们试图添加一个数字和一个字符串。请注意，错误发生后，后面的代码行将不会被执行。所以我们使用上面提到的关键字来确保即使发生错误，脚本也能运行。

```
**>>> try**:
        summed **=** 2 **+** '3'
    **except**:
        print("Summation is not of the same type")Summation is not of the same type
```

所以在这里我们可以看到，当 try 块中出现错误时，它会转到 except 并打印语句。现在让我们添加“else”块，以防没有遇到错误。

```
**>>> try**:
        summed **=** 2 **+** 3
    **except**:
        print("Summation is not of the same type")
    **else**:
        print("There was no error and result is: ",summed)There was no error and result is:  5
```

现在让我们用另一个例子来理解这一点，在这个例子中，我们也提到了 except 块的错误。如果你没有在 except 块中提到错误类型，它将为所有的异常假定错误类型。

```
**>>> try**:
        f **=** open('test','w')
        f.write("This is a test file")
    **except** TypeError:
        print("There is a type error")
    **except** OSError:
        print("There is an OS error")
    **finally**:
        print("This will print even if no error")This will print even if no error
```

现在创建一个错误来查看 except 块是否与 finally 块一起工作

```
**>>> try**:
        f **=** open('test','r')
        f.write("This is a test file")
    **except** TypeError:
        print("There is a type error")
    **except** OSError:
        print("There is an OS error")
    **finally**:
        print("This will print even if no error")There is an OS error
This will print even if no error
```

参考笔记本[此处](https://github.com/jayashree8/Python_guide/blob/master/Python%20errors%20and%20exception%20handling/Errors%20and%20exception%20handling%20in%C2%A0Python.ipynb)。

## 学习 Python 可以参考的入门书籍:

[](https://amzn.to/3yDY4To) [## Python 速成班，第二版:基于项目的编程入门实践

### 世界上最畅销的 Python 书籍的第二版。一个快速的，没有废话的 Python 编程指南…](https://amzn.to/3yDY4To) [](https://amzn.to/3vtvQZv) [## 艰难地学习 Python:一个非常简单的介绍可怕的美丽世界…

### 你会学习 Python！Zed Shaw 完善了世界上最好的学习 Python 的系统。遵循它，你会…](https://amzn.to/3vtvQZv) [](https://amzn.to/3urluYI) [## 思考 Python，2e:如何像计算机科学家一样思考

### 思考 Python，2e:如何像计算机科学家一样思考](https://amzn.to/3urluYI) 

## 学习 Python 可以参考的高级书籍:

[](https://amzn.to/3fMzVBn) [## 编程 Python:强大的面向对象编程

### 如果你已经掌握了 Python 的基础，你就可以开始使用它来完成真正的工作了。编程 Python 将…](https://amzn.to/3fMzVBn) [](https://amzn.to/34oFFMl) [## 高级 Python 编程:使用以下工具构建高性能、并发和多线程应用

### 关键特性使用 Dask 和 PySpark Master 技能在集群上设置和运行分布式算法，以准确地…](https://amzn.to/34oFFMl) 

> *联系我:* [*LinkedIn*](https://www.linkedin.com/in/jayashree-domala8/)
> 
> *查看我的其他作品:* [*GitHub*](https://github.com/jayashree8)