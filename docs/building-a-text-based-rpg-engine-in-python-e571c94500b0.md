# 用 Python 构建基于文本的 RPG 引擎

> 原文：<https://levelup.gitconnected.com/building-a-text-based-rpg-engine-in-python-e571c94500b0>

![](img/add47a16f3aad58f338b2197a41a3b19.png)

来源:[巨蟒](https://www.youtube.com/watch?v=EJdzuwSF-Fc)

【T2 篇】可用波兰语 [*此处*](https://bulldogjob.pl/news/1209-budujemy-silnik-gry-rpg-w-pythonie?utm_source=linkedin&utm_medium=organic&utm_campaign=Blog) *。*

我知道你在想什么…什么…为什么会有人想这么做？我的一个正在学习 Python 的朋友提到他想开发一个基于文本的角色扮演游戏，这让我开始思考。自从我尝试构建上一个基于文本的“游戏”monty.py 以来，已经过去了七年，它只是一个简单的 Monty Python 短剧。这就是为什么我再次尝试用 python 创建一个基于文本的角色扮演游戏引擎。

## 定义我们的输入和输出

首先，我们需要设定我们的期望。我认为在最简单的形式下，游戏需要做以下事情:

1.  加载包含游戏信息的文件
2.  显示游戏的文本输出
3.  接受玩家的输入

第一点很简单。我们需要一些包含数据的文件。我们会用泡菜来包装我们的物品。每个元素的形状将更多地由接下来的两个要求决定。

我们希望将输出和输入组合在一起。我们将把它们放在我们称之为页面的东西上。每一页都有一个号码，这样我们就可以跟踪他们。以下是页面的外观:

```
1: {
  'text': 
  'options':
}
```

我们不希望只有大块的文本，所以“文本”应该是一个带有字符串的列表元素。这样，在输入一个输入之前，你必须点击通过多行对话。此外，我真的很喜欢它，当它看起来像是文本被输入到屏幕上，所以我们将稍后实现该功能。

```
'text': [
   "This is out first line",
   "And this is our second"
]
```

接下来，对于输入。每个 input 语句应该有一行文本，然后还有一个指示符，表示当这个输入被选中时，将转到这个页面。因为这些数据是明确定义的，所以我们可以使用元组数组来存储这些数据，如下所示:

```
'options': [
    ("Option 1", 2)
    ("Option 2", 3)
]
```

一旦我们以适当的格式编写了我们的故事，我们可以使用如下的脚本将它包装成二进制 pickle 文件，该文件可以从我们的引擎中分发和读取。

```
import picklestory = {
  1: {
    'Text': [
        "Hello there..",
        "I bet you werent exepecting to hear from me so soon...",
        "...you seem a little confused do you know who I am?"
    ],
    'Options': [
        ("Yeah of course!", 2),
        ("I'm sorry I dont", 3)
    ]
  }
}with open('chapter1.ch', 'wb') as chapter:
    pickle.dump(story, chapter)
```

## 输出文本

我们要做的第一件事是弄清楚如何将文本缓慢地输出到屏幕上，就像有人在打字一样。用[这个](https://stackoverflow.com/questions/4099422/printing-slowly-simulate-typing)奇妙的解决方案来拯救堆栈溢出。它遍历每个字母，并将其放在输出端。sys.stdout 调用提供对命令提示符的低级访问，允许您覆盖 Python 设置的默认值。

```
import sys,time,random

def slow_type(t):
    typing_speed = 100 #wpm
    for l in t:
        sys.stdout.write(l)
        sys.stdout.flush()
        time.sleep(random.random()*10.0/typing_speed)
```

好了，现在我们已经解决了慢速打字的问题，我们可以开始真正的工作了。我们说过，我们希望一次打印一页中的每一行文本。当你点击回车键时，它应该移动到下一个输出。下面是这个函数的样子。

它从页面的文本部分获取行列表；然后，它遍历所有的行，缓慢地输入一行，然后等待按下回车键，再移动到下一行。

```
def display_page_text(lines: list):  
    for line in lines:
       slow_type(line) # Make the user press enter to see the next line 
       get_input([''])
```

但是等一下！我知道我只是使用了`get_input()`而没有展示这个函数是什么样子的。接下来，让我们看看如何获取输入值。

## **接受输入**

从命令行获得输入并不太糟糕。首先，我们将创建一个函数，它的唯一目的是接受输入。我们将传递给它一个有效输入字符串的列表。然后我们将接受用户输入。如果他们的输入没有列在有效输入列表中，我们会通知用户，告诉他们什么是有效输入，然后清除输入。否则，我们返回输入。

```
def get_input(valid_input: list):  
    while True:    
        user_entered = input()            if user_entered not in valid_input:      
            print("Invalid input. Please use one \
                   of the following inputs:\n")
            print(valid_input)      
            user_entered = None            else:
            return user_entered
```

如果我们需要回车键，我们可以像前面提到的那样用['']调用 get inputs。否则，获取输入的主要用途是决定下一步转到哪个页面。这是 get a response 函数的工作。向它传递表示选项的元组列表。它们有一个字符串选项，然后是页码，如下所示:(“选项 1”，2)。

Get response 对元组进行迭代，为选项和选项文本打印一个数字。然后，将有效输入(选项的索引)传入 get input 函数以获取用户输入。最后，它返回下一个页码。

```
def get_response(options: list):
    for index, option in enumerate(options): 
        print(index + “. “ + option[0]) 

    valid_inputs = [str(num) for num in range(len(options))]
    option_index = int(get_input(valid_inputs))

    return options[option_index][1]
```

## 振作起来

我们需要做的最后一件事就是把它都拉在一起。当你加载应用程序时，我们需要加载第一页。然后我们建立我们的程序循环。为了退出循环，我们将页面设置为 none。

然后，我们将从 story dict 中获取当前页面。如果没有页面，那么我们需要跳出这个循环。这样，如果没有提到索引的页面，程序将优雅地退出。

获取页面后，我们将使用之前定义的函数显示页面文本。一旦他们完成了页面文本，我们将获得他们输入的下一个页面。如果列表中没有任何选项，那么我们会说故事结束了，我们可以关闭这个故事了。如果有选项，我们将使用 get response 函数来获得它们的选择。

```
def story_flow(story: dict):  
    curr_page = 1   
    while curr_page != None:    
        page = story.get(curr_page, None)
        if page == None:
            curr_page = None
            break

        display_page_text(page['Text'])

        if len(page['Options']) == 0:      
            curr_page = None      
            break     

        curr_page = get_response(page['Options'])
```

最后，我们将让脚本加载 pickle 文件来播放这个故事。这是如何做到的。

```
import pickle
if __name__ == "__main__": story= {} with open('chapter1.ch', 'rb') as file:
        story = pickle.load(file) story_flow(story) 
```

就是这样！查看 [Github repo](https://github.com/dtaivpp/text_rpg_engine) 看看一个完整的工作示例。还有，恭喜你！如果您遵循了这个示例，那么您已经实现了一个地图数据结构！