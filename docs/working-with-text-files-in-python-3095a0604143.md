# 使用 Python 处理文本文件变得简单！

> 原文：<https://levelup.gitconnected.com/working-with-text-files-in-python-3095a0604143>

处理文本文件可能会令人沮丧。

用 Python 让它变得简单吧！

![](img/36d41c12cc43c008c4fa6ebc4274e1b5.png)

照片由[纳迪·博罗迪纳](https://unsplash.com/@borodinanadi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 用 Python 读取文本文件

让我们在我们的目录中创建一个文本文件`info.txt`,并将以下信息保存在其中。

```
$ echo "Hello" > info.txt
```

确认该文本文件的内容:

```
$ cat info.txt//Output : Hello
```

让我们开始处理这个文本文件。

要读取单行/多行文本文件，我们使用下面的语法和`.read()`方法。

```
**with open**("info.txt", "**r**") **as** info_txt:
    info_text_content = info_txt.**read**() print(info_text_content)#Prints: Hello
```

这就创建了一个**上下文管理器**，它打开一个文件，执行打印操作，然后在执行完该操作后关闭该文件。

![](img/5bcfee1ad4e6b8d143a3d63aa48e28fa.png)

[Artturi Jalli](https://unsplash.com/@artturijalli?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 读取多行文本文件

假设我们有一个名为`paragraph.txt`的文本文件，有多行。

```
I wandered lonely as a cloud
That floats on high o’er vales and hills,
When all at once I saw a crowd,
A host, of golden daffodils;
Beside the lake, beneath the trees,
Fluttering and dancing in the breeze …
```

## 逐行阅读

要逐行读取这个文件，我们可以使用下面的语法和`.readline()`方法。

```
**with open**("paragraph.txt", **"r"**) **as** para_txt:
    line_1 = para_txt.**readline**() print(line_1) #Prints: I wandered lonely as a cloud line_2 = para_txt.**readline**()    print(line_2) #Prints: That floats on high o’er vales and hills,
```

## 一起读所有的行

为了一起阅读所有的行，我们使用下面的语法和`.readlines()`方法。

```
**with open**("paragraph.txt", **"r"**) **as** para_txt:
    for line in para_txt.readlines():
        print(line)
```

`para_txt.readlines()`生成一个可以迭代的行列表，以打印所有的行。

![](img/9ff37d647eb689fba831ef1907e9a88f.png)

照片由[伊斯哈格·罗宾](https://unsplash.com/@esmiloenak?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 写入文本文件

为了创建并写入一个名为`new_file.txt`的新文本文件，其内容为`Hello`，下面的语法可以与`.write()`方法一起使用。

```
**with open**("new_file.txt", "**w**") **as** new_file:
  new_file.write("Hello")
```

打开该文件时，它将如下所示:

```
Hello
```

![](img/10dd495e20a400d99de0aad1f23eb491.png)

照片由[凯瑟琳·拉威利](https://unsplash.com/@cathrynlavery?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 向文本文件添加数据

为了将文本添加到现有的文本文件中，下面的语法可以和`.write()`方法一起使用。

```
**with open**("new_file.txt", "**a**") **as** new_file:
  new_file.write("Ashish")
```

这将在新的一行中向文件内容追加/添加`Ashish`。

打开文本文件时，它将如下所示:

```
Hello
Ashish
```

![](img/8561af427095383c9b337f1152c7f5c9.png)

由 [Fernando Hernandez](https://unsplash.com/@_ferh97?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

希望这能让你处理文本文件更简单！

*感谢您阅读本文！*

*如果你是 Python 或编程的新手，可以看看我的新书《没有公牛**t 学习 Python 指南**’***下面:**

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)