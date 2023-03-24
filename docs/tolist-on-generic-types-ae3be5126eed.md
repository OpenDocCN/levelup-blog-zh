# 泛型类型上的 ToString()

> 原文：<https://levelup.gitconnected.com/tolist-on-generic-types-ae3be5126eed>

![](img/4ebd5c8deb5fc67c25f7648fe3817032.png)

照片由 [David Ballew](https://unsplash.com/@daveballew) 在 [Unsplash](https://unsplash.com/) 上拍摄

如果您调用以下代码:

`new List<string>().ToString();`

您将获得以下输出:

`System.Collections.Generic.List`1[System.String]`

当我第一次看到这个的时候，我很困惑为什么它不返回:

`System.Collections.Generic.List<System.String>`

之所以返回这种方括号样式的输出，是因为 C#不是唯一的。网语。

一大堆不同风格的语言都可以调用`ToString()`方法，并且一个方法的行为不应该根据调用该方法的语言而改变。

因此，使用 CLR 语法。

以下内容直接摘自 [ECMA-335](http://www.ecma-international.org/publications/standards/Ecma-335.htm) CLI 规范(参见第 I.10.7.2 节类型名和 arity 编码)，描述了 CLR 使用的语法:

符合 CLS 标准的泛型类型名使用格式`*name[`arity]*` 编码，其中`*[…]*`表示重音符```和 arity 一起是可选的。编码名称应遵循以下规则:

*   名称应为不包含```字符的 ID(见第二部分)。
*   arity 被指定为不带前导零或空格的无符号十进制数。
*   对于普通泛型类型，arity 是在该类型上声明的类型参数的数量。
*   对于嵌套泛型类型，arity 是新引入的类型参数的数量。