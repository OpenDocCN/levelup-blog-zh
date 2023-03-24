# Python RPG(第二部分)

> 原文：<https://levelup.gitconnected.com/python-rpg-part-2-561a5c317bc3>

## 如何使用面向对象编程在 Python 中创建自己的基于文本的 RPG！

![](img/af32e0fd90d3779123bcc537a706a961.png)

瑞安·昆塔尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你想从头开始编写游戏代码，这里有 [**第一部分**](https://medium.com/writers-blokke/python-rpg-game-part-1-f097ece7f476) 的链接。如果你想跳过教程直接看代码看，这里有 GitHub 上的 [**项目**](https://github.com/davidtc8/My_First_RPG_Game) ！

在本系列文章结束时，您将能够创建类似这样的东西:

![](img/057a6bc4f67c9b00648b39cb2c65edce.png)

大卫·托雷斯的 RPG 游戏

如果您对代码有任何改进或任何建议，请告诉我。玩得开心，享受这篇文章吧！

在下面的文章中，我将介绍:

*   敌人发电机
*   敌人攻击

> **第三步:敌人生成器**

## 敌人名称生成器

敌人生成器像 Python 中的其他函数一样工作。该函数将以**“level boss”**作为参数。这个布尔变量会让我们知道我们正在创造的敌人是一个 boss(真)还是一个共同的敌人(假)。

为了给我们的怪物起一个名字，我们有不同的文本文件:

*   形容词. txt
*   animal.txt

这些“txt”文件中有一组不同的单词，我们将通读这些文件，并为我们的怪物生成一个随机名称，例如:“**强壮的老鼠”**或“**臭鸟”。**这就是为什么我们有形容词和动物词汇。

为此，我们执行以下操作:

```
import random
with open('adjective.txt', 'r') as file:        
  lines = file.readlines()        
  adjective = lines[random.randint(0, len(lines)-1)][:-1]    
with open('animal.txt', 'r') as file2:        
  lines2 = file2.readlines()        
  animal = lines2[random.randint(0, len(lines)-1)][:-1]
```

**代码解释:**

主要目标是从形容词列表中随机选择一个形容词。由于 Python 操作列表的方式，我们的形容词将位于第一个形容词(0)和最后一个形容词(-1)之间。

```
adjective = lines[random.randint(0, len(lines)-1)][:-1]
```

前面一行是在说:**从我们所有的形容词中，随机选择一个，代码末尾的“[:-1]”是为了从文本中剔除每个字母末尾出现的'/n '。**

我们用 animal.txt 做同样的事情来生成一个随机的动物字母。

## 在老板和共同敌人之间做出选择

![](img/3d5686bfcc9e52921abdab725bb01df5.png)

照片由 [Kamil S](https://unsplash.com/@16bitspixelz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们有一个布尔值“levelBoss”。这个变量将告诉我们，如果 levelBoss = True，那么我们将创建一个具有给定属性的 Boss 敌人，如果 levelBoss = False，那么我们将创建一个共同的敌人。代码是这样工作的:

```
if levelBoss == False:        
  health = random.randint(50, 100)        
  attack = random.randint(5,10)        
  special = random.randint(10,20)        
  chance = random.randint(1,10)         # In here we're saying: call the class Enemy and give those   attributes        
  return Enemy(health, attack, special, chance, adjective + " " + animal) else:        
  health = random.randint(200, 250)        
  attack = random.randint(20, 40)        
  special = random.randint(50, 60)        
  chance = random.randint(1, 8)        
  superMove = random.randint(100,200)         
  # Return the Boss object and give it those attributes   
  return Boss(health, attack, special, chance, adjective + " " + animal, superMove)
```

代码“random.randint(x，y)”**表示它将在这些数字**中选择一个随机数，例如:

```
health = random.randint(50,100)
```

前面的代码意味着敌人的生命值将在 50 到 100 之间。在代码的最后，我们只是把敌人当前的生命值还给敌人。

> **敌方发电机代号:**

> **第四步:敌人进攻**

敌人攻击函数也是一个普通函数，它有 4 个参数:

*   命中机会
*   攻击值
*   名字
*   防御

由于我们在上一步中已经创建了一个怪物，hit_chance 中唯一缺少的参数是一个函数，我将在第 3 部分中介绍它；下面是 [**先睹为快的代码**](https://github.com/davidtc8/My_First_RPG_Game/blob/master/functions_for_the_game.py) 。

我希望你喜欢这个系列。在评论区让我知道你对它的看法！

学分:Youtube，高级 Python 文本冒险。

[(9)高级 Python 文本冒险—第 1 部分—类(A 级)— YouTube](https://www.youtube.com/watch?v=VxhZZHnig8U)