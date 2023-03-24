# 五个 jupyter 魔术命令加速您的工作流程

> 原文：<https://levelup.gitconnected.com/five-jupyter-magic-commands-to-speed-up-your-work-flow-dfaa5ade14ed>

![](img/be0d43fa837d8334f06a98f1f476c27c.png)

Artem Maltsev 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 jupyter 笔记本上工作是测试代码和检查结果的好方法。有时我发现当我的工作流分散到太多的单元时会变得杂乱无章。这里有 5 个 jupyter 魔术命令，帮助你坚持下去，做更多的事情。

## 1.`%%writefile`

如果我正在做一个项目，我希望代码单元以后变成 python 文件，我使用`%%writefile`单元魔法。`%%writefile`把你正在写的文件名作为它的参数。

```
%%writefile hello.py
def hello():
    print("Hello everybody!")Writing hello.py
```

如果以后我想添加那个文件，我只需要添加`-a`标志。

```
%%writefile -a hello.py
def hope():
    print('Hope you are having a great day.')
if __name__ == '__main__':
    hello()
    hope()Appending to hello.py
```

## 2.`%load`

如果我想加载我一直在处理的文件，我可以使用`%load`行魔术，它把文件名作为一个参数。

```
# %load hello.py
def hello():
    print("Hello everybody!")
def hope():
    print('Hope you are having a great day.')
if __name__ == '__main__':
    hello()
    hope()Hello everybody!
Hope you are having a great day.
```

在`%load`行魔术执行后，代码出现在单元格中。魔术行注释掉了，这样我就可以执行我的代码，而不需要魔术重新运行。我也可以只加载我想用`-r`标签使用的文件行。

```
# %load -r 3-4 hello.py
def hope():
    print('Hope you are having a great day.')
```

我最喜欢的功能是直接从 URL 加载。

```
# %load https://bit.ly/jupmagart
def hello():
    print("Hello everybody!")
def hope():
    print('Hope you are having a great day.')

if __name__ == '__main__':
    hello()
    hope()Hello everybody!
Hope you are having a great day.
```

## 3.`%alias`

`%alias`允许用户给一个新的魔法附加一个系统命令。

```
%alias sign_file echo "%l" >> hello.py
%sign_file "#Written for Medium by Michael Wayne Smith"
```

## 4.`%history`

`%history`打印您之前命令的历史记录。`-n`标志对输出进行编号，`-l`限制最后返回的行数。

```
%history -n -l 34:
# %load -r 3-4 hello.py
def hope():
    print('Hope you are having a great day.')
5:
# %load https://bit.ly/jupmagart
def hello():
    print("Hello everybody!")
def hope():
    print('Hope you are having a great day.')
if __name__ == '__main__':
    hello()
    hope()
6:
%alias sign_file echo "%l" >> hello.py
%sign_file "#Written for Medium by Michael Wayne Smith"
```

## 5.`%macro`

`%macro`允许用户定义宏。这在与 history 命令结合使用时尤其强大。您可以将历史记录的行号传递给宏。

```
%macro reprint_hello 5Macro `reprint_hello` created. To execute, type its name (without quotes).
=== Macro contents: ===
# %load https://bit.ly/jupmagart
def hello():
    print("Hello everybody!")
def hope():
    print('Hope you are having a great day.')
if __name__ == '__main__':
    hello()
    hope()
```

然后可以调用命名的宏。

```
reprint_helloHello everybody!
Hope you are having a great day.
```

感谢阅读！

你可以在这里找到更多关于 IPython magics [的信息。](https://ipython.readthedocs.io/en/stable/interactive/magics.html#)