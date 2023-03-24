# 让我们用 Python 创建一个莎士比亚式的侮辱生成器

> 原文：<https://levelup.gitconnected.com/lets-create-a-shakespearean-insult-generator-in-python-e64909fd7e09>

“天堂真的知道你像地狱一样虚假”

![](img/fda39e47440db0d3c3e55179541baa21.png)

埃斯特·韦克斯勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

现在是周一晚上，这只能意味着一件事:是时候用 Python 写一个莎士比亚式的侮辱生成器了。

(我很爽。)

让我们面对现实吧。如果说唱之战在他那个时代存在的话，这位吟游诗人将会击败对手。从邪恶(“你是一个沸腾；一个瘟疫疮”([李尔王](https://www.nosweatshakespeare.com/resources/shakespeare-insults/)))，对轻蔑者(“你的脸不值得晒黑”([亨利五世](https://www.nosweatshakespeare.com/resources/shakespeare-insults/)))，对彻头彻尾的诽谤者(“恶棍，我干了你的母亲”([泰特斯·安德洛尼克斯](https://www.nosweatshakespeare.com/resources/shakespeare-insults/)))，莎士比亚的侮辱是无情的，毁灭性的，坦率地说，相当怪异。

在本文中，我们将设计并创建一个 Python 程序，它使用外部文本文件返回随机生成的莎士比亚侮辱。让我们开始吧。

# 第一步。找一些侮辱

这部分很简单。快速的谷歌搜索把我带到了一个很棒的网站“无汗莎士比亚”，它展示了一个方便的[莎士比亚侮辱生成器](https://464078-1464405-raikfcquaxqncofqfm.stackpathdns.com/wp-content/w3-webp/uploads/2011/07/shakespeare-insult-generator.jpgw3.webp)，我用它创建了下面的表格。

## 下面是我们用来创造一个随机的莎士比亚侮辱的基本格式:

**第 1 列**(形容词)+ **第 2 列**(更精细的形容词)+ **第 3 列**(名词)

为了使事情变得简单，我建议将这些列分成三个文本文档(每列一个。)

```
**COLUMN 1           COLUMN 2               COLUMN 3**Artless           base-court            apple-john 
Bawdy             bat-fowling           baggage
Beslubbering      beef-witted           barnacle
Bootless          beetle-headed         bladder
Churlish          boil-brained          boar-pig
Cockered          common-kissing        bugbear
Clouted           crook-pated           bum-bailey
Craven            dismal-dreaming       canker-blossom
Currish           dizzy-eyed            clack-dish
Dankish           dog-hearted           clotpole
Dissembling       dread-bolted          coxcomb
Droning           earth-vexing          death-token
Errant            elf-skinned           dewberry
Fawning           fat-kidneyed          flap-dragon
Fobbing           fen-sucked            flax-wench
Froward           flap-mouthed          flirt-gill
Frothy            fly-bitten            foot-licker
Gleeking          folly-fallen          fustilarian
Goatish           fool-born             giglet
Gorbellied        full-gorged           gudgeon
Impertinent       futs-griping          haggard
Infectious        half-faced            harpy
Jarring           hasty-witted          hedge-pig
Loggerheaded      hedge-born            horn-beast
Lumpish           hell-hated            hugger-mugger
Mammering         idle-headed           jolthead
Mangled           ill-nurtured          lewdster
Mewling           knotty-pated          lout
Paunchy           milk-livered          maggot-pie
Pribbling         motley-minded         malt-worm
Puking            onion-eyed            mammet
Puny              plume-plucked         measle
Qualling          pottle-deep           minnow
Rank              pox-marked            miscreant
Reeky             reeling-ripe          moldwarp
Roguish           rough-hewn            mumble-news
Ruttish           rude-growing          nut-hook
Saucy             rump-fed              pigeon-egg
Spleeny           shard-borne           pignut
Spongy            sheep-biting          puttock
Surly             spur-galled           pumpion
Tottering         swag-bellied          ratsbane
Unmuzzled         tardy-gaited          scut
Vain              tickle-brained        skainsmate
Venomed           toad-spotted          strumpet
Villainous        unchin-snouted        vartlot
Warped            weather-bitten        vassal
```

# 第二步。做一个提纲

首先，让我们弄清楚我们希望程序做什么。

这些常规任务将是我们的[功能](https://www.w3schools.com/python/python_functions.asp):

*   显示一个问候(我们称这个函数为‘show _ header’)
*   **从每个文档中返回一行**(让我们称这个函数为‘get _ limities’)
*   **格式化这些行以产生一个新的侮辱**(我们的‘main’函数将执行这个任务)

现在让我们进一步分解这些。这将是我们的[伪代码](https://towardsdatascience.com/pseudocode-101-an-introduction-to-writing-good-pseudocode-1331cb855be7):

```
#Import random module#Define global constants#Define main function #Initialize [sentinel value](https://www.pythonclassroom.com/loops/while-loop-sentinel) to 'y'
    #While sentinel value == 'y':
        #Call the show_header function
        #Call get_insults function 3x (one for each column)
        #Remove [newline character](https://www.freecodecamp.org/news/python-new-line-and-how-to-python-print-without-a-newline/) from each insult
        #[Concatenate](https://www.w3schools.com/python/gloss_python_string_concatenation.asp) insults and display result #See if user wants to get another insult ('y' for yes,
        #any other key for no). This will be the sentinel's new
        #value#Define get_insults function (takes a file as argument) #Create empty list
        #Open file for reading
        #Read through document, append each line to empty list
        #Close file
        #Find length of the list
        #Generate a random number between 0 and the length of the     
        #list - 1
        #Use this number to return a random item from our list#Define show_header function

        #Display greeting#Call the main function
```

# 第三步。导入随机模块并定义全局常数

可重用性和可读性在编码中很重要。这就是为什么如果您需要在整个程序中使用特定的值(并且这些值可能会改变)，那么将全局常量写入代码是一个好的做法。

出于我们的目的，我们在整个代码中使用了三个文本文件，所以让我们将这些文件名设为全局常量。如果我们将来想要使用不同的文件，这允许我们进行更改，而不必遍历每个函数并费力地编辑每个实例。

我们还需要使用 Python 内置的“随机”模块，所以让我们也导入它。

```
import randomFIRST_COLUMN = "Column1_file.txt"SECOND_COLUMN = "Column2_file.txt"THIRD_COLUMN = "Column3_file.txt"
```

# 第四步。编写函数

## 1.get _ limities 函数

因为我们想用这个函数来打开文件，所以让‘file’成为这个函数的一个参数。(见第 1 行)

第 2 行创建了一个空列表。这是我们通读文件时临时存储列表数据的地方。

第 3 行以[“读取”模式](https://www.w3schools.com/python/python_file_handling.asp)打开文件。但是如果文件打不开怎么办？如果文件被移动或重命名了怎么办？

```
1 def get_insults(file):
2         insult_list = []
3         openfile = open(file, 'r')
```

现在，如果打开文件有问题，我们的程序就会崩溃。

我们可以通过编写一个 try-except 语句来防止这种情况(第 3 行到第 6 行):

```
1 def get_insults(file):
2         insult_list = []
3         try:       
4                 openfile = open(file, 'r')
5         except IOError:
6                 print("Unable to open file.")
```

现在，如果文件无法打开(即，如果发生 IOError)，我们的程序将显示消息“无法打开文件”。

接下来，让我们开始处理我们的文件。第 7 行将变量“Line”赋给文件的第一行。这允许我们开始一个“while”循环，该循环一直重复，直到“line”是一个空字符串(这意味着我们已经到达了文档的末尾)。

```
 7         line = openfile.readline()
8         while line != '':
9                 insult_list.append(line)
10                line = openfile.readline()  
11        openfile.close()
12        num_items = len(insult_list)
13        index = random.randint(0, num_items - 1)
14        return insult_list[index]
```

在第 9 行，我们的程序将文件中的每一行追加到我们的临时列表中。一旦文档结束(即' line' = ' ')，我们就关闭文件。

太棒了。好的——现在让我们从文件中得到我们的短语。在第 12 行，我们找到了列表的长度。第 13 行生成了一个至少为 0 且小于列表长度的随机数。最后(我们快完成了，朋友们！)，在第 14 行，我们使用这个随机数索引我们的列表，并返回一个随机短语。

## 2.显示标题函数

这个很容易理解。(请不要评价缺乏艺术性——我尽力了。)

```
def header():16  print("----------------------------------------------")
17  print("Welcome to the Shakespearean Insult Generator!")
18  print("----------------------------------------------")
19  print() 
```

## 3.主要功能

*折断指关节*是时候把它们绑在一起了。

```
20 def main():21 **#Initialize** [**sentinel value**](https://www.pythonclassroom.com/loops/while-loop-sentinel) **to 'y'** 22       **#While sentinel value == 'y':**23       again = 'y'
24       while again.lower() == 'y':25 **#Call the show_header function** 26               show_header()27 **#Call get_insults function 3x (one for each column)** 28               first_column = get_insults(FIRST_COLUMN)
29               second_column = get_insults(SECOND_COLUMN)
30               third_column = get_insults(THIRD_COLUMN)31 **#Remove** [**newline character**](https://www.freecodecamp.org/news/python-new-line-and-how-to-python-print-without-a-newline/) **from each insult** 32               first_word = first_column.rstrip()
33               second_word = second_column.rstrip()
34                third_word = third_column.rstrip()35 **#**[**Concatenate**](https://www.w3schools.com/python/gloss_python_string_concatenation.asp) **insults and display result** 36               print("Here's my next random insult:")
37               print(f"{first_word} {second_word} {third_word}! \n")38               **#See if user wants to get another insult ('y' for** 39 **#yes, any other key for no). This will be the** 40 **#sentinel's new value**
41               print("Would you like to generate another insult?")
42               again = input("(Press 'y' for yes, or " +
43                               "any other key for no) ")
44     print("Thanks for using the Shakespearean Insult Generator!")
```

恭喜你，朋友。你刚刚用 Python 写了第一个(但可能不是最后一个)莎士比亚侮辱生成器。干得好——去喝杯咖啡吧。

*感谢* [*无汗莎士比亚*](https://www.nosweatshakespeare.com/resources/shakespeare-insults/) *(当然还有吟游诗人本人)。*