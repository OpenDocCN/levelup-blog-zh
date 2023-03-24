# 每个 Python 开发者都必须知道的 10 个字符串方法

> 原文：<https://levelup.gitconnected.com/10-string-methods-that-every-python-developer-must-know-41b2de1908a6>

Python 有许多内置的字符串方法，当您处理项目时，工具箱中有这些方法非常重要。

![](img/6919b9fdba50aa65093f863ea1557258.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 。下部()

此方法将所有字符串字符都改为小写。

```
name = "Ashish"print(name.lower())**#Output: ashish**
```

## 。上部()

此方法将所有字符串字符都改为大写。

```
name = "Ashish"print(name.upper())**#Output: ASHISH**
```

## 。标题()

此方法将字符串中每个单词的首字母大写。

```
sentence = "i am ashish"print(sentence.title())**#Output: I Am Ashish**
```

![](img/cd06aca143d5a8896f37bd011d0b3f12.png)

照片由 [Surendran MP](https://unsplash.com/@sure_mp?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 。条状()

此方法移除字符串开头和结尾的空格。

```
line = "      Hey!      "print(line.strip())**#Output: Hey!**
```

## 。拆分()

默认情况下，此方法在给定的参数或空格处拆分字符串。

```
line = "This is a blue lion"print(line.split())**#Output: ["This","is","a","blue","lion"]**
```

如果给定一个参数`\n`，一个多行的字符串可以被分成几行。

```
line = """This is a blue lion.
This is a red sea.
This is a pink rabbit."""print(line.split())#Output: ["This is a blue lion.","This is a red sea.","This is a pink rabbit."]
```

![](img/810ddf6f3cccd48b8449eb01a61d253e.png)

照片由 [Dzmitry Tselabionak](https://unsplash.com/@tsellobenok?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 。加入()

该方法连接由分隔符分隔的字符串数组(在下面的示例中为`,`)。

```
arr = ["Sky", "is", "red"]print(",".join(arr))#Output: Sky,is,red
```

## 。查找()

此方法在字符串中搜索指定元素的第一个引用，并返回其索引。

```
text = "The water is green"print(text.find("t"))#Output: 6
```

这个方法的功能与`index()`相似，除了如果在字符串中没有找到元素的话`index()`方法会抛出一个异常。

## 。计数()

此方法返回元素在字符串中出现的次数。

```
text = "I am Ashish. This is Ashish."print(text.count("Ashish"))#Output: 2
```

![](img/2d70c331154bc491c55b4649a8a0903e.png)

照片由 [Emile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 。替换()

此方法将字符串中的一个元素替换为另一个元素。

```
text = "I love Java"

print(text.replace("Java", "Python"))#Output: I love Python
```

## 。伊萨法()

这个方法返回`True`如果一个字符串中的所有字符都是字母，它返回`False`。

```
text = "Python"print(text.isalpha())#Output: Truetext = "Python2022"print(text.isalpha())#Output: False
```

*欲了解更多字符串操作方法，请查看:*

[](https://www.w3schools.com/python/python_ref_string.asp) [## Python 字符串方法

### Python 有一组内置的方法，可以用在字符串上。注意:所有的字符串方法都返回新值。他们确实…

www.w3schools.com](https://www.w3schools.com/python/python_ref_string.asp)  [## 字符串-常见字符串操作- Python 3.10.6 文档

### 源代码:Lib/string.py 这个模块中定义的常量是:描述的和常量的串联…

docs.python.org](https://docs.python.org/3/library/string.html) 

*感谢您阅读这篇文章！*

*如果你是 Python 或编程的新手，可以看看我的新书《Python 学习指南》*[](https://bamaniaashish.gumroad.com/l/python-book)*****下面:*****

****[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)****