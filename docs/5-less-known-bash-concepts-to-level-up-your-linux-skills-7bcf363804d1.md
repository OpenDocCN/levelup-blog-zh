# 5 个鲜为人知的 Bash 概念来提升您的 Linux 技能

> 原文：<https://levelup.gitconnected.com/5-less-known-bash-concepts-to-level-up-your-linux-skills-7bcf363804d1>

![](img/3587b119155c07cb7cb7852ea649d405.png)

图像由[按下](https://www.freepik.com/author/pressfoto) [Freepik](https://www.freepik.com/free-photo/professional-programmer-working-late-dark-office_5698342.htm#query=programming&position=4&from_view=search&track=sph) 上的

# 1.Bash 数组

您必须熟悉 Python 列表和 JavaScript 数组，但是您知道 Bash 中也存在一维数组吗？

*   Bash 数组可以用方括号`()` ***中的元素来定义，而不会在*** `***=***` ***符号和方括号之间留下任何空格。***

```
array=('Ashish' 1 3 'b')
```

*   Bash 中的数组可以同时包含 ***数字、字符串*** 和 ***空值*** 值**。**
*   可以使用以下语法找到数组的长度:

```
array_length=${#array[@]}
```

用`echo $array_length`可以将`array_length`的值打印在屏幕上。这将输出到`4`。

> 注意方括号中的`@`或`*`符号指的是数组中所有*元素。*

*   *要打印数组中的所有值，我们可以使用以下语法:*

```
*echo ${array[*]}*
```

*或者，*

```
*echo ${array[@]}*
```

> *注意，与其他变量不同，数组是在`$`符号后用花括号`{}`引用的。*

*   *可以使用以下语法找到特定索引处的值:*

```
*echo ${array[0]}*
```

*以上将输出到`Ashish`。*

*   *新元素可以添加到数组中，如下所示:*

```
*array[5]='cat'*
```

*这会将字符串`cat`添加到数组中的索引`5`中。*

> *请注意，Bash 数组是由 **0 索引的**。*

*   *可以如下循环遍历数组中的元素索引和值:*

```
*for elem in ${!array[@]}; do
  echo "Element $elem is ${array[$elem]}"
done*
```

*![](img/21e43cebe14c2bb1514fcdf27b2b47b6.png)*

*图片由[在](https://www.freepik.com/author/pressfoto) [Freepik](https://www.freepik.com/free-photo/it-specialist-checking-code-computer-dark-office-night_5698336.htm#query=programmer&position=4&from_view=search&track=sph) 上按下*

# *2.Bash 哈希表/字典*

*   *要在 Bash 中创建由键值对组成的哈希表，可以使用以下语法:*

```
*declare -A myHashTable*
```

*   *键-值对可以添加到其中，如下所示:*

```
*myHashTable[a]='Ashish'

myHashTable[b]='Bamania'*
```

*   *要迭代键值对，可以使用以下语法:*

```
*for key in "${!myHashTable[@]}"
> do
>   echo -n "key: $key, "
>   echo "value: ${myHashTable[$key]}"
> done*
```

*这将输出到:*

```
*key: a, value: Ashish
key: b, value: Bamania*
```

*![](img/a6f3322b0376b09f95466b9b6b05ccac.png)*

*图像由[按下](https://www.freepik.com/author/pressfoto) [Freepik](https://www.freepik.com/free-photo/close-up-image-programer-working-his-desk-office_5698344.htm#query=programmer&position=19&from_view=search&track=sph) 上的*

# *3.抨击语录*

*Bash 从字面上解释 ***单引号*** 中包含的文本，单引号中的字符没有特殊含义。*

****双引号*** 用于像单引号一样括住文本，它允许 shell 解释:*

*   *美元符号(`$`)*
*   *反斜杠(`)*
*   *反斜杠(`\`)*
*   *感叹号(`!`)*

*例如，如果我们如下定义一个名为`variable`的变量，*

```
*variable=123*
```

*`echo "$variable"`将输出到`123`但是，*

*`echo '$variable'`将输出到`$variable`。*

# *4.Bash Case 语句*

*类似于 JavaScript 中的`switch-case`语句，Bash 也提供了围绕`case`语句的工具。*

*人们可以如下使用它:*

```
*case expression in
    pattern1)
       statement 
    ;;
    pattern2)
        statement
    ;;
    *)
    pattern3)
      default statement
    ;;
esac*
```

*例如:*

*让我们把一个叫做`pet`的变量定义为`pet='cat'`。*

```
*case $pet in 
    cat) 
      echo -n “Meow!”
    ;; 
    dog) 
      echo -n “Woof!” 
    ;; 
    *) 
      echo -n “No sound found”
    ;; 
esac*
```

*这将输出到`Meow!`。*

*![](img/433f6925d424555a52321ee349f6f915.png)*

*图像由[在](https://www.freepik.com/author/pressfoto) [Freepik](https://www.freepik.com/free-photo/professional-programmer-working-late-dark-office_5698342.htm#query=programming&position=4&from_view=search&track=sph) 上按 foto*

# *5.Bash 函数*

*Bash 中的函数可以定义为:*

```
*function_name () {
  commands
}*
```

*或者，*

```
*function function_name {
  commands
}*
```

> *请注意，大括号必须用空格与正文分开。*

*要调用一个函数，只需写出函数名。*

*让我们定义一个叫做`print_hello`的函数。*

```
*function print_hello {
  echo 'Hello!'
}*
```

*该函数可以按如下方式调用:*

```
*print_hello*
```

*这输出到`Hello!`。*

**本文到此为止！**

*非常感谢你的阅读！*

**如果你是 Python 或编程的新手，可以看看我的新书《没有公牛**t 的学习 Python 指南**’***下面:***

*[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)*