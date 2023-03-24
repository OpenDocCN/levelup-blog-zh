# Python 最好的内置库

> 原文：<https://levelup.gitconnected.com/the-best-of-pythons-built-in-libraries-e08495396ef>

## 发现 Python 的宝藏！

![](img/58ff4a419355c3de6a582d9ea5dfbaf1.png)

图片由 [annca](https://pixabay.com/users/annca-1564471/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2014558) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2014558)

> 我为学习者写了一份名为《强大的知识》的时事通讯。每一期都包含链接和最佳内容的关键课程，包括引文、书籍、文章、播客和视频。每一个人都是为了学习如何过上更明智、更快乐、更充实的生活而被挑选出来的。 [**在这里报名**](https://mighty-knowledge.ck.page/b2d4518f88) 。

Python 是一门美丽的语言。简单易用，但表现力很强。但是，您是否使用了它所提供的一切？

每个经验丰富的开发人员都知道，了解他们选择的编程语言的隐藏宝藏有助于他们避开许多常见的错误和日常编码麻烦。

这里有一些直接来自 Python 内置库的珍宝！

# (1)方便的操作系统功能

它使得与您的文件系统*的交互更加容易。*

```
import os### Execute a shell command
**os.system("echo 'My name is bob the builder'")**### Return the current working directory
**os.getcwd()**### List all of the files and sub-directories in a particular folder
**os.listdir("Documents")**### Create a single folder
**os.mkdir("Data Science Projects")**### Create folders recursively
### The below line creates a folder "Documents" with a subfolder 
### inside it called "Data Science Projects" with a subfolder inside ### that one called "Project 1"
**os.makedirs("Documents/Data Science Projects/Project 1")**### Delete a file 
**os.remove("data.txt")** ### Delete a folder
**os.rmdir("Data Science Projects")** ### Delete directories recursively.
**os.removedirs("Documents/Data Science Projects/Project 1")** ### Rename a file or folder
**os.rename("My Documents", "Your Documents")**
```

# (2)轻松处理文件路径

不要再浪费构建文件路径的时间了

```
import os### Handling slashes / when creating file paths
file = "process.py"
folder = "Documents/project1"
**full_path = os.path.join(folder, file)**### Get the directory and file name from a full path
**file = os.path.basename(full_path)
folder = os.path.dirname(full_path)**### Check if a file or folder exists
**os.path.exists(full_path)**### Get the extension of a file 
**name, extension = os.path.splitext(file)**
```

# (3)列表的高级解包

您已经可以这样做了:

```
>>> a, b = range(2)
>>> a
0
>>> b
1
```

但是你知道你可以用 python 变得更聪明吗:

```
>>> a, b, *c = range(8)
>>> a
0
>>> b
1
>>> c
[2, 3, 4, 5, 6, 7]
```

实际上，您可以在列表中的任何地方这样做:

```
>>> a, *b, c = range(8)
>>> a
0
>>> b
[1, 2, 3, 4, 5, 6]
>>> c
7
```

# (4)与时间一起工作

不再需要硬编码日期和时间。并测量每个主要功能的运行时间。

```
import time
import datetime
import calendar### Get the current date and time
>>> datetime.datetime.now()
2018-11-01 20:17:12.964253### Get just the current time
>>> datetime.datetime.now().time()
2018-11-01 20:19:16.819745### Freeze the program for a set period of time
>>> time.sleep(secs=5)### Measure runtime of a python command
>>> start = time.time()
>>> 100 / 5
>>> end = time.time()
>>> print(end - start)
0.000005346
```

# (5)列表上的函数

使用列表时使事情变得更容易

```
### Sorting a list form low to high
list_1 = [7, 26, 34, 2, 12, 98, 56]
list_2 = sorted(list_1)### Getting the max, min, and sum of a list
list_sum = sum(list_1) 
max_val = max(list_1)
min_val = min(list_1)### Convert a string to a list where each element is a character
>>> list("bob")
["b", "o", "b"]### You can quickly reverse a list
>>> list_1.reverse()
[56, 98, 12, 2, 34, 26, 7]### Are any or all of the items in our list True?
>>> bool_list = [True, False, True, True]
>>> any(bool_list)
True
>>> all(bool_list)
False### You can loop through a list's values AND indices simultaneously
for index, value in enumerate(list_1):
    "do stuff"
```

# (6)巧用琴弦

我们在 StackOverflow 上查阅了无数次，所以我们就把它们列在这里吧！

```
### Check if a string contains a substring
sentence = "Hi I'm Bob!"
if "Bob" in sentence:
    print("YES")### Let's take care of those capital letters too, just in case the ### string for "bOb" looks a little bit different ...
if "bOb".lower() in sentence.lower():
    print("YES")### These two will convert a string to lower and upper case letters
sentence.lower()
sentence.upper()### Check the properties of your string
sentence.isalpha() # Alphabetic characters only (no symbols or nums)
sentence.isnumeric() # Numbers only
sentence.islower() # Is it all lower case?
sentence.isupper() # Is it all upper case?### Clean up your string
sentence.capitalize() # First char is capitalized
sentence.lstrip() # Remove whitespace on left side
sentence.rstrip() # Remove whitespace on right side
sentence.strip() # Remove whitespace on both sides### The .join() method can concatenate strings with a separator
### list --> string
>>> " ".join(["Bob","has","a","balloon"])
"Bob has a balloon"### The .split() method does the opposite of .join()### string --> list
>>> "Bob has a balloon".split(" ")
["Bob","has","a","balloon"]### The .replace() method can replace a substring with another
>>> "Bob has a balloon".replace("has", "is")
"Bob is a balloon"
```

# (7)一些鲜为人知的数学函数

当你想用你的数字做一些开箱即用的事情时，这些会派上用场

```
import math### Remainder
>>> math.fmod(12, 5)
2### Getting both the faction and integer parts
>>> math.modf(96.12)
(0.1200000000, 96.0)### Computes the Euclidean norm sqrt(x*x + y*y)
>>> hypot(2, 3)
3.6056
```

# 喜欢学习？

在 twitter 上关注我，我会在这里发布所有最新最棒的人工智能、技术和科学！也在 LinkedIn 上与我联系！