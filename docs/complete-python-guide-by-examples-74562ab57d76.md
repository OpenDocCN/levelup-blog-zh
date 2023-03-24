# 完整的 Python 示例指南

> 原文：<https://levelup.gitconnected.com/complete-python-guide-by-examples-74562ab57d76>

![](img/828d3fb38ceed382273367d6fb83a75b.png)

Alexander Schimmeck 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本教程中，您将通过简单的示例学习 Python 编程语言。这对数据科学、机器学习、人工智能和用 Django 进行 web 开发都有好处。

# **1。Python 基础知识**

## 1.1 概述

```
# Print output on the screen
print("Hello, world!")# output
"""
Hello, world!
"""
```

## 1.2 评论

```
# It's a single line comment in Python 
# The Comment doesn't executed in program
# It's for snippets
print("Hello, world!") # print Hello, world! without ""# multli line comment
"""
multi line comment
another line
"""
```

## 1.3 基本语法

```
# There are a lot of shapes of code 
name = "Muhammad Ibrahim"
print(name)
# number
num = 5
print(num)
# decimal
dec = 5.5
print(dec)
```

## 1.4 基本类型

```
# How to define strings ?
abbr = 'M'
print(abbr)
first_name = "Muhammad"
print(first_name)
last_name = 'Ibrahim'
print(last_name)# Numbers have two types
# Intger likes 20, 30 and 0 
int_num = 20
print(int_num)
float_num = 20.1
print(float_num)# Booleans have two types True or False
isTrue = True
print(isTrue)
isFalse = False
print(isFalse)
```

## 1.5 关键词

```
# When defining variables, you shouldn't use keywords
# like bool, int, float, or and or 
# special characters like $, =, +, - ...etc 
# If you use keywords, it will make error in writing code
```

## 1.6 获取用户的输入

```
# You should wait user to put input
first_name = input('Enter your first name ')
print(first_name)age = input('Enter your age')
print(age)# output
"""
Enter your first name Muhammad
Muhammad
Enter your age 30
30
"""
```

# 2.变量

## 2.1 数字

```
# Integer number
print(10)# Float-point number
print(22.198)# Convert float to int
print(int(1.5))# Convert int to float
print(float(2222))# Output
"""
10
22.198
1
2222.0
"""
```

## 2.2 算术运算

```
# Integer Division
print(21 // 10)   # 2# Power
y = 5 ** 2   # 5*5 = 25
print(y)
y = 5 ** 3   # 5 * 5 * 5 = 125
print(y)# Modulus
y = 21 % 10  # 1
print(y)# Output
"""
2
25
125
1
"""# Define variables
x = 6
y = 5# Addition
z = x + y  # 11
print(z)# Subtraction
z = x - y  # 1
print(z)# Mutliplication
z = x * y  # 30
print(z)# Division
z = x / y  # 1.2
print(z)
```

## 2.3 分配操作

```
# This called assignment operations
x = 5# Other cases
# Plus
x += 5  # means x = x + 5 = 5 + 5 = 10
print(x)# Minus
x -= 1 # x =  x - 1 = 10 - 1 = 9
print(x)# Multiply
x *= 2  # 9 * 2 = 18
print(x)# Division
x /= 2    # 18 / 2 = 9.0
print(x)# Output 
"""
10
9
18
9.0
"""
```

## 2.4 复数

```
# Complex number
z = 1-1j
print(z)  # (1+1j)# Real part
print(z.real)  # 1.0# Imaginary part
print(z.imag)  # -1.0# Conjugate
print(z.conjugate())  # (1+1j)# Another way to define complex
z = complex(1, 5)
print(z)# Get Data types
x = 2
print(type(x))
print(type(10.2))
print(type('a'))
print(type('Muhmmad'))# Output
"""
<class 'int'>
<class 'float'>
<class 'str'>
<class 'str'>
"""
```

# 3.用线串

## 3.1 字符串变量

```
# How to define string
# All the same output below 3
name = "Muhammad"
print(name)name = 'Muhammad'
print(name)name = """Muhammad"""
print(name)# Define character
logo = 'M'
logo = "M"# name and logo is same type is string
```

## 3.2 转义字符

```
# \n   will make new line in output
print("Hello,\nWorld!")
# Output
"""
Hello,
World!
"""# \t  tab usually = 4 spaces
print("Hello,\tWorld!")
# Output
"""
Hello, World!
"""# \' will make single '
print("isn\'t")
# Output
"""
isn't
"""# \" double quotes
print("\"as is\"")
# Output
"""
"as is"
"""# \\ backslash
print("/\\/\\")
# Output
"""
/\/\
"""
```

## 3.3 获取字符

