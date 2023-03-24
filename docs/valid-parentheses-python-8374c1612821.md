# 解决方案:有效括号(Python)

> 原文：<https://levelup.gitconnected.com/valid-parentheses-python-8374c1612821>

![](img/b9ae9cf2d71537510cdd46356b2f3db5.png)

给定一个仅包含字符`'('`、`')'`、`'{'`、`'['`和`']'`的字符串`s`，确定输入的字符串是否有效。

在以下情况下，输入字符串有效:

1.  左括号必须用相同类型的括号括起来。
2.  左括号必须以正确的顺序结束。

来看看:[https://leetcode.com/problems/valid-parentheses/](https://leetcode.com/problems/valid-parentheses/)

**例 1:**

```
**Input:** s = "()"
**Output:** true
```

**例 2:**

```
**Input:** s = "()[]{}"
**Output:** true
```

**例 3:**

```
**Input:** s = "(]"
**Output:** false
```

**例 4:**

```
**Input:** s = "([)]"
**Output:** false
```

例 5:

```
**Input:** s = "{[]}"
**Output:** true
```

**约束:**

*   `1 <= s.length <= 104`
*   `s`仅由括号组成`'()[]{}'`

**解决方案:**

```
 class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2 != 0:
            return False
        dict = {'(' : ')', '[' : ']', '{' : '}'}
        stack = []
        for iin s:
            if i in dict.keys():
                stack.append(i)
            else:
                if stack == []:
                    return False
                a = stack.pop()
                if i!= dict[a]:
                    return False
        return stack == [] 
```

**解释:**

这里我们使用堆栈验证和字典！我们需要一个堆栈来存储最后一个有效的左括号，左括号也作为键，它们的值就是右括号。所以如果我们把左括号存储在堆栈中，如果我们找到一个有效的右括号，就把它从堆栈中取出来。想想' {[]} '的情况，就不难搞清楚了，我们需要一个**栈**来存储括号最后一个有效的左边部分，当下一个 char 是有效的右边部分时，弹出左边部分。

另一个验证是检查字符串的长度是否是偶数，如果是奇数，那么显然不是有效的括号(或)平衡括号！所以一个有效的括号串的长度应该总是偶数，我们可以在开头加一个检查。最糟糕的情况是当有人以“((((((((()”等形式开始输入时。

[GitHub](https://github.com/ritchiepulikottil)

[领英](https://www.linkedin.com/in/ritchie-pulikottil-6876341aa)

[推特](https://twitter.com/itsritchie1005)

[Instagram](https://instagram.com/ritchiepulikottil)