# 如何创建一个半人马世界

> 原文：<https://levelup.gitconnected.com/how-to-create-a-wordle-as-a-centaur-6e934dda9536>

## *为什么你应该使用现代人工智能编程工具*

![](img/e3d554f9c3d799d5d3438febc82476ae.png)

我一直用 Wordle 作为严肃软件开发的类比。

在本文中，我将把创建良好模型的 TDD 解决方案与使用人工智能生成的自动化代码结合起来。

> *TL；DR:明智地使用最好的可用工具*

# 各个击破

# 分部

我写过几篇关于软件开发的文章。

我将谈论 Worlde 来代表严肃的软件。

在第一篇文章中，我使用 TDD 和 PHP 创建了一个后端 Wordle。

[](https://blog.devgenius.io/how-to-develop-a-wordle-game-using-tdd-in-25-minutes-2157c93dda9f) [## 如何在 25 分钟内用 TDD 开发一个 Wordle 游戏

### 使用 TDD 开发一个完整的 Wordle 游戏非常容易

blog.devgenius.io](https://blog.devgenius.io/how-to-develop-a-wordle-game-using-tdd-in-25-minutes-2157c93dda9f) 

接下来，我看了一个关于如何使用自动代码生成完全开发一个 Wordle 的视频。

[](/step-by-step-wordle-creation-with-codex-ai-fcf243212594) [## 一步一步用 Codex 人工智能创造世界

### 我抄写指令，用自然语言创造一个工作词

levelup.gitconnected.com](/step-by-step-wordle-creation-with-codex-ai-fcf243212594) 

因为 Wordle 是另一种形，我一直用 Javascript 用 TDD 练习它

[](/how-to-create-a-wordle-with-tdd-in-javascript-926d7947bd90) [## 如何在 Javascript 中用 TDD 创建一个 Wordle

### 我们不断练习这个惊人的形并学习。可以按照步骤来！

levelup.gitconnected.com](/how-to-create-a-wordle-with-tdd-in-javascript-926d7947bd90) 

现在我们有了一个了不起的 Wordle，它有一个很棒的领域模型和一个令人难以置信的机器学习用户界面。

让我们把它们结合起来。

# 征服

我们有两个存储库:

1 —用 TDD 制作 Javascript Wordle 的那个

[](https://replit.com/@mcsee/Wordle-TDD) [## Wordle - TDD - Node.js Repl

### 跳到内容有趣的在线多人点击器游戏！在新标签更新中打开:黑客升级！聊天！快速排行榜…

replit.com](https://replit.com/@mcsee/Wordle-TDD) [](https://github.com/mcsee/wordle/tree/main/How%20to%20Create%20a%20Wordle%20with%20TDD%20in%20Javascript) [## 如何在主 mcsee/wordle 上用 Javascript 创建一个带有 TDD 的 wordle

### 几个使用机器学习和 TDD 的 Wordle 实现

github.com](https://github.com/mcsee/wordle/tree/main/How%20to%20Create%20a%20Wordle%20with%20TDD%20in%20Javascript) 

2 —带有机器生成代码的那个

[](https://github.com/mcsee/wordle/tree/main/Open%20AI%20Codex%20from%20DotCSV) [## 在主 mcsee/wordle 从 DotCSV 打开 AI Codex

### 在主 mcsee/wordle 上使用机器学习和来自 DotCSV 的 TDD - wordle/Open AI Codex 的几个 Wordle 实现

github.com](https://github.com/mcsee/wordle/tree/main/Open%20AI%20Codex%20from%20DotCSV) 

有缺陷的可播放版本(实况)

 [## 用 Codex 生成的单词

### 用 Codex 生成的单词

用 Codexmcsee.github.io 生成的 Wordle](https://mcsee.github.io/wordle/DotCSV/index.html) 

# 让它一起工作

我们需要将更改注入到主文件中。

记住脚本化的 UI 版本不是模块化的。

我们在构建 UI 之前设置了有效的游戏

```
const response = await fetch("dictionary.txt");
 const dictionary = await response.text();
 const words = dictionary.split(/\r?\n/).map((string) => new Word(string)); var randomIndex = Math.floor(Math.random() * words.length);
 var winnerWord = words[randomIndex]; var game = new Game(words, winnerWord); // Before we setup our UI.
// We want to create our valid working Game
```

我们创建了一个文本字段，向最终用户显示状态/错误

```
// Step 14 bis
/* add an input text field under the table */var status = document.createElement('input');
status.setAttribute('type','text');
status.setAttribute('placeholder','');
status.id = 'status';
status.readOnly = true;
document.body.appendChild(status);
status.style.margin = '10px';
status.style.width = '300px';
```

这并不是绝对必要的，但它有助于保持 UI 尽可能简单。

```
// Step 17
/* create variable named 'rowindex' starting at 0 */var rowIndex = game.wordsAttempted().length;
```

rowIndex 变量不再是全局变量。我们根据游戏的尝试次数来计算。

我们将状态具体化到我们的游戏对象中

这是所有奇迹发生的时候。

我们用更健壮的算法取代了算法和错误剪枝字母计数计算

```
// Step 24/* when clicking validate button we add an attempt */document.getElementById('validate').addEventListener('click', function(event) {
  var cells = document.querySelectorAll('td');
  var currentLetters = '';
  for (var i = 0; i < cells.length; i++) {
    if (i >= rowIndex * 5 && i < (rowIndex + 1) * 5) {
        currentLetters += cells[i].innerHTML ;
    }
  }  
  var status = document.getElementById('status');
  status.value = '';
  try { 
    var attempt = new Word(currentLetters);
    game.addAttempt(attempt);  
  }
  catch (error) { 
    status.value = error.message; 
    return;
  }    var correctMatches = attempt.matchesPositionWith(winnerWord); 
  var incorrectMatches = attempt.matchesIncorrectPositionWith(winnerWord);   for (var i = rowIndex * 5; i < (rowIndex + 1) * 5; i++) { 
    if (correctMatches.includes(i-(rowIndex * 5)+1)) { 
        cells[i].style.backgroundColor = '#aedb95'; 
    }
    if (incorrectMatches.includes(i-(rowIndex * 5)+1)) { 
        cells[i].style.backgroundColor = '#edc953'; 
    }
  }
  if (game.hasWon()){
     status.value = 'Congratulations. You won!';
  }
  if (game.hasLost()){
     status.value = 'Sorry. You have lost! Correct word was ' + winnerWord.word();
  }
  document.getElementById('input').value = '';
  rowIndex = game.wordsAttempted().length;
});
```

我们把它作为一个很长的函数放在同一个事件中进行澄清。

该模型现在根据[快速失效原则](https://codeburst.io/fail-fast-3f3f036032b0)引发异常，我们可以将它们展示给最终用户。

这种方法需要在以后的文章中进行大量的重构。

最后，我们重置游戏。

这是第一版纠正的许多错误之一。

```
// Step 27/* when pressing remove, chose randomly the secret word from the words collection */ document.getElementById('remove').addEventListener('click', function(event) {
  var randomIndex = Math.floor(Math.random() * words.length);
  winnerWord = words[randomIndex];
  game = new Game(words, winnerWord);   
});
```

脚本的开头有[个重复的代码](https://blog.devgenius.io/code-smell-46-repeated-code-433d3feb516d)。

你可以在这里玩[的最终版本](https://mcsee.github.io/wordle/Centaur/)

源代码是[这里](https://mcsee.github.io/wordle/Centaur/)

还有一个工作回复[这里](https://replit.com/@mcsee/Centaur-TDD)

# 免责声明

最终的代码充满了重构的机会和几个[代码的味道](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)。

这是一个概念验证，而不是一个优雅的最终解决方案。

# 信用

图片来源:一个有趣的 [Twitter 帖子](https://twitter.com/Alt_Products_AI/status/1571835127101100033)要求人工智能画一个半人马