```
name = "Muhammad"
print(name[0]) # print the first char in string is "M"
print(name[1]) # print "u"# return range Muh
print(name[0:3]) # range is 0 , 1 and 2  "Muh"print(name[2:-1:-1]) # range 2, 1, 0 is  "Muh"
```

## 3.4 格式化输出

```
print("i=%s" %(5) )
# Output i=5print("i={}".format(5))
# Output i=5print("i=%s, j=%s" %(5, 7) )
# Output i=5, j=7print("i={}, j={}".format(5, 7))
# Output i=5, j=7print("i={f}, j={s}".format(f=5, s=7))
# Output i=5, j=7print('i=', 5 )
# Output i= 5
```

## 3.5 字符串属性

```
letter = 'hello, my name is Muhammad'# Capitilize 
# Make first character upper case
cap_letter = letter.capitalize()
print(cap_letter)  # Hello, my name is muhammad# Upper case
up_letter = letter.upper()
print(up_letter)  # HELLO, MY NAME IS MUHAMMAD# Lower case
low_letter = letter.lower()
print(low_letter)  # hello, my name is muhammad# Find the index of the letter
# return -1 on failure
h_index = letter.find('h')
print(h_index)  # 0# Add search ranges if you need
# return -1 on failure
h_index = letter.find('h', 5)  # to End of the string
print(h_index)  # 20# Check if startswith 
# return True if yes . False if no
isStartWith = letter.startswith('h')
print(isStartWith)   # TrueisStartWith = letter.startswith('a')
print(isStartWith)   # False
```

# 4.条件表达式

## 4.1 If 语句

```
# If the condition in statment is true, the code in if block will be executed
# If not the code in if block won't be executed
x = 6
if x == 6 :
    print(x)# This called If statement 
# "x == 6" means if x = 6 
# When using if you should == 
# This operation is logical operation# The ouput of program is 6
# As the condition in if statement is true#if x == 6:
#    do_this# If the condition in statment is true, the code in if block will be executed
# If not the code in if block won't be executed
x = 6
if x == 6 :
    print(x)# This called If statement 
# "x == 6" means if x = 6 
# When using if you should == 
# This operation is logical operation# The ouput of program is 6
# As the condition in if statement is true#if x == 6:
#    do_this
```

## 4.2 比较运算符

```
x = 5# Equal
if x == 5:
    print('first if-block')# Greater than
if x > 5:
    print('second if-block')# Greater than or equal
if x >= 5:
    print('third if-block')# Less than
if x < 5:
    print('forth if-block')# Less than or equal
if x <= 5:
    print('fifth if-block')# Not Equal
if x != 5:
    print('sixth if-block')# Output
"""
first if-block
third if-block
fifth if-block
"""
```

## 4.3 逻辑运算符

```
x = 5
y = 7# And
# Check two conditions in statement if true go to block
# If not skip if block
if x == 5 and y == 7:
    print('first if-block')# Or
# Check if one condition is true go to if-block
# If two conditions if false skip if-block
if x > 5 or y == 7:
    print('second if-block')# Not
# If true make it false
# if false make it true
isFalse = False
if ~isFalse:
    print('third if-block')# Output
"""
first if-block
second if-block
third if-block
"""
```

## 4.4 如果…Elif …否则

```
first_name = "Muhammad"# If first condition is true run it
# If not go to else-block to run
if first_name != "Muhammad":
    print("Hello, world!")
    print("It's you")
else:
    print("Welcome")
    print("It's me")# Output
"""
Welcome
It's me
"""x = 3if x < 2:     # false 
    print('first_block')
elif x > 3:   # false
    print('second_block')
elif x == 3:  # true
    print('third_block')# Output
"""
third_block
"""x = 3if x < 2:     # false 
    print('first_block')
elif x > 3:   # false
    print('second_block')
else:  # this block should run if all last statements are flase
    print('third_block')# Output
"""
third_block
"""
```

## 4.5 嵌套 If

```
x = 6
y = 20
z = 0
u = 0if x == 6:
    u = 220
    if y >= 20:
        z = 3print(u)
print(z)# Output
"""
220
3
"""first_name = "Muhammad"
last_name = "Ismael"if first_name == "Muhammad":
    if last_name == "Ismael":
        print("Hello, Muhammad Ismael")
    else:
        print("Hello, Muhammad")
else:
    if last_name == "Islam":
        print("Hello,----- Islam")
    else:
        print("Hello, --------")# Output
"""
2
2
"""
```

## 4.6 如果带有字符串

```
name = "Muhammad"if 'M' in name:  # you can use "M" with double quote
    print('Yes')
if 'Muh' in name:
    print('Yes, too')
if 'f' in name:
    print('No')# Output
"""
Yes
Yes, too
"""
```

