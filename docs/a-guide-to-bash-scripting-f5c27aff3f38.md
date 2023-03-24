# Bash 脚本指南

> 原文：<https://levelup.gitconnected.com/a-guide-to-bash-scripting-f5c27aff3f38>

在 Linux 上开发时简化您的生活！

# 什么是 Bash？

Bash 是'**B**ourne**A**gain**SH**ell '的缩写。

根据维基百科，它是由 [Brian Fox](https://en.wikipedia.org/wiki/Brian_Fox_(computer_programmer)) 为 [GNU 项目](https://en.wikipedia.org/wiki/GNU_Project)编写的 Unix shell 和命令语言，作为 [Bourne shell](https://en.wikipedia.org/wiki/Bourne_shell) (sh)的免费软件替代。

# 什么是贝壳？

外壳是一种允许人类用户与计算机内核交互的计算机程序。

操作系统外壳可以是

*   命令行界面(Bash)或者，
*   图形用户界面(微软视窗 10)

# 内核是什么？

内核是位于计算机操作系统核心的计算机程序。

它充当计算机硬件和运行在其上的进程之间的桥梁。

# 什么是 Bash 脚本？

它是在使用 Bash 时编写代码片段以自动化重复任务的过程。

![](img/58ae97dfa8b4bc5f6ba15685841969b6.png)

照片由 [Sai Kiran Anagani](https://unsplash.com/es/@anagani_saikiran?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 如何开始？

1.  将 bash 脚本文件命名为。sh '扩展名(例如`script.sh`)
2.  以`#!/bin/bash`开始您的脚本文件

> 这将告诉您的计算机在运行脚本文件时要使用的解释器。

3.把你所有的脚本放在你的文件系统根目录下的“bin”目录中，比如`~/bin/`

4.将这个目录添加到配置文件中的`PATH`(Linux 上的`~/.bashrc`或 macOS 上的`~/.bash_profile`)

*   `PATH=~/bin:$PATH`

> 这使得可以从文件系统中的任何地方访问您的脚本文件。

5.使用以下命令使您的脚本文件可执行:

```
$ chmod +x script.sh
```

![](img/35e89e56f953c71e50b91ad23c25e5b0.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Bash 中的命令

## 打印/显示输出

要在屏幕上打印/显示，使用`echo`。

```
$ echo "Welcome"
```

## 定义变量

变量可以通过使用变量名后跟' = '和用引号(`"`)括住变量的内容(没有空格)来定义。

```
$ name="Ashish"$ number=0
```

## 访问变量

使用`$`符号可以访问变量。

```
$ echo $name
```

这将在屏幕上显示`Ashish`。

## 条件式

*   用`if`开始一个条件
*   将条件写在方括号内(`[ ]`)，在方括号和条件语句之间留出空间
*   如果条件为真，编写应该使用`then`关键字运行的代码
*   使用`else`编写条件为假时运行的代码
*   使用`fi`结束条件

```
if [ $number -eq 0 ]
then
  echo "Number is 0"
else
  echo "Number is not 0"
fi
```

![](img/0861839ab191734c7574f74bb10d5d21.png)

照片由[萨法尔·萨法罗夫](https://unsplash.com/@safarslife?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 比较运算符

您可以使用以下运算符来比较条件语句中的数字。

*   等于:`-eq`
*   不相等:`-ne`
*   小于:`-lt`
*   小于或等于:`-le`
*   大于:`-gt`
*   大于或等于:`-ge`
*   为空:`-z`
*   小于(在双括号内):`<`

```
(("$a" < "$b"))
```

*   小于或等于(在双括号内):`<=`

```
(("$a" <= "$b"))
```

*   大于(在双括号内):`>`

```
(("$a" > "$b"))
```

*   大于或等于(在双括号内):`>=`

```
(("$a" >= "$b"))
```

您可以使用以下运算符来比较条件语句中的字符串。

*   等于:`==`
*   不相等:`!=`
*   为空:`-z`
*   不为空:`-n`
*   小于，按 ASCII 字母顺序:`<`

```
if [[ "$a" < "$b" ]]if [ "$a" \< "$b" ]
```

*   大于，按 ASCII 字母顺序:`>`

```
if [[ "$a" > "$b" ]]if [ "$a" \> "$b" ]
```

# 环

## For 循环

要遍历城市列表，请使用以下语法。

```
for city in $cities
do
  echo $city
done
```

## While 循环

要打印小于 10 的数字，请使用以下语法。

```
number=0while [ $number -lt 10 ]
do
  echo $number
  index=$((number + 1))
done
```

`$((number +1))`将帮助在每个步骤中执行“数字”加 1。

## 直到循环

要打印数字直到它等于 4，请使用下面的语法。

```
number=1until [ $number -eq 4 ]
do
  echo $number
  index=$((number + 1))
done
```

![](img/80b0e666edb753f39c229544c7361d5c.png)

照片由[萨法尔·萨法罗夫](https://unsplash.com/@safarslife?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 接受输入

为了接受变量的输入，我们可以使用`read`关键字。

```
echo "Please enter your name"read nameecho "Your name is $name"
```

这个脚本将显示输入的名字。

## 创建别名

您可以使用 bash 脚本为重复代码创建别名。

可以使用配置文件将别名添加到环境中(Linux 上的`~/.bashrc`或 macOS 上的`~/.bash_profile`)。

例如，如果您正在使用 Kubernetes，并且您不想重复键入`kubectl` ，请使用以下语法在配置文件中创建一个别名。

```
alias k="kubectl"
```

现在，您将能够在 bash shell 中使用以下内容。

```
$ k get pods
```

![](img/9a46c8b85c65a2d5335438f567eb6b9d.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*感谢您阅读本文！*

*如果你是 Python 或编程的新手，可以看看我的新书，书名为“* [**”《没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book)**”***下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型的理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)