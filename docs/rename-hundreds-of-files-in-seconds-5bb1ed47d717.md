# 在几秒钟内重命名数百个文件

> 原文：<https://levelup.gitconnected.com/rename-hundreds-of-files-in-seconds-5bb1ed47d717>

> 不到十行代码！

![](img/3f49446ee470400f2db9ae754ef11981.png)

玛丽马克维奇拍摄的照片

# 问题

最近，我们从一家公司获得了一个巨大的投资组合，该公司向我们转让了数百份软拷贝文件。我注意到每个文件都标有客户的名字，并放入标有年份的文件夹中。

**📂2014 年**

*   📄Edwin.pdf
*   📄Chrissa.pdf
*   📄Timothy.pdf

对我的标准来说，这并不理想，还可以更好。客户端名称很有用，但是它不能告诉我文件的内容。

***有意义的文件名*** 和井井有条的文件夹结构使查找、跟踪文件变得更加容易，最终有助于提高组织效率。

我的计划是重命名文件，以包含更有意义的描述。

# **重命名文件或文件夹——正常方式**

通常，以下步骤用于重命名文件或文件夹。

*   右键单击项目并选择重命名，或者选择文件并按 F2。
*   键入新名称，然后按 Enter 键或单击重命名。

> 以这种方式命名一些文件是很容易的，但是我想重命名数百个文件！

# Python 食谱🐍

这就是 Python 的用武之地。

# **配料:**

我们将使用以下内容:

*   Python 3
*   操作系统库
*   重命名()函数
*   for 循环。

# **说明:**

## 步骤 1-导入库

导入 OS 库是必要的，因为它包含 rename()函数，我们将使用它来重命名我们的文件。

```
**import** os
```

## 步骤 2-创建变量

我创建了**文件夹**变量来存储文件的*绝对路径(也叫目录)*。这个变量用来告诉计算机在哪里可以找到这些文件。

```
folder = r'D:\Python\Rename_files\2014\\'
```

> **绝对路径**包含定位文件的完整目录。

这可以通过右击文件夹，选择 ***属性*** ，在 ***通用*** 页签中查找 ***位置*** 找到。这是文件夹的绝对路径。

*   d:\ Python \重命名文件\2014

## 步骤 For 循环

for 循环用于遍历文件夹目录中的每个文件，提取当前文件名，附加新描述，并重命名文件。

> Pro 提示:使用# comments
> # comments 用于在代码中添加注释&描述，以便我们知道代码在做什么。

```
**for** current_file_name **in** os.listdir(folder):

 *# Create the actual file directory*
    file_location = folder + current_file_name *# Construct new file name*
    new_file_name = "Contract_2014_" + current_file_name *# Renaming the file*
    os.rename(file_location, new_file_name)
```

# 运行代码，我们就完成了！

**📁2014 年**

*   📄Edwin.pdf→**合同 _2014_Edwin.pdf**
*   📄Chrissa.pdf→**合同 _2014_Chrissa.pdf**
*   📄Timothy.pdf→**合同 _2014_Timothy.pdf**

## 完整代码:

```
**import** osfolder = r'D:\Python\Rename_files\2014\\'**for** current_file_name **in** os.listdir(folder):

 *# Create actual file directory*
    file_location = folder + current_file_name *# Construct new file name*
    new_file_name = "Contract_2014_" + current_file_name *# Renaming the file*
    os.rename(file_location, new_file_name)
```

# **接下来是什么？**

谢谢你走到这一步！还有很多更有用的 Python 函数来处理文件，它们并不总是像本例中所示的那样简单。

# 跟我来。

我希望这篇文章对你有帮助！

我正在深入研究 Python、SQL、机器学习、AR、VR 和可视化，目标是确定最佳实践、提高效率并不断提升！

在这里整理我的想法/笔记，希望它能让媒体社区的每个人受益！

请关注我，这样我就知道我的故事正在帮助人们，如果您有任何问题，请随时与我联系！感激不尽！