# 5.环

## 5.1 For 循环

```
for i in range(5):
    print(i) # for loop code block# Output
"""
0
1
2
3
4
"""x = 0
y = 0for i in range(5):
    # You can write any code here inside for
    # Each loop called iteration
    # total number of iterations = 5 from 0 to 4
    x = x + 1  # add last x to 1
    y = y + 2  # add last y to 2print(x)
print(y)
# Output
"""
5
10
"""# 0 is the start
# 10 is the end
# 2 is the step
# i.g. 0 -> 2 -> 4 -> 6 -> 8 -> (10 not)
for i in range(0, 10, 2):
    print(i)print('======================')
# 10 is the start
# 0 is the end
# -2 is the step
# i.g. 10 -> 8 -> 6 -> 4 -> 2 -> (10 not)
for i in range(10, 0, -2):
    print(i)# Output
"""
0
2
4
6
8
======================
10
8
6
4
2
"""
```

## 5.2 对于带字符串的循环

```
for v in "Muhammad":
    print(v)# Output
"""
M
u
h
a
m
m
a
d
"""
```

## 5.3 嵌套 For

```
x = 0
# for each iteration if upper for loop 
# second for loop make 5 iterations
# All iterations = 5 * 5 = 25
for i in range(5):
    # you can add code here    
    for j in range(5):
        x += 1
        # To trace what happen
        print("i=%s, j=%s, x=%s" %(i, j, x))
print(x)  # x = 25# output
"""
i=0, j=0, x=1
i=0, j=1, x=2
i=0, j=2, x=3
i=0, j=3, x=4
i=0, j=4, x=5
i=1, j=0, x=6
i=1, j=1, x=7
i=1, j=2, x=8
...
i=4, j=2, x=23
i=4, j=3, x=24
i=4, j=4, x=25
25
"""
```

## 5.4 While 循环

```
x = 0
while x <= 5:
    print(x)
    x += 1# Output
"""
0
1
2
3
4
5
"""
```

## 5.5 而…否则

```
i = 0
# When the condition is false run else-block
while i < 3:
    print(i)
    i += 1
else:
    print("The end")# Output
"""
0
1
2
The end
"""
```

## 5.6 中断声明

```
# break statement ends the loop for and while
for i in range(20):
    print(i)
    if i >= 3:
        break
# Output
"""
0
1
2
3
"""j = 0
while j <= 5:
    print(j)
    if j >= 3:
        break
    j += 1# Output
"""
0
1
2
3
"""
```

## 6.数据结构

## 6.1 列表

```
# The list is object has several values
l = [1, 2, 3, 6]
print(l) # [1, 2, 3, 6]
print(type(l))  # <class 'list'># Access the elements in the list
print(l[0]) #  the first element = 1
print(l[2]) # the third element = 3# The length of the list
print(l.__len__()) # Or
print(len(l))# Change value of list
l[0] = 3
print(l[0])  # 3
```

## 6.2 带有 For 循环的列表

```
myList = [1, 4, 6]
for item in myList:
    print(item)# Output
"""
1
4
6
"""
```

## 6.3 列表属性

```
l = [3, 5, 2]# Check an item
# Check an item in list print Yes
if 3 in l:
    print("Yes")
else:
    print("No")# Add an item
# Add an item in last of the list
l.append(7)
print(l)  # [3, 5, 2, 7]# Remove an item
l.remove(5)
print(l) # [3, 2, 7]# Copy the list
new_list = l.copy()
print(new_list)  # [3, 2, 7]# Clear the list
l.clear()
print(l)   # []# Concatenate lists
sec_list = [20, 23]
l.extend(sec_list)
print(l)  # [20, 23]   as the first list is emptyl = [3, 5, 2, 2, 6]# Check an item
# if count returns 0, the item isn't in the list
print(l.count(20))  # 0 as the list has no value = 20
print(l.count(3))   # 1  as the list has one value = 3
print(l.count(2))   # 2  as the list has two values = 3# Reverse the list
l.reverse()
print(l)  #  [6, 2, 2, 5, 3]# Sort the list
l.sort()
print(l)   # [2, 2, 3, 5, 6]# Insert an item with index
l.insert(3, 4)
print(l)    # [2, 2, 3, 4, 5, 6]# Remove the last item
last_item = l.pop()
print(last_item)    # 6 is the last item# Add items
l += [3, 6, 6]
print(l)   #  [2, 2, 3, 4, 5, 3, 6, 6]# Return all items in list 
print(l[:])   # [2, 2, 3, 4, 5, 3, 6, 6]# Return items with index
# 1, 2, 3   index 
print(l[1 : 4]) #   from the second element to forth
```

## 6.4 列表类型

