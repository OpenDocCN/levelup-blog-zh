# Python 数字格式二进制类型

> 原文：<https://levelup.gitconnected.com/python-number-formatting-binary-type-3cc722dd95d2>

![](img/0dd9de8b1a621f0ccf701e4cc2978d53.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在完成了一个涉及流行语言之一 Python 的二进制格式的小型项目后，我在研究可以使用的不同方法时遇到了相当大的困难。他们到处都是！所以，我试着把我发现的不同方法汇编成这篇短文。

这里是我发现自己在 Python 中使用了很多次的一些方法。

最简单的方法是将整数转换成字符串类型的二进制形式:

```
>> bin(int)          # return "0bXXX"
# It will return a bunch of 0s and 1s with 0b in front
```

另一个例子如下。当他们使用方法`format()`时，他们看起来彼此相似，但是书写方式不同:

```
>> “{0:b}”.format(int) 
# It will return a bunch of 0s and 1s (return XXX)
>> format(int, 'b') 
# It will return a bunch of 0s and 1s (return XXX)
>> format(int, "0b") 
# It will return a bunch of 0s and 1s (return XXX)
```

以下是上述方法的示例:

```
>> bin(2)                   # Output: 0b10
>> format(2, 'b')           # Output: 10
>> format(2, "0b")          # Output: 10
>> “{0:b}”.format(2)        # Output: 10
```

为了转换回十进制，只需使用这个最简单的一个:

```
int(x, base)
# int(x,2)
```

目前就这些。我希望这篇文章能帮助一些正在寻找不同转换方式的人。感谢您的阅读！

如果你喜欢我写的东西，请在下面鼓掌，分享给别人看。请在下面的评论区分享你自己的二进制转换方式！

你也可以在我的[社交媒体](https://twitter.com/Ded_busterZ)或我的[博客](http://deddytandean.com/dyslog/)上关注我。