```
# List of string
l = ["Muhammad", "Ismael"]
print(l)  # ['Muhammad', 'Ismael']# List of numbers
l = [23, 30.5, 34]
print(l)# List with different types
l = [20, "Muhammad", 34]
print(l)  # [20, 'Muhammad', 34]
```

## 6.5 元组

```
# It looks like list but can't change value of items
# It has several functions like list
t = (12, 23, 33)
print(t)  # (12, 23, 33)print(len(t))  # 3# Access items with index
print(t[0])  #  12
print(t[2])  #  33# Access items with reverse index
print(t[-1])   # 33
print(t[-3])   # 12# Error can't change value of tuples
#t[0] = 0
```

## 6.6 带 For 循环的元组

```
# The same examples in list
t = (1, "Muhammad", 6)
for item in t:
    print(item)# Output
"""
1
Muhammad
6
"""
```

## 6.7 元组属性

```
t = (3, 5, 2, 4, 5, 3)# Check an item
# Check an item in tuple print Yes
if 3 in t:
    print("Yes")
else:
    print("No")# Return number of elements in the tuple
print(t.count(3))   #  2
print(t.count(0))   #  0
```

# 7.功能

## 7.1 简单功能

```
# How to define functions in python
# Define function
def foo():
    print('Hello, world!')# Call function to run it
foo()# output
"""
Hello, world!
"""
```

## 7.2 功能返回

```
def foo():
    print('Hello,')
    return "Muhammad"my_name = foo()  # return 'Muhammad' to my_name
print(my_name)# Output
"""
Hello,
Muhammad
"""
```

## 7.3 带参数的函数

```
# Function can accept paramtersdef sum(x, y):
    z = x+y
    print(z)sum(5, 2) # it will print 7
sum(3, 40)  # it will print 43def sum_ret(x, y):
    return x+yresult = sum_ret(3,5)  # 8
print(result)result = sum_ret(20,30) #  50
print(result)# You can put name of params
result = sum_ret(x=33, y=77)
print(result)  # 110
```

## 7.4 具有默认值的函数

```
def print_name(first_name, last_name ="Ahmed"):
    print(first_name+ " " + last_name)# You can use it as the previous examples
print_name("Muhammad", "Islam")# Or
print_name(first_name="Islam")
print_name("Islam")  # The same as above
# Output
"""
Muhammad Islam
Islam Ahmed
Islam Ahmed
"""
```

## 7.5 递归函数

```
# Recursive is a function calls itself
# Get the factorial of numbers
# Ex n=5 : 5*4*3*2*1 = 120
def factorial(n):
    if n <= 1:
        return 1
    else:
        return n * factorial(n-1)result = factorial(5)
print(result)  # 120
```

## 7.6 任意参数

```
def print_sports(*args):
    print(args)# Call the function
# You can send params to the function or not 
# You can send more than 2 params
print_sports("Football", "Baseball") # ('Football', 'Baseball')
print_sports("Football")  # ('Football',)
print_sports()   #  ()def print_type_of_args(*args):
    print(type(args))print_type_of_args()   # <class 'tuple'>
```

## 7.7 已知参数

```
# You can put name of arguments
def print_fruits(**kwargs):
    print(kwargs)# Call the function
# You can send params to the function or not 
print_fruits(f1="Banana", f2="Apple", f3="Orange") 
# Output
# {'f1': 'Banana', 'f2': 'Apple', 'f3': 'Orange'}print_fruits(nameOfFruit="Banana")  # {'nameOfFruit': 'Banana'}
print_fruits()   #  {}def print_type_of_kwargs(**kwargs):
    print(type(kwargs))print_type_of_kwargs()   # <class 'dict'>
```

## 7.8λ表达式

```
# Lambda expression
# lambda parameter_list: expressionmultiply = lambda x: x*2
y = multiply(5)
print(y)   # 25cube = lambda x : x*x*x
print(cube(5))  # 125
```

## 7.9 当地范围

```
x = 5
def foo():
    # Error
    x = x + 1foo()# It will make error
# As x is defined in foo function only not all file
#print(x)def foo():
    x = 3
    print(x)foo()# It will make error
# As x is defined in foo function only not all file
#print(x)
```

## 7.10 全球范围

```
juice = 2def foo():
    global juice
    juice += 2
    print(juice)foo()  # output is 4
```

## 7.11 本地功能

```
def outer1():
    print('Outer1')

    # The output with no inner
    # It's a local function
    # You should call it
    def inner1():
        print('Inner1')def outer2():
    print('Outer2')
    def inner2():
        print('Inner2')
    inner2()# 
outer1()  # Output Outerouter2()
# Output 
"""
Outer1
Outer2
Inner2
"""
```